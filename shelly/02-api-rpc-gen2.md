# Gen2+ RPC API — rdzeń

Źródła: `gen2/General/RPCChannels`, `gen2/General/Authentication`, `gen2/General/CommonErrors`.

**JSON-RPC 2.0.** Ramki jak w Sonoffie (`id`, `src`, `dst`, `method`, `params`, `result`/`error`)
— albo raczej odwrotnie, bo to Shelly było pierwsze.

## Cztery kanały

Limit: **6 równoległych nietrwałych kanałów RPC** na urządzenie. [DOK]

### HTTP — trzy warianty wywołania

To jest wygoda, której Sonoff nie ma. Shelly przyjmuje RPC na **trzy sposoby**:

**1. POST całej ramki na `/rpc`** — zwraca pełną ramkę odpowiedzi:

```bash
curl -X POST -d '{"id":2, "method":"Switch.Set", "params":{"id":0, "on":true}}' "http://${SHELLY}/rpc"
```

**2. POST samych parametrów na `/rpc/<Metoda>`** — zwraca **tylko zawartość `result`**:

```bash
curl -X POST -d '{"id":0, "on":true}' "http://${SHELLY}/rpc/Switch.Set"
```

**3. GET z query stringiem** — najwygodniejsze do ręcznych testów i z przeglądarki:

```bash
curl "http://${SHELLY}/rpc/Switch.Set?id=0&on=true"
```

Zasady dla wariantu GET:

- Od wersji **1.0.0** cudzysłowów wymagają **tylko** literały `"true"` / `"false"` i wartości
  liczbowe, które mają być stringami; poza tym typ string jest domyślny
  (wcześniej cudzysłowy były wymagane dla wszystkich stringów)
- Obiekty przekazuje się na dwa sposoby:
  - cały JSON url-encoded: `/rpc/Sys.SetConfig?config={%22device%22:{%22name%22:%22Awesome%20Shelly%22}}`
  - **od 1.0.0** pojedyncze wartości ścieżką z kropkami: `/rpc/Sys.SetConfig?config.device.name=%22Awesome%20Shelly%22`

Notyfikacji po HTTP odebrać się nie da — tak samo jak u Sonoffa.

### WebSocket

`ws://<ip>/rpc`. Połączenie trwałe, ten sam mechanizm, którego używa lokalny panel WWW
i biblioteka `aioshelly` (ta z Home Assistanta).

**Trzeba wysłać co najmniej jedną ramkę z poprawnym `src`, żeby zacząć dostawać notyfikacje.**

### MQTT

Szczegóły w [04-mqtt.md](04-mqtt.md). Topiki:

| Topik | Kierunek |
|---|---|
| `<shelly-id>/rpc` | żądania do urządzenia |
| `<src>/rpc` | odpowiedzi |
| `<shelly-id>/events/rpc` | notyfikacje |
| `<shelly-id>/online` | `true` / `false` (LWT) |

⚠️ Uwaga na typ payloadu w `online`: Shelly publikuje **`true`/`false`**, Sonoff **`online`/`offline`**.

### UDP — czego u Sonoffa nie ma

RPC po UDP działa w dwie strony:

- notyfikacje wysyłane z urządzenia pod skonfigurowany adres peera
- wywołania RPC przez datagramy na skonfigurowany port urządzenia

**Domyślnie wyłączone**, włącza się kluczami `udp_rpc` w komponencie System.

Dokumentacja sama radzi: używać **tylko gdy WebSocket i MQTT nie wchodzą w grę**, bo UDP nie
daje gwarancji dostarczenia. Sensowne przy bardzo dużej liczbie urządzeń albo minimalnym
narzucie.

## Autoryzacja

**Digest SHA-256 wg RFC 7616** — dokładnie ten sam pomysł co u Sonoffa, ale lepiej dopracowany.

### Nie są chronione [DOK]

