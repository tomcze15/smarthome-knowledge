# Plan integracji z JS/TS

Kontekst: `~/smarthome/core` to projekt Node w TypeScript (CommonJS, `ts-node`), ma już
zależność `mqtt@^5` i katalogi `mosquitto` / `mosquitto-data` / `zigbee2mqtt-data`.
Czyli broker MQTT już stoi.

---

## Krok 1 — rozstrzygnąć niewiadomą (5 minut)

**Zanim napiszesz jedną linijkę kodu.** Trzeba ustalić, czy BASIC-1GSP w ogóle wystawia
lokalne RPC (patrz README — dokumentacja jest firmowana jako MINI-1GSP i wskazuje na inny MCU).

```bash
export SONOFF=<ip-urzadzenia>
curl -X POST -d '{"id":1,"src":"probe","method":"Sonoff.GetDeviceInfo"}' http://${SONOFF}/rpc
```

Interpretacja:

| Wynik | Znaczenie |
|---|---|
| JSON z `result.id`, `result.model` | 🎉 Jest. Cała ścieżka z plików 02–04 otwarta. Zapisz `id` i `model` |
| `-12803 METHOD_NOT_FOUND` | Serwer RPC jest, ale bez komponentu `Sonoff` — dziwne, drążyć |
| 404 / connection refused | Brak RPC na porcie 80. Sprawdź port 8081 (plik 05), potem plan B |
| Timeout | Zły IP albo urządzenie w innym VLAN-ie |

Jeśli odpowiedziało — sprawdź od razu, które komponenty są obsługiwane. `-12803` na metodzie
= komponentu nie ma:

```bash
for M in Switch.GetStatus Meter.GetStatus MQTT.GetConfig System.GetStatus; do
  echo "== $M"
  curl -s -X POST -d "{\"id\":1,\"src\":\"probe\",\"method\":\"$M\",\"params\":{\"id\":1}}" http://${SONOFF}/rpc
  echo
done
```

Uwaga: jeśli hasło już ustawione, dostaniesz 401 zamiast wyniku — to też jest odpowiedź
twierdząca (metoda istnieje, tylko chroniona).

**Plan B, gdy RPC nie ma:** Matter → Home Assistant. Karta produktu potwierdza, że HA
i SmartThings widzą przez Matter również statystyki zużycia. Wtedy dane ciągnie się z HA
(REST/WebSocket API), a nie bezpośrednio z urządzenia.

---

## Krok 2 — wybrać architekturę

Dwie sensowne drogi. **Zdecydowanie polecam B.**

### A. Klient RPC w Node (WebSocket + HTTP)

```
BASIC-1GSP ──ws://ip/rpc── kolektor w Node ── baza
```

- ➕ pełna kontrola, dostęp do wszystkich metod (`Meter.Download`, `Switch.GetRecords`)
- ➖ trzeba samemu zaimplementować digest SHA-256, w tym paskudne `ha2` liczone **per metoda**
- ➖ własny reconnect, backoff, obsługa regenerowanego `nonce`

### B. Urządzenie → mój Mosquitto → wszystko inne ⭐

```
BASIC-1GSP ──MQTT──▶ Mosquitto ──┬──▶ Home Assistant (autodiscovery)
                                  └──▶ kolektor w Node (mqtt@5, już w core)
```

- ➕ **MQTT nie wymaga autoryzacji digest** — cały ból znika
- ➕ jednorazowa konfiguracja urządzenia, potem tylko konsumpcja topików
- ➕ `enable_ha: true` = encje w HA za darmo
- ➕ reconnect i LWT załatwia broker; `retained` na `/status` znaczy, że po restarcie
  kolektor od razu dostaje ostatni stan
- ➖ i tak potrzebujesz **jednorazowo** RPC (WS/HTTP z digestem), żeby wywołać `MQTT.SetConfig`
- ➖ przez czyste MQTT nie ma dostępu do historii — `Meter.Download` idzie jako RPC po MQTT,
  co nadal działa (topik `<id>/rpc`), ale jest mniej wygodne

