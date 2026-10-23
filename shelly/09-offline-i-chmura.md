# Czy Shelly działa offline i czy dostanę lokalne API?

Ustalone 2026-08-17 na podstawie dokumentacji Gen2+ i oficjalnego support center Shelly.

## Krótka odpowiedź: tak, bez zastrzeżeń

**Lokalne API nie jest u Shelly funkcją dodatkową — jest podstawowym interfejsem
urządzenia.** Wbudowany panel WWW urządzenia sam korzysta z tego samego RPC po WebSocket
(dokumentacja mówi o tym wprost przy opisie kanału WS). Nie ma trybu, który trzeba
odblokowywać, ani konta, które trzeba założyć.

## Cztery poziomy niezależności

| Poziom | Czy działa bez internetu |
|---|---|
| **Przekaźnik jako taki** | ✅ Wejście fizyczne steruje wyjściem niezależnie od sieci (`in_mode: follow`/`flip`) |
| **Panel WWW + lokalne RPC** | ✅ Wpisujesz IP w przeglądarkę, wszystko działa w LAN |
| **Twoja apka po WS / MQTT** | ✅ Broker stoi u Ciebie, chmura niepotrzebna |
| **Aktualizacje firmware** | ❌ Wymaga internetu (`Shelly.CheckForUpdate` / `Update`) |

Oficjalny support Shelly potwierdza:

> „access and control them locally through the embedded web server of each device.
> Simply type the IP address of the Shelly device into your web browser"

> „you can utilize MQTT protocol to control your devices through an MQTT broker"

## Chmurę można wyłączyć całkowicie

Komponent **`Cloud`** ma `SetConfig` z flagą `enable`. Ustawiasz `false` i urządzenie
przestaje się łączyć z serwerami producenta. Reszta funkcji działa bez zmian.

## ⚠️ Na co uważać

### 1. Okno provisioningu (firmware 1.7.5+)

Świeże urządzenie ma **ograniczony czas na pierwszą konfigurację** — szczegóły
w [01-generacje-i-modele.md](01-generacje-i-modele.md).

Dobra wiadomość: przejście `pending` → `confirmed` wyzwala **„first IP acquisition over
Wi-Fi/Ethernet"**, czyli wystarczy **sieć lokalna, nie internet**. LAN bez internetu
w zupełności wystarcza.

Zła wiadomość: jeśli rozpakujesz urządzenie i zostawisz na tygodnie bez konfiguracji,
wpadnie w stan **`locked`** — AP i BLE RPC wyłączone do czasu przepięcia zasilania,
resetu albo wciśnięcia przycisku. Nie jest to nieodwracalne, ale zaskakuje.

### 2. Zegar a harmonogramy

Funkcje zależne od czasu (`Schedule`, timery kalendarzowe) wymagają zsynchronizowanego
zegara. Bez internetu trzeba wskazać **lokalny serwer NTP** w konfiguracji komponentu `Sys`
[DEDUKCJA — pole NTP w `Sys` nie było przeze mnie czytane, ale komponent `Sys` obsługuje
konfigurację czasu; sprawdzić].

Dotyczy to też `aenergy.by_minute`, które wg dokumentacji istnieje **tylko przy
zsynchronizowanym zegarze**.

### 3. Aplikacja producenta a tryb lokalny

Shelly Smart Control ma osobne ustawienie *Settings → App Settings → Local Wi-Fi Network
Control*. Włączone = telefon gada z urządzeniami bezpośrednio po LAN. Ale funkcje chmurowe
(zdalny dostęp spoza domu, powiadomienia push, automatyzacje po stronie chmury) wymagają
internetu.

Dla Twojego przypadku to bez znaczenia — apka producenta nie jest w ogóle potrzebna.

## Pierwsze uruchomienie bez konta

Standardowa procedura Gen2+:

1. Zasilasz urządzenie → wystawia własny AP
2. Łączysz się z jego siecią
3. Wchodzisz na wbudowany panel WWW
4. Podajesz dane swojego Wi-Fi
5. Urządzenie dołącza do LAN, znajdujesz IP w routerze albo przez mDNS `_shelly._tcp`
6. `Shelly.SetAuth` → ustawiasz hasło
7. Gotowe, RPC działa

Żadnego konta, żadnej rejestracji, żadnej aplikacji.

## Sprzęt na szynę DIN

> ⚠️ **KOREKTA 2026-08-17.** Wcześniej napisałem tu, że „Shelly nie ma sprzętu DIN
> na 32 A". **To było błędne** — przeoczyłem serię `Pro CB`. Pełny asortyment:
> [10-asortyment.md](10-asortyment.md).

Seria **Pro** montuje się na szynie DIN, ma Ethernet obok Wi-Fi i pełne Gen2+ API
ze skryptami.

**Przekaźniki:** `Pro 1` (styki bezpotencjałowe), `Pro 1PM`, `Pro 2PM`, `Pro 3`, `Pro 4PM` —
te faktycznie operują w okolicach 16 A na kanał.

**Wyłączniki nadprądowe `Pro 1CB` / `2CB` / `3CB` / `4CB`** — to nie przekaźnik, tylko
prawdziwy **MCB ze zdalnym sterowaniem**, dostępny w wariantach **10, 16, 25, 32, 40
i 63 A**, charakterystyka C, zdolność zwarciowa 6 kA. Do tego ochrona przepięciowa
i podnapięciowa, fizyczny przełącznik blokujący zdalne sterowanie, audit log, skrypty, KVS.

🟢 **To zamyka lukę wobec BASIC-1GSP — i to z nawiązką, bo zastępuje wyłącznik
w rozdzielnicy zamiast siedzieć za nim.**

[DO SPRAWDZENIA] Czy `Pro CB` mierzy energię, czy tylko napięcie linii. Jeśli tylko
napięcie, do pomiaru trzeba dołożyć `Pro EM` albo `Pro 3EM`.

⚠️ Prądy przekaźników pochodzą ze stron sklepowych, nie z kart katalogowych —
zweryfikuj przed zakupem. Producent zmienia rewizje (np. „Pro 4PM v2").