- komunikacja po **MQTT**
- komunikacja po **UART**
- **Outbound WebSocket**
- metoda RPC `Shelly.GetDeviceInfo`
- endpoint HTTP **`/shelly`**

### Ustawienie hasła

`Shelly.SetAuth` z gotowym `ha1 = SHA256("<user>:<realm>:<password>")`.
`user` to zawsze `admin`, `realm` to `device_id`.

```js
const crypto = require('crypto');
const ha1 = crypto.createHash('sha256')
  .update(['admin', 'shellymodel-XXXXXXXXXXXX', 'mysecretpassword'].join(':'))
  .digest('hex');
```

### 🟢 Kluczowa różnica względem Sonoffa: `ha2` jest **stałe**

```
ha1      = SHA256("admin:<realm>:<hasło>")
response = SHA256("<ha1>:<nonce>:<nc>:<cnonce>:auth:<ha2>")
```

A `ha2` zależy od transportu — i tu Shelly jest wygodniejszy:

| Transport | `method` | `uri` |
|---|---|---|
| HTTP | faktyczna metoda HTTP (`GET`, `POST`) | faktyczne URI (`/rpc`, `/rpc/Shelly.GetStatus`) |
| **WebSocket RPC** | **`"dummy_method"`** | **`"dummy_uri"`** |
| Pozostałe transporty RPC | `"dummy_method"` | `"dummy_uri"` |

Czyli po WS `ha2 = SHA256("dummy_method:dummy_uri")` — **liczysz raz i cache'ujesz na zawsze**.
Sonoff w tym samym miejscu wymaga `SHA256("Namespace:Metoda")`, czyli przeliczania przy każdym
wywołaniu. Shelly wygrywa.

### Pole `auth` (WebSocket)

| Pole | Typ | Opis |
|---|---|---|
| `realm` | string | `device_id` |
| `username` | string | zawsze `admin` |
| `nonce` | string | z challenge'u |
| `cnonce` | **number** | losowa liczba klienta |
| `nc` | **string** | **8 cyfr hex z wiodącymi zerami**, np. `"00000001"`, `"0000000a"` |
| `response` | string | jak wyżej |
| `algorithm` | string | `"SHA-256"` |

⚠️ Uwaga: u Shelly `nc` to **string z paddingiem**, u Sonoffa **number bez paddingu**.
Drobiazg, który wywali podpis.

### Challenge

Po HTTP w nagłówku `WWW-Authenticate`:

```
WWW-Authenticate: Digest qop="auth", realm="shellypro4pm-f008d1d8b8b8", nonce="AAAAAABn...(base64)...", algorithm=SHA-256
```

Po WS jako błąd 401, gdzie `message` to **zagnieżdżony JSON jako string** (identycznie jak
u Sonoffa):

```json
{"id":1,"src":"shellypro4pm-f008d1d8b8b8","dst":"user_1",
 "error":{"code":401,"message":"{\"auth_type\": \"digest\", \"nonce\": \"AAAAAABn...\", \"realm\": \"shellypro4pm-f008d1d8b8b8\", \"algorithm\": \"SHA-256\"}"}}
```

Pola challenge'u: `auth_type`, `nonce`, `realm`, `algorithm`, opcjonalnie `stale`.
(`nc` w challenge'u było zwracane przez WS **do firmware'u 2.0.0**, potem usunięte.)

### Firmware 2.0.0+ — ponowne użycie nonce'a

To jest realna optymalizacja, warta zaimplementowania:

- **Ważność nonce'a: 1 godzina** od wydania
- **Do 30 000 żądań** na jednym nonce, przez inkrementowanie `nc`
- **Ochrona przed replay:** urządzenie odrzuca `nc`, które nie jest **ściśle większe** od
  poprzednio widzianego dla tego nonce'a
- Po wygaśnięciu/wyczerpaniu → 401 z **`stale=true`** i nowym nonce'em; klient ma wtedy
  pobrać nowy nonce **bez pytania użytkownika o hasło**

