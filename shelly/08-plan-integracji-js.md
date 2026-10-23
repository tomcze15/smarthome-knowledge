# Plan integracji z JS/TS — warstwa wspólna

Kontekst: `~/smarthome/core` — Node + TypeScript (CommonJS, `ts-node`), zależność `mqtt@^5`,
Mosquitto już postawiony (`core/mosquitto`, `core/mosquitto-data`).

Ponieważ Sonoff i Shelly mówią praktycznie tym samym protokołem
([07-porownanie-sonoff.md](07-porownanie-sonoff.md)), **nie budować dwóch osobnych
integracji.** Jedna warstwa RPC + cienki adapter per producent.

---

## Krok 1 — rozpoznanie urządzenia

Shelly ma tu przewagę: `/shelly` działa bez autoryzacji na **obu** generacjach.

```bash
export SHELLY=<ip>
curl http://${SHELLY}/shelly
```

Pole **`gen`** rozstrzyga, czy to Gen1 (REST + CoIoT) czy Gen2+ (RPC).

Dla Gen2+ pełne rozpoznanie plus lista możliwości:

```bash
curl -X POST -d '{"id":1,"method":"Shelly.GetDeviceInfo"}' http://${SHELLY}/rpc
curl -X POST -d '{"id":2,"method":"Shelly.ListMethods"}' http://${SHELLY}/rpc
```

🟢 **`Shelly.ListMethods` uwzględnia ACL i autoryzację** — pokazuje metody dozwolone dla
tego konkretnego kanału. Odpowiednika u Sonoffa nie ma, tam trzeba strzelać metodami
i łapać `-12803`.

Discovery w sieci: mDNS `_shelly._tcp`, albo `_http._tcp` z filtrem po TXT `gen=2`/`gen=3`.

---

## Krok 2 — kształt warstwy wspólnej

```
src/
├─ rpc/
│  ├─ frame.types.d.ts       ← ramki JSON-RPC (wspólne)
│  ├─ ws-channel.ts          ← WebSocket, reconnect, korelacja id→promise
│  ├─ mqtt-channel.ts        ← MQTT, korelacja przez <src>/rpc
│  └─ digest.ts              ← podpis, z hakiem na ha2 per producent
├─ vendors/
│  ├─ shelly.adapter.ts
│  └─ sonoff.adapter.ts
└─ model/
   └─ measurement.types.d.ts ← znormalizowany odczyt
```

Adapter odpowiada dokładnie za pięć rzeczy, w których producenci się różnią:

1. **`ha2`** — Shelly: stałe `SHA256("dummy_method:dummy_uri")`; Sonoff: `SHA256("NS:Metoda")`
2. **typ `nc`** — Shelly: string `"00000001"`; Sonoff: number `1`
3. **indeks bazowy** — Shelly 0, Sonoff 1
4. **skalowanie** — Shelly ×1, Sonoff ÷100
5. **mapowanie kodów błędów** na wspólny enum

```ts
export interface VendorAdapter {
  readonly name: 'shelly' | 'sonoff';
  readonly firstComponentIndex: 0 | 1;
  computeHa2(method: string): string;
  formatNonceCount(counter: number): string | number;
  normalizeMeasurement(raw: Record<string, number>): Measurement;
  mapErrorCode(code: number): RpcErrorKind;
}
```

### Znormalizowany odczyt

```ts
export interface Measurement {
  timestamp: number;
  voltageVolts: number;
  currentAmperes: number;
  activePowerWatts: number;
  frequencyHertz: number;
  totalEnergyWattHours: number;
  returnedEnergyWattHours: number;
}
```

Shelly daje to wprost (`voltage`, `current`, `apower`, `freq`, `aenergy.total`,
`ret_aenergy.total`). Sonoff wymaga dzielenia przez 100 i przeliczenia `total_energy`
z setnych kWh na Wh (`× 10`).

⚠️ Uwaga na `ret_aenergy` u Shelly: energia zwrócona jest **także** wliczona do `aenergy`.
Nie sumować obu, żeby nie policzyć podwójnie.

---

## Krok 3 — digest, z uwzględnieniem obu wariantów

```ts
import { createHash } from 'node:crypto';

const sha256 = (value: string) => createHash('sha256').update(value).digest('hex');

export const shellyAdapter: Pick<VendorAdapter, 'computeHa2' | 'formatNonceCount'> = {
  computeHa2: () => sha256('dummy_method:dummy_uri'),
  formatNonceCount: (counter) => counter.toString(16).padStart(8, '0'),
};

export const sonoffAdapter: Pick<VendorAdapter, 'computeHa2' | 'formatNonceCount'> = {
  computeHa2: (method) => {
    const [namespace, procedure] = method.split('.');
    return sha256(`${namespace}:${procedure}`);
  },
  formatNonceCount: (counter) => counter,
};

export function buildAuth(
  adapter: VendorAdapter,
  method: string,
  realm: string,
  nonce: string,
  password: string,
  counter: number,
) {
  const clientNonce = Math.floor(Date.now() / 1000);
  const nonceCount = adapter.formatNonceCount(counter);
  const ha1 = sha256(`admin:${realm}:${password}`);
  const ha2 = adapter.computeHa2(method);

  return {
    realm,
    username: 'admin',
    nonce,
    nc: nonceCount,
    cnonce: clientNonce,
    response: sha256(`${ha1}:${nonce}:${nonceCount}:${clientNonce}:auth:${ha2}`),
    algorithm: 'SHA-256',
  };
}
```

