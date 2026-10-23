# Shelly Gen2+ vs Sonoff (nowe RPC)

Notatki źródłowe: [`../sonoff/`](../sonoff/) i pozostałe pliki w tym folderze.

## Teza

**Sonoff skopiował API Shelly.** Dowody są zbyt liczne, żeby to była zbieżność:

| Element | Shelly | Sonoff |
|---|---|---|
| Nagłówek serwera | `Server: ShellyHTTP/1.0.0` | `Server: SonoffHTTP/1.0.0` |
| Endpoint RPC | `/rpc` | `/rpc` |
| WebSocket | `ws://<ip>/rpc` | `ws://<ip>/rpc` |
| Topiki MQTT | `<id>/rpc`, `<src>/rpc`, `<id>/events/rpc`, `<id>/online` | identycznie |
| Nazwy metod | `X.GetStatus` / `GetConfig` / `SetConfig` | identycznie |
| Notyfikacje | `NotifyStatus`, `NotifyEvent` | identycznie |
| Zdarzenie zmiany configu | `config_changed` | `config_changed` |
| Autoryzacja | digest SHA-256 RFC 7616, `ha1 = SHA256(admin:realm:pass)` | identycznie |
| Challenge po WS | błąd 401, `message` = JSON jako string | identycznie |
| Warunki LWT | cztery punkty | **ta sama lista, to samo brzmienie** |
| Wyjątki od auth | `GetDeviceInfo`, MQTT, outbound WS | identycznie |
| Certyfikaty | `PutUserCA`, `PutTLSClientCert`, `PutTLSClientKey` | identycznie |
| `ssl_ca` | `null` / `*` / `ca.pem` / `user_ca.pem` | identycznie |

**Praktyczny wniosek: jedna warstwa abstrakcji obsłuży obu producentów.**

## Ale różnice są realne

### 🔴 Rzeczy, które wywalą wspólny kod

| Aspekt | Shelly | Sonoff | Konsekwencja |
|---|---|---|---|
| **Jednostki pomiarów** | **SI** — W, V, A, Wh | **×100** — `22000` = 220 V | Normalizacja obowiązkowa |
| **`ha2` po WebSocket** | stałe `SHA256("dummy_method:dummy_uri")` | `SHA256("Namespace:Metoda")`, inne **dla każdej metody** | Osobna implementacja podpisu |
| **`nc` w polu `auth`** | **string**, 8 cyfr hex z zerami (`"00000001"`) | **number**, dziesiętnie bez zer | Zły typ = zły podpis |
| **Indeksy komponentów** | od **0** (`switch:0`) | od **1** (`id: 1`) | Off-by-one w każdym wywołaniu |
| **Kody błędów** | −103…−114 | −12801…−12820 | Osobne mapowanie |
| **Payload `/online`** | `true` / `false` | `online` / `offline` | Osobny parser |
| **Klucz komponentu** | `switch:0` | `switch:1`, ale Meter zagnieżdża `params.meter.status` | Osobne ścieżki w notyfikacjach |
| **Zmiana configu MQTT** | **wymaga rebootu** | stosowana od ręki | Inny przebieg konfiguracji |

### 🟢 Gdzie Shelly jest wyraźnie lepszy

