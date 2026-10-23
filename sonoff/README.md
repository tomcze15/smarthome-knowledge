# Sonoff — notatki

Zebrane 2026-08-17 bezpośrednio z `help.sonoff.tech` (czytane ze stron, nie ze streszczeń).
Punkt wyjścia: chcę zbierać dane i sterować **SONOFF BASIC-1GSP** z własnej apki w JS/TS.

## Spis

| Plik | O czym |
|---|---|
| [01-basic-1gsp-urzadzenie.md](01-basic-1gsp-urzadzenie.md) | Co to za sprzęt, specyfikacja, przyciski, diody, funkcje w eWeLink |
| [02-api-rpc-lokalne.md](02-api-rpc-lokalne.md) | **Najważniejsze.** JSON-RPC 2.0: ramki, kanały HTTP/WS/MQTT, autoryzacja digest SHA-256, kody błędów |
| [03-komponenty-switch-meter.md](03-komponenty-switch-meter.md) | `Switch.*` i `Meter.*` — metody, pola, jednostki, przykłady JSON |
| [04-mqtt-i-home-assistant.md](04-mqtt-i-home-assistant.md) | Konfiguracja brokera z poziomu urządzenia, topiki, autodiscovery HA |
| [05-diy-mode-legacy.md](05-diy-mode-legacy.md) | Stare DIY API (port 8081, mDNS) — inna rodzina, żeby nie pomylić |
| [06-plan-integracji-js.md](06-plan-integracji-js.md) | Jak to spiąć z `~/smarthome/core`, szkice kodu, kolejność kroków |
| [07-offline-i-chmura.md](07-offline-i-chmura.md) | 🔴 **CZYTAJ NAJPIERW.** Które modele naprawdę mają to API, co działa bez chmury |
| [08-asortyment.md](08-asortyment.md) | **Cała gama** — cztery światy protokołów, pełna lista modeli z lokalnym API |
| [09-odm-partnership.md](09-odm-partnership.md) | Program ODM — modele współpracy, kto za tym stoi, o co pytać przed podpisem |

## Legenda pewności

W notatkach oznaczam:

- **[DOK]** — wprost z dokumentacji Sonoffa, przepisane z konkretnej strony
- **[DEDUKCJA]** — mój wniosek, logiczny ale niepotwierdzony
- **[DO SPRAWDZENIA]** — trzeba zweryfikować na sprzęcie

## 🔴 Największa niewiadoma — ROZSTRZYGNIĘTA 2026-08-17, negatywnie

**Wszystko wskazuje, że BASIC-1GSP tego API nie ma.** Pełne uzasadnienie z cytatami:
[07-offline-i-chmura.md](07-offline-i-chmura.md).

Skrót dowodów:

1. Strona produktowa **MINI-1GSP** reklamuje wprost „Local Web Console", „Open API
   Interfaces", „JSON-RPC 2.0 via HTTP, WebSocket, and MQTT" i „Home Assistant Integration
   via MQTT Discovery". Strona **BASIC-1GSP** nie wspomina o żadnej z tych rzeczy
2. Zgłoszenie [SonoffLAN#1820](https://github.com/AlexxIT/SonoffLAN/issues/1820): na
   BASIC-1GSP czujniki mocy, prądu i napięcia **zamarzają, gdy zamkniesz apkę eWeLink** —
   dane pomiarowe najwyraźniej idą push-em z chmury, nie lokalnie
3. Dokumentacja pokazuje `"model": "SN-ESP32C6-MINI1GSP-01"` (**ESP32-C6**), a karta
   BASIC-1GSP podaje **BK7238**. Dwa różne układy, prawdopodobnie dwie linie firmware'u

**Dokumentacja pod `Developer API Protocol` opisuje MINI-1GSP i nie należy zakładać, że
stosuje się do innych modeli.** Nazwa sekcji nie jest przypadkowa.

Weryfikacja na sprzęcie nadal warta wykonania (jedna komenda —
[06-plan-integracji-js.md](06-plan-integracji-js.md), krok 1), ale spodziewaj się 404.

**Plan B dla BASIC-1GSP: Matter.** Urządzenie da się sparować z Home Assistantem
bezpośrednio kodem QR, bez aplikacji eWeLink. Dostajesz on/off i Power On State; pomiar
energii przez Matter deklarowany dla HA i SmartThings — [DO SPRAWDZENIA] czy działa
niezawodnie i lokalnie.

## Źródła

Wszystkie pod `https://help.sonoff.tech/docs/`:

- `BASIC-1GSP` — instrukcja urządzenia
- `puU2rU4w` — indeks „Developer API Protocol"
- `7shYNoAi` — indeks „MINI-1GSP API"
- `API_QuickStart`, `API_RPCProtocol`, `API_RPCChannels`, `API_Notifications`, `API_Authentication`, `API_ComponentConcept`, `API_ErrorCode`, `API_Debuglogs`
- `API_System`, `API_Sonoff`, `API_Cloud`, `API_Input`, `API_Matter`, `API_Meter`, `API_MQTT`, `API_Ota`, `API_Switch`, `API_Timer`, `API_WebSocket`, `API_WiFi`, `API_Changelog`
- `DIY-MODE-API-PROTOCOL` — stare API
- `SPMMain-HTTP-API`, `MINIR3-API`, `D1-API`, `BASICR3-RFR3-MINIR2-API` — inne rodziny

Strony mają stopkę „Last modified: 2026-08-14" — dokumentacja jest świeża i aktywnie rozwijana, warto zerknąć na `API_Changelog` przed większą robotą.