🟢 **Na Shelly z firmware 2.0.0+ warto wykorzystać ponowne użycie nonce'a:** pełny challenge
raz, potem inkrementacja `counter` przy każdym żądaniu — do 30 000 razy albo przez godzinę.
Trzeba tylko obsłużyć 401 z **`stale: true`** jako sygnał „weź nowy nonce, hasło jest dobre".

🔴 Na Shelly **nie retry'ować agresywnie po błędzie**: po 10 nieudanych próbach w oknie
10 minut wchodzi progresywny backoff, a urządzenie zwraca **429** zamiast 401.
Kilka pomyłek w podpisie potrafi zablokować na 5 minut.

---

## Krok 4 — architektura zbierania danych

Ta sama co przy Sonoffie: **urządzenia → Mosquitto → konsumenci**.

```
Shelly Gen2+  ──MQTT──┐
                       ├──▶ Mosquitto ──┬──▶ Home Assistant
Sonoff BASIC-1GSP ─────┘                └──▶ kolektor w Node (core)
```

MQTT u obu producentów **nie wymaga autoryzacji digest** — cały ból z podpisami dotyczy
tylko jednorazowej konfiguracji.

Różnica w procedurze konfiguracji:

| | Shelly | Sonoff |
|---|---|---|
| Ustawienie | `Mqtt.SetConfig` | `MQTT.SetConfig` |
| Wejście w życie | **wymaga `Shelly.Reboot`** | od ręki |
| Autodiscovery HA | brak flagi; skrypt `mqtt-discovery.js` albo natywna integracja HA | `enable_ha: true` |

⚠️ Dla Shelly **rozważ, czy w ogóle iść przez MQTT.** Natywna integracja Shelly w Home
Assistancie (WebSocket) jest dojrzała i podpięcie tego samego urządzenia dodatkowo przez
MQTT może się z nią gryźć [DEDUKCJA — sprawdzić]. Wariant do rozważenia: Shelly do HA
natywnie, a kolektor w Node ciągnie z HA albo osobnym WebSocketem.

---

## Krok 5 — co zejść na urządzenie, a co trzymać na serwerze

Shelly pozwala na coś, czego Sonoff nie potrafi — **JavaScript na urządzeniu**
([05-skrypty-on-device.md](05-skrypty-on-device.md)).

[DEDUKCJA] Sensowny podział:

| Na urządzeniu (skrypt) | Na serwerze |
|---|---|
| Twarde progi bezpieczeństwa | Zbieranie i archiwizacja danych |
| Zrzucanie obciążenia | Wizualizacja, raporty |
| Reakcja na awarię odbiornika | Korelacja między urządzeniami |
| Logika, która ma działać gdy padnie serwer | Logika wymagająca stanu globalnego |

Gotowce warte podpatrzenia: `load-shedding.js`, `failure-monitor.js`,
`consume-limited-power.js`.

---

## Pułapki — obie platformy razem

| Pułapka | Gdzie |
|---|---|
| Jednostki ×100 | tylko Sonoff |
| `ha2` per metoda | tylko Sonoff |
| `nc` jako string z paddingiem | tylko Shelly |
| Indeks od 0 vs od 1 | różnica |
| `/online`: `true`/`false` vs `online`/`offline` | różnica |
| Reboot po zmianie configu MQTT | tylko Shelly |
| **`NotifyStatus` to delta, nie pełny stan** | **oba** |
| **`null` w `NotifyStatus` = „pole zniknęło", nie „wartość null"** | Shelly [DOK] |
| Brak notyfikacji po HTTP | oba |
| Trzeba wysłać ramkę z `src` przed notyfikacjami po WS | oba |
| Limit 6 równoległych kanałów RPC | Shelly |
| 429 przy brute-force / przeciążeniu nonce'ów | Shelly |
| `-12806` / `-12810` przy przepełnieniu kolejki | Sonoff |
| `ret_aenergy` wliczone też w `aenergy` | Shelly |
| `aenergy.by_minute` tylko przy zsynchronizowanym zegarze | Shelly |
| `SetProfile` kasuje webhooki i harmonogramy | Shelly |
| `Meter.ResetCounters` kasuje też historię | Sonoff |

---

## Kolejność prac

1. Rozpoznanie: `/shelly` → `gen`, potem `Shelly.ListMethods`
2. Warstwa ramek + kanał WebSocket z reconnectem (wspólna)
3. `digest.ts` z hakiem `computeHa2` / `formatNonceCount`
4. Dwa adaptery, każdy ~50 linii
5. Konfiguracja MQTT na urządzeniach (Shelly: **pamiętaj o reboocie**)
6. `mosquitto_sub -v -t '#'` i **podejrzenie, co realnie leci** przed pisaniem parsera
7. Kolektor: `NotifyStatus` na żywo, `NotifyFullStatus` przy starcie (Shelly)
8. Backfill historii: Sonoff `Meter.Download`; Shelly — sprawdzić `EMData`/`EM1Data`