Czyli: pełny challenge robisz raz, potem tylko inkrementujesz licznik. Po WS konstruujesz
obiekt `auth` raz i podbijasz `nc`.

Przed 2.0.0: nonce był zwykłą liczbą (`1625053638`), bez śledzenia po stronie serwera,
bez wygasania — każde żądanie HTTP przechodziło pełny cykl challenge-response, a `nc` był
zwykle na sztywno `"00000001"`.

### Zarządzanie nonce'ami — bufor 32 wpisów [DOK]

| Stan | Opis |
|---|---|
| **Pending** | Nonce wydany w 401, klient jeszcze nie odpowiedział |
| **Active** | Klient uwierzytelnił się co najmniej raz |

Polityka wywłaszczania gdy bufor pełny:

1. Najpierw puste sloty
2. Potem najmniej używane sesje aktywne (użyte tylko raz)
3. **Pending są chronione** w pierwszym przebiegu
4. Jeśli nic nie pasuje — **2-sekundowe okno throttlingu**, w którym żądania o nowy nonce
   dostają **HTTP 429**
5. Po oknie: wywłaszczenie slotu o minimalnym użyciu, w razie potrzeby także pending

### Ochrona przed brute-force (fw 2.0.0+) [DOK]

Okno przesuwne **10 minut**, progresywne opóźnienia:

| Nieudanych prób | Opóźnienie |
|---|---|
| 1–10 | brak |
| 11–20 | 10 s |
| 21–30 | 30 s |
| 31–40 | 60 s |
| 40+ | 5 min |

W trakcie throttlingu urządzenie zwraca **429**, nie 401. Udane uwierzytelnienie ze świeżym
nonce'em zeruje liczniki; reboot też.

🔴 **Konsekwencja dla klienta:** jeśli zobaczysz **429**, to nie jest błąd sieci — to znaczy,
że albo za dużo równoległych sesji, albo źle liczysz podpis i wpadłeś w backoff.
Nie retry'ować agresywnie.

### Gotowe implementacje wskazane przez producenta

- przykład NodeJS na GitHubie
- integracja Shelly w Home Assistancie — bardzo dobry przykład klienta WS z autoryzacją,
  w Pythonie (`aioshelly`)

## Kody błędów [DOK]

Krótka lista, w stylu gRPC:

| Kod | Nazwa | Kiedy |
|---|---|---|
| **−103** | INVALID ARGUMENT | Parametry nie pasują do metody (np. brak wymaganego `id`) |
| **−104** | DEADLINE EXCEEDED | Timeout — zwykle `HTTP.GET`/`HTTP.POST` w skryptach |
| **−105** | NOT FOUND | Wskazana instancja nie istnieje (np. `Input.GetConfig` z `id=7`) |
| **−108** | RESOURCE EXHAUSTED | Limit zasobu (np. 21. zadanie harmonogramu przy limicie 20) |
| **−109** | FAILED PRECONDITION | Warunek wstępny niespełniony (overpower, trwający reboot) |
| **−114** | UNAVAILABLE | Usługa niedostępna — czujnik, strefa czasowa, aktualizacja, HTTP w skrypcie |

Plus `401` przy challenge i `429` przy throttlingu.

Porównanie: Sonoff ma 20 kodów w okolicach −128xx, Shelly sześć w okolicach −10x.
**Kody nie są kompatybilne** — mapować osobno.

## Narzędzia

- **`mos`** — CLI MongooseOS, natywne dla tej platformy. `export PORT=http://ip/rpc`
  albo `ws://ip/rpc`, potem `mos --port ${PORT} call Switch.Toggle '{"id":0}'`
- **`curl`** — `--anyauth -u admin:pass` załatwia digest automatycznie
- **`wscat` / `websocat`** — WebSocket z ręki
- **`mosquitto_pub` / `mosquitto_sub`** — MQTT
