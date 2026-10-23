# Lokalne API RPC (gałąź „MINI-1GSP API")

Źródła: `API_QuickStart`, `API_RPCProtocol`, `API_RPCChannels`, `API_Notifications`,
`API_Authentication`, `API_ErrorCode`, `API_Sonoff`.

To jest **nowa generacja** lokalnego API Sonoffa. Stare DIY API (port 8081) to co innego —
plik [05](05-diy-mode-legacy.md).

## W skrócie

**JSON-RPC 2.0**, metody w przestrzeniach nazw, trzy kanały transportowe (HTTP / WebSocket /
MQTT), autoryzacja digest SHA-256. Bez chmury, bez konta eWeLink, bez aplikacji.

Urządzenie ma **własny web backend i tryb AP** — pierwszą konfigurację (Wi-Fi, hasło) da się
zrobić z przeglądarki, bez żadnego ekosystemu producenta. [DOK]

## Ramki

### Request

| Pole | Typ | Wymagane | Opis |
|---|---|---|---|
| `jsonrpc` | string | nie | `"2.0"` |
| `id` | number/string | **tak** | Identyfikator żądania |
| `src` | string | **tak** | Nazwa źródła — dowolna, np. `"user_1"`. **Kluczowe:** po WS bez tego nie dostaniesz notyfikacji |
| `method` | string | **tak** | np. `"Switch.GetConfig"` |
| `params` | object | nie | Parametry metody |
| `auth` | object | zależnie | Tylko WebSocket, patrz Autoryzacja |

```json
{ "id": 1, "src": "user_1", "method": "Switch.GetConfig", "params": { "id": 1 } }
```

### Response

Zawiera `id`, `src` (ID urządzenia), `dst` (= `src` z żądania) oraz **`result` albo `error`**
— nigdy oba.

```json
{ "id": 2, "src": "sonoffmini1gsp-acebe61fae74", "dst": "user_1", "result": {} }
```

```json
{ "id": 1, "error": { "code": -12805, "message": "internal error" } }
```

### Notification

Bez `id`, nie oczekuje odpowiedzi. Pola: `src`, `dst`, `method`, `params` — wszystkie wymagane.
Dwa typy: `NotifyStatus` (zmiana stanu komponentu) i `NotifyEvent` (zdarzenie, np. wciśnięcie
przycisku, `config_changed`).

## Kanały

### HTTP

`POST http://<ip>/rpc`, port 80. Jedno żądanie = jedna odpowiedź, bez trwałego połączenia.
**Notyfikacji po HTTP nie da się odbierać.** [DOK]

```bash
curl -X POST -d '{"id":2,"src":"user_1","method":"Switch.Set","params":{"id":1,"on":true}}' "http://${SONOFF}/rpc"
```

### WebSocket

`ws://<ip>/rpc`, port 80. Połączenie trwałe, ten sam mechanizm, którego używa lokalny panel WWW
urządzenia. **Żeby dostawać notyfikacje, trzeba najpierw wysłać co najmniej jedną ramkę
żądania z poprawnym `src`.** [DOK]

To jest domyślny kanał do zbierania danych.

### MQTT

Urządzenie samo łączy się do wskazanego brokera (konfiguracja przez `MQTT.SetConfig` —
plik [04](04-mqtt-i-home-assistant.md)).

| Topik | Kierunek | Opis |
|---|---|---|
| `<sonoff-id>/rpc` | publish | Żądania do urządzenia |
| `<src>/rpc` | subscribe | Odpowiedzi (`<src>` = to, co wpisałeś w ramce) |
| `<sonoff-id>/events/rpc` | subscribe | Notyfikacje |
| `<sonoff-id>/status` | subscribe | Stan urządzenia, wiadomość **retained** |
| `<sonoff-id>/online` | subscribe | `online` / `offline`, retained, `offline` też jako LWT |

`<sonoff-id>` to np. `sonoffmini1gsp-acebe61fae74`.

Broker publikuje LWT `offline`, gdy: wykryje błąd I/O lub sieci, klient nie odezwie się w
keep-alive, klient zerwie połączenie bez DISCONNECT, albo broker zamknie połączenie z powodu
błędu protokołu. [DOK]

## Autoryzacja

**Digest SHA-256 wg RFC 7616.** Nie ma tokenów, nie ma JWT.

### Nie wymagają autoryzacji [DOK]

- `Sonoff.GetDeviceInfo`
- Outbound WebSocket
- **cała komunikacja MQTT** ← istotne, MQTT omija digest

### Ustawienie hasła (pierwsze uruchomienie)

1. `Sonoff.GetDeviceInfo` → sprawdź `initial_password`
   - `false` → hasła nie ma, idź do kroku 2
   - `true` → hasło ustawione, przechodzisz do flow challenge-response