**Rekomendacja:** raz przejść przez digest, żeby ustawić MQTT (albo zrobić to z panelu WWW
urządzenia, jeśli ma to w UI), a potem żyć na MQTT.

---

## Krok 3 — kod

### Digest po HTTP (jednorazowe wywołania)

Node ma wbudowany `fetch`, ale **nie umie digest auth**. Większość bibliotek digest robi
wyłącznie MD5, nie SHA-256. Trzeba policzyć samemu:

```ts
import { createHash, randomBytes } from 'node:crypto';

const sha256 = (value: string) => createHash('sha256').update(value).digest('hex');

export async function rpcOverHttp(
  host: string,
  password: string,
  method: string,
  params: Record<string, unknown> = {},
) {
  const body = JSON.stringify({ jsonrpc: '2.0', id: Date.now(), src: 'collector', method, params });
  const endpoint = `http://${host}/rpc`;

  const unauthenticated = await fetch(endpoint, { method: 'POST', body });
  if (unauthenticated.status !== 401) return unauthenticated.json();

  const header = unauthenticated.headers.get('www-authenticate') ?? '';
  const challenge = Object.fromEntries(
    [...header.matchAll(/(\w+)="([^"]*)"/g)].map((match) => [match[1], match[2]]),
  );

  const clientNonce = randomBytes(8).toString('hex');
  const nonceCount = '00000001';
  const ha1 = sha256(`admin:${challenge.realm}:${password}`);
  const ha2 = sha256('POST:/rpc');
  const digestResponse = sha256(
    `${ha1}:${challenge.nonce}:${nonceCount}:${clientNonce}:auth:${ha2}`,
  );

  return fetch(endpoint, {
    method: 'POST',
    body,
    headers: {
      Authorization:
        `Digest username="admin", realm="${challenge.realm}", nonce="${challenge.nonce}", ` +
        `uri="/rpc", algorithm=SHA-256, qop=auth, nc=${nonceCount}, ` +
        `cnonce="${clientNonce}", response="${digestResponse}"`,
    },
  }).then((response) => response.json());
}
```

⚠️ **[DEDUKCJA]** — kanał HTTP jest w dokumentacji pokazany wyłącznie przez `curl --digest`,
więc powyższe opiera się na standardzie RFC 7616, nie na przykładzie producenta. Konkretnie
niepewne: czy `ha2` to na pewno `POST:/rpc` i czy `nc` ma być 8-cyfrowym hexem z zerami.
Jeśli dostaniesz `-12809 ACCESS_DENIED`, to jest pierwsze miejsce do debugowania —
porównaj z `curl --digest -v`, który na pewno działa.

### Digest po WebSocket (tu dokumentacja jest jednoznaczna)

Format `auth` jest opisany wprost, z działającym przykładem w Pythonie. Przepisane na TS:

```ts
import { createHash } from 'node:crypto';

const sha256 = (value: string) => createHash('sha256').update(value).digest('hex');

