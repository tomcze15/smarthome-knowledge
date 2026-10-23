# MQTT w Gen2+

Źródło: `gen2/ComponentsAndServices/Mqtt`, `gen2/General/RPCChannels`.

**QoS 1** — gwarancja dostarczenia co najmniej raz. [DOK]

## Co daje połączenie MQTT

| Funkcja | Flaga konfiguracji |
|---|---|
| Kanał RPC | `enable_rpc` |
| Notyfikacje RPC (`NotifyStatus`, `NotifyEvent`) | `rpc_ntf` (domyślnie **true**) |
| Publikowanie pełnego statusu komponentów | `status_ntf` (domyślnie **false**) |
| Interfejs sterowania komponentami | `enable_control` (domyślnie **true**) |

## Konfiguracja

| Pole | Opis |
|---|---|
| `enable` | Włącz połączenie |
| `server` | `host` lub `host:port` |
| `client_id` | `null` → używane jest `device_id` |
| `user` | Nazwa użytkownika |
| `ssl_ca` | `null` = zwykłe TCP · `*` = **TLS bez walidacji certyfikatu** · `user_ca.pem` = własne CA · `ca.pem` = wbudowany bundle |
| `topic_prefix` | Prefiks topików. `null` → `device_id`. **Max 300 znaków**, nie może zaczynać się od `$`, zabronione `#`, `+`, `%`, `?` |
| `rpc_ntf` | Notyfikacje na `<prefix>/events/rpc` |
| `status_ntf` | Pełny status na `<prefix>/status/<component>:<id>` przy **znaczącej** zmianie |
| `use_client_cert` | Certyfikaty klienta |
| `enable_control` | Interfejs sterowania |

Status komponentu: jedno pole `connected`.

Uwaga: `ssl_ca: "*"` to **TLS z wyłączoną walidacją certyfikatu** — szyfrowanie bez
uwierzytelnienia serwera. U Sonoffa `*` znaczy „systemowy CA bundle", czyli coś innego.
[DOK dla obu] Nie przenosić konfiguracji jeden do jednego.

## 🔴 Zmiana konfiguracji MQTT wymaga rebootu

To najważniejsza różnica operacyjna wobec Sonoffa, który przebudowuje połączenie sam.

Procedura wg dokumentacji:

```bash
# 1. sprawdź stan
curl -X POST -d '{"id":1, "method":"Mqtt.GetStatus"}' http://${SHELLY}/rpc

# 2. sprawdź konfigurację
curl -X POST -d '{"id":1, "method":"Mqtt.GetConfig"}' http://${SHELLY}/rpc

# 3. ustaw
curl -X POST -d '{"id":1, "method":"Mqtt.SetConfig", "params":{"config":{"enable":true, "server":"broker.hivemq.com:1883"}}}' http://${SHELLY}/rpc

# 4. REBOOT — bez tego nowe ustawienia nie wejdą
curl -X POST -d '{"id":1, "method":"Shelly.Reboot"}' http://${SHELLY}/rpc

# 5. zweryfikuj
curl -X POST -d '{"id":1, "method":"Mqtt.GetStatus"}' http://${SHELLY}/rpc
```

Odpowiedź na `SetConfig` sama sygnalizuje konieczność restartu. Tak jak u Sonoffa,
`SetConfig` **wymaga opakowania w `params.config`**.

Pola pominięte w `config` zachowują dotychczasowe wartości — można ustawić samo `enable`
albo sam `server`.

## Topiki

| Topik | Kierunek | Payload |
|---|---|---|
| `<prefix>/events/rpc` | ← | Notyfikacje |
| `<prefix>/status/<comp>:<id>` | ← | Pełny status komponentu (gdy `status_ntf`) |
| `<prefix>/command/<comp>:<id>` | → | Komendy sterujące (gdy `enable_control`) |
| `<prefix>/error/<comp>:<id>` | ← | Komunikat błędu przy złych parametrach komendy |
| `<shelly-id>/rpc` | → | Żądania RPC |
| `<src>/rpc` | ← | Odpowiedzi |
| `<shelly-id>/online` | ← | **`true`** / **`false`** (LWT) |

⚠️ `<prefix>` to `topic_prefix` jeśli ustawiony, inaczej `device_id`. Ale topiki
`<shelly-id>/rpc` i `<shelly-id>/online` w dokumentacji kanałów są opisane przez `<shelly-id>`
— **[DO SPRAWDZENIA]** czy custom prefix zmienia także je, czy tylko `events`/`status`/`command`.

## Warunki LWT [DOK]

Broker publikuje `false`, gdy:

- wykryje błąd I/O lub awarię sieci
- klient nie odezwie się w okresie keep-alive
- klient zamknie połączenie bez pakietu DISCONNECT
- broker zamknie połączenie z powodu błędu protokołu

Identyczna lista jak u Sonoffa — kolejny dowód na wspólne źródło.

## Praktyka

Bez autoryzacji digest po MQTT (patrz [02](02-api-rpc-gen2.md)), więc kolektor tylko słucha
brokera. To samo podejście co przy Sonoffie — jedna wspólna warstwa MQTT obsłuży oba
producenty, jeśli zrobić mapowanie topików i normalizację jednostek.

Do Home Assistanta: Shelly **nie ma** wbudowanego autodiscovery MQTT jak Sonoff
(`enable_ha`). Zamiast tego są dwie drogi:

1. **Natywna integracja Shelly w Home Assistancie** — po WebSocket/CoIoT, nie przez MQTT.
   Dojrzała i rekomendowana
2. Skrypt na urządzeniu **`mqtt-discovery.js`** z repozytorium `shelly-script-examples`,
   który dorabia autodiscovery — patrz [05](05-skrypty-on-device.md)

⚠️ Podpięcie Shelly do HA przez MQTT **wyłącza** natywną integrację (obie naraz się gryzą).
[DEDUKCJA] — logiczne, ale nie napisane wprost w czytanej dokumentacji, sprawdzić przed
wdrożeniem.