2. `Sonoff.SetAuth` z parametrami:
   - `user`: zawsze `"admin"`
   - `realm`: ID urządzenia (pole `id` z `GetDeviceInfo`)
   - `ha1`: `SHA256("admin:<id urządzenia>:<hasło>")`

Dopóki hasło nie jest ustawione, wszystkie metody poza tymi bez autoryzacji zwracają błąd
`No password configured, access denied` (kod `-12820`). [DOK]

```bash
echo -n "admin:sonoffmini1gsp-acebe61fae74:12345678" | sha256sum
```

### HA1

```
ha1 = SHA256("admin:<realm>:<hasło>")
```

`<realm>` to ID urządzenia, np. `sonoffmini1gsp-acebe61fae74`.

### Flow

1. Klient pyta o zasób chroniony bez poświadczeń
2. Urządzenie zwraca **401** z `realm` i `nonce`
3. Klient powtarza żądanie z policzonymi poświadczeniami
4. Urządzenie weryfikuje i odpowiada

### HTTP — nagłówek 401 [DOK]

```
WWW-Authenticate: Digest qop="auth", realm="sonoffmini1gsp-acebe61fae74", nonce="2dswss384", algorithm=SHA-256
```

`curl --digest -u admin:<hasło>` załatwia cały flow automatycznie.

### WebSocket — inaczej niż HTTP! ⚠️

Po WS **nie ma** nagłówków HTTP. Challenge przychodzi jako **błąd 401, w którym `message` to
zagnieżdżony JSON jako string**:

```json
{
  "id": 1,
  "src": "sonoffmini1gsp-acebe61fae74",
  "dst": "user_1",
  "error": {
    "code": 401,
    "message": "{\"auth_type\":\"digest\",\"nonce\":\"BMWOplDD{@Xg*R$f\",\"nc\":1,\"realm\":\"sonoffmini1gsp-acebe61fae74\",\"algorithm\":\"SHA-256\"}"
  }
}
```

Czyli: `JSON.parse(response.error.message)`. Uwaga, `nonce` bywa pełen znaków specjalnych.

Odpowiedź wkłada się w pole **`auth`** ramki żądania:

| Pole | Typ | Opis |
|---|---|---|
| `realm` | string | ID urządzenia |
| `username` | string | zawsze `admin` |
| `nonce` | string | z błędu 401, serwer regeneruje przy każdym challenge |
| `cnonce` | **number** | losowa liczba klienta (może być timestamp) |
| `nc` | **number** | licznik klienta, przy sklejaniu jako dziesiętny string **bez wiodących zer** |
| `response` | string | patrz niżej |
| `algorithm` | string | zawsze `"SHA-256"` |

```
ha1      = SHA256("admin:<realm>:<hasło>")
ha2      = SHA256("<method1>:<method2>")     ← np. Sonoff.GetConfig → SHA256("Sonoff:GetConfig")
response = SHA256("{ha1}:{nonce}:{nc}:{cnonce}:auth:{ha2}")
```

🔴 **Największa pułapka całego API:** `ha2` po WebSocket liczy się z **nazwy metody RPC
rozbitej po kropce**, a nie z `METODA_HTTP:URI` jak w klasycznym digeście. Czyli `ha2` jest
**inne dla każdej wywoływanej metody** — nie da się policzyć raz i cache'ować.

Dla HTTP obowiązuje standardowy RFC, czyli `ha2 = SHA256("POST:/rpc")`.

```json
{"id":2, "src":"user_1", "method":"Sonoff.GetConfig", "auth": {"realm":"sonoffmini1gsp-acebe61fae74","username":"admin","nonce":"BMWOplDD{@Xg*R$f","nc":1,"cnonce":1786349688,"response":"<policzone>","algorithm":"SHA-256"}}
```

### Reset hasła [DOK]

- `Sonoff.ForgetAuth` z `state: "waiting"` → urządzenie daje **10 s** na fizyczne potwierdzenie
  **podwójnym kliknięciem przycisku**
- Po autoryzacji jest **10 minut** na wywołanie `Sonoff.ReSetAuth` z nowym `ha1`
- Oba te wywołania **nie wymagają** autoryzacji interfejsu
- Po sukcesie leci event `reset_auth_succ`, po porażce `reset_auth_fail`

Obie wartości czasowe są zaszyte w urządzeniu i niekonfigurowalne.

## Komponent Sonoff — zarządzanie urządzeniem

