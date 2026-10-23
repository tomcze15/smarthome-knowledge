# MQTT + Home Assistant autodiscovery

Źródło: `API_MQTT`. Wszystko **[DOK]** o ile nie zaznaczono inaczej.

## Dlaczego to jest najważniejszy plik w tym folderze

Urządzenie potrafi **samo połączyć się do mojego brokera Mosquitto** (jest już w
`~/smarthome/core/mosquitto`) i **samo wystawić encje do Home Assistanta** przez autodiscovery.

To zmienia architekturę: zamiast pisać kolektor odpytujący urządzenie, mogę ustawić urządzenie
raz i **tylko słuchać brokera**. Apka w JS staje się konsumentem topików, a nie klientem RPC.

Dodatkowy bonus: **komunikacja MQTT nie wymaga autoryzacji digest** (patrz plik 02). Cały ból
z liczeniem SHA-256 i `ha2` per metoda znika — zostaje tylko jednorazowa konfiguracja.

## Metody

| Metoda | Opis |
|---|---|
| `MQTT.SetConfig` | Ustaw konfigurację |
| `MQTT.GetConfig` | Odczytaj (hasło zawsze zwracane jako `null`) |
| `MQTT.GetStatus` | Czy połączony z brokerem |
| `MQTT.GetCertsInfo` | Nazwy wgranych certyfikatów |
| `MQTT.PutUserCA` | Wgraj/usuń własne CA |
| `MQTT.PutTLSClientCert` | Wgraj/usuń certyfikat klienta (mTLS) |
| `MQTT.PutTLSClientKey` | Wgraj/usuń klucz prywatny (mTLS) |

## Config

| Pole | Typ | Opis |
|---|---|---|
| `enable` | boolean | Włącz połączenie MQTT |
| `enable_ha` | boolean | **Home Assistant MQTT autodiscovery** |
| `rpc_ntf` | boolean | Czy wysyłać notyfikacje RPC przez MQTT |
| `status_ntf` | boolean | Czy automatycznie publikować status |
| `debug_output` | boolean | Logi livelog przez MQTT |
| `server` | string\|null | `host` lub `host:port`; host max 128 B, port 1–65535 |
| `userName` | string\|null | max 64 B |
| `pass` | string\|null | max 64 B, **nigdy nie jest zwracane** |
| `clientId` | string\|null | max 64 B; `null` lub pusty = urządzenie wygeneruje samo |
| `ssl_ca` | string\|null | `null`/pusty = TLS wyłączony · `*` = systemowy CA bundle · `ca.pem` = wbudowane w firmware · `user_ca.pem` = wgrane przeze mnie |

⚠️ `MQTT.SetConfig` **wymaga opakowania w `params.config`** — podanie pól bezpośrednio pod
`params` nie zadziała. Pola nieprzekazane zachowują dotychczasowe wartości; `null` dla pól
tekstowych je czyści.

Po udanej konfiguracji urządzenie **samo odbudowuje połączenie, restart nie jest potrzebny**.

## Konfiguracja krok po kroku

**1.** Sprawdź obecny stan (przez WS lub HTTP RPC):

```json
{"jsonrpc":"2.0","id":1,"src":"web","method":"MQTT.GetConfig","params":{}}
```

**2.** Ustaw brokera:

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "src": "web",
  "method": "MQTT.SetConfig",
  "params": {
    "config": {
      "enable": true,
      "enable_ha": true,
      "server": "192.168.3.29:1883",
      "userName": "mqtt_user",
      "pass": "mqtt_password",
      "clientId": "basic-1gsp-rozdzielnica",
      "ssl_ca": null,
      "rpc_ntf": true,
      "status_ntf": true,
      "debug_output": false
    }
  }
}
```

**3.** Zweryfikuj po kilku sekundach:

```json
{"jsonrpc":"2.0","id":3,"src":"web","method":"MQTT.GetStatus","params":{}}
```

→ `{"connected": true}`. Jeśli `false`: sprawdź adres i port, osiągalność sieciową,
dane logowania, konfigurację TLS.

## Topiki

| Topik | Payload | Uwagi |
|---|---|---|
| `<topic_root>/status` | JSON | **retained**, wymaga `status_ntf: true` |
| `<topic_root>/online` | `online` / `offline` | **retained**, `offline` też jako LWT |
| `<sonoff-id>/rpc` | ramka RPC | publish — żądania do urządzenia |
| `<src>/rpc` | ramka RPC | subscribe — odpowiedzi |
| `<sonoff-id>/events/rpc` | notyfikacje | subscribe, wymaga `rpc_ntf: true` |
| `<topic_root>/cmd/<pole>` | tekst | uproszczone sterowanie, plik [03](03-komponenty-switch-meter.md) |

`<topic_root>` domyślnie równa się ID urządzenia — można to potwierdzić patrząc na pole `src`
w odpowiedziach RPC.

Boole w topikach `cmd/` przyjmują **case-insensitive** `ON`, `OFF`, `true`, `false`, `1`, `0`.

## RPC przez MQTT

```bash
# nasłuch odpowiedzi
mosquitto_sub -h ${MQTT_SERVER} -p ${MQTT_PORT} -v -t "${RPC_SRC}/rpc"

# żądanie
mosquitto_pub -h ${MQTT_SERVER} -p ${MQTT_PORT} \
  -t "${SONOFF_ID}/rpc" \
  -m '{"jsonrpc":"2.0","id":100,"src":"sonoff-doc-client","method":"Switch.GetStatus","params":{"id":1}}'
```

Odpowiedź trafia na `sonoff-doc-client/rpc`, czyli `<src>/rpc`.

## TLS

Certyfikaty wgrywa się metodami `Put*`, w pakietach:

- `name` — nazwa pliku, max 64 B, niepusta
- `data` — treść PEM; **`null` usuwa** certyfikat
- `append` — `true` = to kolejny fragment, `false`/brak = koniec pakietu i zapis
- Max **4096 bajtów** na jeden PEM
- Odpowiedź: `{"len": <ile zapisano>}`, `0` po skasowaniu

Przy `append: true` pole `data` nie może być `null`.

Konfiguracja mTLS = wgrać CA + cert klienta + klucz, potem `ssl_ca: "user_ca.pem"` i port 8883.

## Bezpieczeństwo

Dokumentacja sama ostrzega: publiczne brokery (typu `broker.hivemq.com` z przykładów) nadają
się **tylko do weryfikacji funkcjonalnej**. W realnym wdrożeniu prywatny broker z hasłem lub TLS.

To istotne — mowa o urządzeniu, które potrafi odciąć obwód 32 A. Broker bez autoryzacji w LAN-ie
oznacza, że każdy sprzęt w sieci może wyłączyć pompę ciepła.

## Zdarzenia

`config_changed` po zmianie konfiguracji MQTT. W evencie `component` zawsze `"mqtt"`, `id`
zawsze `1`, brak dodatkowych pól. `ts` to **uniksowy timestamp UTC w sekundach, może zawierać
część dziesiętną**.

## [DO SPRAWDZENIA]

- Dokładny kształt payloadu `<topic_root>/status` — dokumentacja odsyła do „status definitions
  in this document", ale pełnej tabeli dla całego urządzenia nie pokazuje. Trzeba podejrzeć na
  żywo `mosquitto_sub -v -t '<id>/status'`
- Jakie dokładnie encje tworzy `enable_ha` w Home Assistancie i czy pokrywają pomiar energii
- Czy `enable_ha` używa domyślnego prefiksu `homeassistant/` (raczej tak [DEDUKCJA], ale nie
  jest to napisane)
