# Czy Sonoff działa offline i czy dostanę lokalne API?

Ustalone 2026-08-17 na podstawie stron produktowych `sonoff.tech`, help center i zgłoszeń
użytkowników. To jest **rozstrzygnięcie niewiadomej** z [README.md](README.md).

---

## 🔴 Wniosek: MINI-1GSP ≠ BASIC-1GSP. To nie to samo urządzenie pod względem API.

Porównanie sekcji marketingowych na oficjalnych stronach produktowych — **to samo źródło,
ten sam producent, ta sama data**:

| Deklaracja na stronie produktu | MINI-1GSP | BASIC-1GSP |
|---|---|---|
| „Local Web Console" — dostęp przez przeglądarkę bez apki i bez chmury | ✅ **jest** | ❌ **brak** |
| „Open API Interfaces" — HTTP, WebSocket, MQTT | ✅ **jest** | ❌ **brak** |
| „Supports JSON-RPC 2.0 via HTTP, WebSocket, and MQTT" | ✅ **jest** | ❌ **brak** |
| „Home Assistant Integration via MQTT Discovery" | ✅ **jest** | ❌ **brak** |
| Integracja z Home Assistant / OpenHAB / ioBroker / Node-RED | ✅ **jest** | ❌ **brak** |
| „Local Control does not Depend on the Server" | — | ✅ jest |
| Matter | ✅ | ✅ |

**Brak tych deklaracji na stronie BASIC-1GSP nie jest przeoczeniem.** Producent wymienia je
prominentnie przy modelu, który je ma. Przy BASIC-1GSP jedyne zdanie o pracy lokalnej brzmi:

> „Local Control does not Depend on the Server — Even without Internet access, SONOFF Basic
> DIN Rail can be used with local control."

To odnosi się do **trybu LAN aplikacji eWeLink i do Matter**, nie do API dla programisty.

### Dowód potwierdzający z praktyki

Zgłoszenie [AlexxIT/SonoffLAN#1820](https://github.com/AlexxIT/SonoffLAN/issues/1820) —
użytkownik z BASIC-1GSP (firmware v1.0.4), Home Assistant 2026.6.x:

- czujniki **mocy, prądu i napięcia w ogóle się nie aktualizują** przez SonoffLAN
- wartości odmrażają się **tylko gdy aplikacja eWeLink jest otwarta na telefonie**
- po zamknięciu apki odczyty zamarzają

Hipoteza autora integracji: BASIC-1GSP **nie wspiera lokalnego protokołu LAN dla danych
pomiarowych** i polega wyłącznie na zdarzeniach push z chmury. Urządzenie ma „completely
different internal communication stack" niż klasyczne eWeLink.

### Dowód poszlakowy: inny układ

Dokumentacja API pokazuje `"model": "SN-ESP32C6-MINI1GSP-01"` → **ESP32-C6**.
Karta BASIC-1GSP: **BK7238** (Beken). Dwa różne układy, prawdopodobnie dwie różne linie
firmware'u. Patrz [README.md](README.md).

---

## Co realnie dostaniesz kupując BASIC-1GSP

### ✅ Działa lokalnie, bez chmury i bez konta

- **Przekaźnik działa bez sieci.** Przycisk fizyczny załącza i wyłącza obwód niezależnie
  od Wi-Fi
- **Matter bez eWeLink** — można zeskanować kod QR Matter z instrukcji lub obudowy i dodać
  urządzenie **bezpośrednio do Home Assistanta**, z pominięciem aplikacji producenta.
  Matter jest protokołem lokalnym z założenia
- Po sparowaniu przez Matter w HA pojawia się **on/off oraz Power On State**
- Karta produktu deklaruje pomiar energii przez Matter dla **Home Assistant i SmartThings**
  (dla Apple Home, Google Home i Alexa — nie)

### ❌ Czego nie dostaniesz

- **Lokalnego JSON-RPC** (`/rpc`) — wszystko wskazuje, że go nie ma
- **MQTT bezpośrednio z urządzenia** — brak deklaracji, brak `MQTT.SetConfig`
- **Autodiscovery MQTT do HA** — to funkcja MINI-1GSP
- **Historii energii przez API** (`Meter.GetRecords` / `Download`) — 180 dni historii żyje
  w aplikacji eWeLink
- **OTA firmware bez chmury** — aktualizacje tylko przez eWeLink [wg relacji użytkowników]

### ⚠️ Największy problem dla Twojego przypadku użycia

**Dane pomiarowe to dokładnie to, co chcesz zbierać — i to właśnie one wydają się zależeć
od chmury.** Sterowanie on/off przez Matter działa lokalnie, ale strumień mocy/napięcia/prądu
przez SonoffLAN zamarza bez otwartej apki.

[DO SPRAWDZENIA] Czy pomiar energii **przez Matter** (nie SonoffLAN) w Home Assistancie
aktualizuje się lokalnie i niezawodnie. Karta produktu twierdzi, że tak. Zgłoszenie #1820
dotyczyło ścieżki SonoffLAN, nie Matter. To dwie różne drogi i nie należy ich mylić.
**To jedyna rzecz, która mogłaby uratować ten model do zbierania danych.**

---

## MINI-1GSP — tu jest wszystko, czego szukasz

Cytaty wprost ze strony produktu:

> „**Local Web Console** — Access your device directly through a browser-based local
> interface **without installing an app or connecting to the cloud**. View energy data and
> manage your device in real time while keeping your data private and secure."

> „**Open API Interfaces** — Access device data and functions through HTTP, WebSocket, MQTT,
> and open API interfaces, and seamlessly integrate your devices with third-party platforms
> such as Home Assistant, OpenHAB, ioBroker, and Node-RED."

> „Supports **JSON-RPC 2.0** via HTTP, WebSocket, and MQTT for accessing device data and
> control functions."

I najważniejsze zdanie, wprost odpowiadające na pytanie o offline:

> „**In a LAN-only environment, the device's local web interface and Home Assistant
> (via MQTT integration) still synchronize data in real time.**"

Plus: „In eWeLink, when the device is offline, energy data will continue to be **stored
locally**. Once the device comes back online, the local data will be re-synchronized."

Do tego dokumentacja QuickStart mówi, że pierwsza konfiguracja idzie przez **tryb AP
i przeglądarkę**, „without relying on other apps or ecosystems".

**MINI-1GSP to jedyny model Sonoffa, dla którego cała reszta tych notatek jest potwierdzona.**

Ograniczenie: to nie jest sprzęt na szynę DIN i nie ma 32 A — to moduł dopuszkowy.

---

## Reguła na przyszłość przy zakupach Sonoffa

Przed kupnem sprawdź na stronie produktu, czy występują **dokładnie te trzy hasła**:

1. **Local Web Console**
2. **Open API Interfaces**
3. **JSON-RPC 2.0**

Jeśli ich nie ma — zakładaj, że lokalnego RPC nie ma, niezależnie od tego, co sugeruje
wspólna dokumentacja w help center. Dokumentacja jest firmowana jako „MINI-1GSP API"
i najwyraźniej **dotyczy tylko wybranych modeli**.

Sam link „API for Developers" w stopce sonoff.tech prowadzi do `docs/7shYNoAi`, czyli
do indeksu MINI-1GSP API — ale to element globalny strony, obecny także przy produktach
bez tego API. **Nie jest dowodem na wsparcie.**