| Metoda | Opis |
|---|---|
| `Sonoff.GetDeviceInfo` | Info o urządzeniu (bez auth) |
| `Sonoff.GetConfig` / `SetConfig` | Konfiguracja — na razie tylko `name`, max 20 znaków |
| `Sonoff.SetAuth` | Ustawienie hasła |
| `Sonoff.ReSetAuth` | Reset hasła (wymaga fizycznej autoryzacji) |
| `Sonoff.ForgetAuth` | Start procedury zapomnienia hasła |

### `Sonoff.GetDeviceInfo` — odpowiedź [DOK]

| Pole | Typ | Opis |
|---|---|---|
| `name` | string | Nazwa, max 20 znaków |
| `model` | string | Model, read-only |
| `id` | string | **ID urządzenia**, format `sonoff<model>-<mac>`, np. `sonoffmini1gsp-acebe61fae74` |
| `ver` | string | Firmware, `major.minor.patch`, zakres 0.0.0 – 99.99.99 |
| `ip` | string | IPv4 |
| `mac` | string | MAC, małe litery, rozdzielony `:` |
| `auth_en` | boolean | Czy autoryzacja włączona. Nie da się jej **wyłączyć** — `false` tylko gdy hasła nigdy nie ustawiono |
| `auth_domain` | string | `null` gdy autoryzacja wyłączona |
| `initial_password` | boolean | Czy hasło początkowe zostało ustawione |

⚠️ Dokumentacja explicite ostrzega: odpowiedź **może zawierać dodatkowe, nieudokumentowane pola
rezerwowe**, które mogą zniknąć w kolejnych firmware'ach. Nie opierać się na nich. [DOK]

Uwaga na niespójność w samej dokumentacji: `API_QuickStart` pokazuje `"model": "mini1gsp"`,
a `API_Sonoff` `"model": "SN-ESP32C6-MINI1GSP-01"`. Ten drugi wskazuje na ESP32-C6 —
patrz README, sekcja o niewiadomej.

## Kody błędów [DOK]

| Kod | Nazwa | Opis |
|---|---|---|
| −12801 | `RPC_ERR_PARSE_ERROR` | Błąd parsowania JSON |
| −12802 | `RPC_ERR_INVALID_REQUEST` | Zły format ramki |
| −12803 | `RPC_ERR_METHOD_NOT_FOUND` | Metoda nie istnieje ← **tego szukaj przy sprawdzaniu wsparcia komponentu** |
| −12804 | `RPC_ERR_INVALID_PARAMS` | Złe parametry |
| −12805 | `RPC_ERR_INTERNAL` | Błąd wewnętrzny |
| −12806 | `RPC_ERR_SERVER_BUSY` | Serwer zajęty (kolejka pełna) |
| −12807 | `RPC_ERR_TIMEOUT` | Timeout |
| −12808 | `RPC_ERR_DST_INVALID` | Zły adres docelowy |
| −12809 | `RPC_ERR_ACCESS_DENIED` | Autoryzacja nieudana |
| −12810 | `RPC_ERR_QUEUE_FULL` | Kolejka żądań pełna |
| −12811 | `RPC_ERR_NOT_INITED` | Szyna RPC niezainicjalizowana |
| −12812 | `RPC_ERR_INVALID_STATE` | Operacja niedozwolona w tym stanie |
| −12813 | `RPC_ERR_NO_MEMORY` | Brak pamięci |
| −12814 | `RPC_ERR_NOT_SUPPORTED` | Operacja niewspierana |
| −12815 | `RPC_ERR_INVALID_SIZE` | Rozmiar danych poza limitem |
| −12816 | `RPC_ERR_CHECKSUM_FAILED` | Błąd sumy kontrolnej |
| −12817 | `RPC_ERR_DECRYPT_FAILED` | Błąd deszyfrowania |
| −12818 | `RPC_ERR_MODEL_FAILED` | Niezgodność modelu |
| −12819 | `RPC_ERR_VERSION_FAILED` | Niezgodność wersji |
| −12820 | `RPC_ERR_NO_PASSWORD` | Hasło nieustawione, dostęp odrzucony |

Plus `401` przy challenge digest (kanał WS).

Warto zauważyć: `-12806` i `-12810` to osobne kody dla przepełnienia kolejki. Urządzenie ma
skończony bufor żądań — przy zbieraniu danych nie zalewać go równoległymi wywołaniami.
**W dokumentacji RPC nie ma podanego twardego limitu odstępu między żądaniami** (limit 200 ms
występuje w *starym* DIY API, plik 05 — nie przenosić go tutaj bezmyślnie). [DO SPRAWDZENIA]

## Pozostałe komponenty (nie czytane w szczegółach)

`System`, `Cloud`, `Input`, `Matter`, `Ota`, `Timer`, `WebSocket`, `WiFi` — do doczytania gdy
będą potrzebne. Szczegółowo opisane: `Sonoff`, `Switch`, `Meter`, `MQTT`.