| Funkcja | Shelly | Sonoff |
|---|---|---|
| **Skrypty JS na urządzeniu** | ✅ Espruino, pełne API | ❌ brak |
| **`Shelly.ListMethods`** | ✅ zwraca co urządzenie potrafi | ❌ trzeba strzelać i łapać −12803 |
| **`Shelly.GetStatus`** | ✅ stan całego urządzenia jednym wywołaniem | ❌ komponent po komponencie |
| **`NotifyFullStatus`** | ✅ pełny stan przy starcie | ❌ brak |
| **mDNS** | ✅ `_shelly._tcp` + TXT `gen=` | ❌ brak (IP z routera) |
| **HTTP GET z query stringiem** | ✅ `/rpc/Switch.Set?id=0&on=true` | ❌ tylko POST całej ramki |
| **Ponowne użycie nonce'a** | ✅ 30 000 żądań / 1 h na jednym | ❌ pełny challenge za każdym razem |
| **Ochrona brute-force** | ✅ progresywny backoff, 429 | ❌ nieudokumentowana |
| **Topik błędów MQTT** | ✅ `<prefix>/error/<comp>` | ❌ złe komendy giną po cichu |
| **UDP RPC** | ✅ | ❌ |
| **Modbus** | ✅ rejestry i cewki | ❌ |
| **Webhooki warunkowe** | ✅ z atrybutami (`apower`) | ❌ |
| **`tag` przy zmianie stanu** | ✅ 20 znaków identyfikacji źródła | ❌ |
| **Certyfikaty serwera HTTPS** | ✅ `PutHTTPServer*` | ❌ tylko klienta |
| **Oficjalny serwer MCP dokumentacji** | ✅ | ❌ |
| **Dojrzałość dokumentacji** | ✅ lata rozwoju, wersjonowana | ⚠️ „Last modified: 2026-08-14" na wszystkim |

### 🟡 Gdzie Sonoff ma przewagę

| Funkcja | Sonoff | Shelly |
|---|---|---|
| **Autodiscovery HA wbudowane** | ✅ `enable_ha: true` | ❌ przez skrypt `mqtt-discovery.js` albo natywna integracja HA (nie MQTT) |
| **Historia energii na urządzeniu** | ✅ `Meter.GetRecords` / `Download`, 180 dni po godzinach | ⚠️ `aenergy.by_minute` to tylko 3 ostatnie minuty; historia w osobnych `EMData`/`EM1Data` [DO SPRAWDZENIA] |
| **Taryfy i koszty** | ✅ `price_info`, przedziały godzinowe, weekendy | ❌ brak w czytanych komponentach |
| **Reset hasła z fizycznym potwierdzeniem** | ✅ `ForgetAuth` + podwójne kliknięcie | ⚠️ `FactoryReset` |

`Meter.GetRecords` u Sonoffa to naprawdę użyteczna rzecz — pozwala zrobić backfill po
przestoju kolektora bez utraty danych. U Shelly odpowiednikiem są komponenty `EMData` /
`EM1Data`, ale ich nie czytałem, więc nie wiem, czy działają na przekaźnikach czy tylko na
licznikach energii serii EM.

## Wspólny mianownik — co zadziała identycznie

Jeśli budujesz warstwę abstrakcji, to **poniżej wszystko jest wspólne**:

- kształt ramki: `{id, src, method, params}` → `{id, src, dst, result|error}`
- kanał WebSocket `ws://<ip>/rpc` z wymogiem wysłania ramki z `src` przed notyfikacjami
- brak notyfikacji po HTTP
- topiki MQTT `<id>/rpc`, `<src>/rpc`, `<id>/events/rpc`, `<id>/online`
- MQTT bez autoryzacji digest
- `NotifyStatus` jako **delta**, `NotifyEvent` z tablicą `events[]`
- `config_changed` jako sygnał „dociągnij konfigurację"
- `Switch.Set` / `Switch.Toggle` / `Switch.GetStatus` z polem `on`
- digest SHA-256 z `response = SHA256(ha1:nonce:nc:cnonce:auth:ha2)`

Różnice to głównie **`ha2`, typ `nc`, indeksowanie od 0/1, jednostki i kody błędów**.
Pięć punktów — do ogarnięcia w jednym adapterze per producent.

## Rekomendacja przy zakupie

[DEDUKCJA, moja opinia — nie fakt z dokumentacji]

**Shelly** gdy: chcesz pisać integracje, potrzebujesz logiki działającej lokalnie bez serwera,
zależy ci na dojrzałej dokumentacji i wsparciu Home Assistanta.

**Sonoff** gdy: liczy się cena, wystarczy autodiscovery do HA bez pisania kodu, albo
potrzebujesz gotowej historii zużycia z taryfami na samym urządzeniu.

Do rozdzielnicy na 32 A (jak BASIC-1GSP) i tak decyduje dostępność konkretnego modelu na
szynę DIN, nie API.