export function computeAuth(
  method: string,
  nonce: string,
  nonceCount: number,
  realm: string,
  password: string,
) {
  const clientNonce = Math.floor(Date.now() / 1000);
  const [namespace, procedure] = method.split('.');

  const ha1 = sha256(`admin:${realm}:${password}`);
  const ha2 = sha256(`${namespace}:${procedure}`);

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

Flow: wyślij ramkę bez `auth` → dostaniesz błąd `401`, gdzie `error.message` to **string
z zagnieżdżonym JSON-em** → `JSON.parse` → wyciągnij `nonce`, `nc`, `realm` → wyślij ponownie
z polem `auth`.

🔴 `ha2` zależy od nazwy metody, więc **liczy się je dla każdego wywołania od nowa**. Nie
cache'ować. `nonce` serwer regeneruje przy każdym challenge.

### Konsument MQTT (docelowy kształt kolektora)

```ts
import mqtt from 'mqtt';

const DEVICE_ID = 'sonoffbasic1gsp-xxxxxxxxxxxx';
const client = mqtt.connect('mqtt://localhost:1883');

const scaled = (raw: number) => raw / 100;

client.on('connect', () => {
  client.subscribe([
    `${DEVICE_ID}/online`,
    `${DEVICE_ID}/status`,
    `${DEVICE_ID}/events/rpc`,
    'collector/rpc',
  ]);
});

client.on('message', (topic, payload) => {
  if (topic === `${DEVICE_ID}/online`) {
    console.log('dostępność:', payload.toString());
    return;
  }

  const frame = JSON.parse(payload.toString());

  if (frame.method === 'NotifyStatus' && frame.params?.meter?.status) {
    const meter = frame.params.meter.status;
    console.log({
      ts: frame.params.ts,
      napiecie: scaled(meter.voltage),
      prad: scaled(meter.current),
      moc: scaled(meter.power),
      czestotliwosc: scaled(meter.freq),
      energiaDzienna: scaled(meter.day_energy),
    });
  }
});

// sterowanie: albo uproszczony topik komendowy...
const setSwitch = (on: boolean) => client.publish(`${DEVICE_ID}/cmd/switch`, on ? 'ON' : 'OFF');

// ...albo pełne RPC (odpowiedź wróci na collector/rpc)
const requestRecords = () =>
  client.publish(
    `${DEVICE_ID}/rpc`,
    JSON.stringify({
      jsonrpc: '2.0',
      id: Date.now(),
      src: 'collector',
      method: 'Meter.Download',
      params: { id: 1, type: 1, offset: 0, count: 32 },
    }),
  );
```

---

## Krok 4 — kolejność wdrożenia

1. ✅ Sonda z kroku 1, zapisz `id` urządzenia i `model`
2. Ustaw hasło przez `Sonoff.SetAuth` (jeśli `initial_password: false`)
3. Jednym wywołaniem RPC ustaw `MQTT.SetConfig` na lokalny Mosquitto, `enable_ha: true`
4. `MQTT.GetStatus` → `connected: true`
5. `mosquitto_sub -v -t "<id>/#"` i **podejrzyj co realnie leci** — zwłaszcza kształt
   `<id>/status`, którego dokumentacja nie rozpisuje
6. Sprawdź, czy encje pojawiły się w Home Assistancie
7. Dopiero teraz kolektor w Node: strumień na żywo z notyfikacji
8. Backfill historii przez `Meter.Download` (pakiety po 32) — jednorazowo przy starcie
   i po każdej dłuższej przerwie kolektora

## Pułapki do zapamiętania

| Pułapka | Skutek |
|---|---|
| Wszystkie pomiary są **×100** | `22000` to 220 V, nie 22 kV |
| `ha2` po WS = `SHA256("Namespace:Metoda")` | inne dla każdej metody, nie da się cache'ować |
| Notyfikacji **nie ma po HTTP** | do strumienia danych tylko WS albo MQTT |
| Po WS trzeba najpierw wysłać ramkę z `src` | inaczej cisza mimo poprawnego połączenia |
| `params.meter.status` vs płaskie `{id,on}` | Meter zagnieżdża notyfikację głębiej niż Switch |
| `Meter.GetRecords` `type` = string, `Meter.Download` `type` = number | łatwo pomylić |
| `delay_width` w ms (RPC) vs `power_on_delay_time` w s (MQTT) | trzy rzędy wielkości |
| Topiki `cmd/` nie odpowiadają | potwierdzenie tylko przez `/status` |
| `Meter.ResetCounters` kasuje **też historię** | nieodwracalne |
| Nieudokumentowane pola w odpowiedziach | producent zastrzega, że mogą zniknąć — nie opierać się |
| Broker bez hasła w LAN | każdy sprzęt w sieci może odciąć obwód 32 A |
