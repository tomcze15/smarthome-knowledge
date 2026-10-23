# Shelly — notatki

Zebrane 2026-08-17 bezpośrednio z `shelly-api-docs.shelly.cloud` (czytane ze stron, nie ze
streszczeń). Wersja dokumentacji: **1.0** (Gen2+), stan firmware'u odnoszony do **2.0.0**.

## Spis

| Plik | O czym |
|---|---|
| [01-generacje-i-modele.md](01-generacje-i-modele.md) | Gen1 vs Gen2+/3/4, wykrywanie urządzeń przez mDNS, profile |
| [02-api-rpc-gen2.md](02-api-rpc-gen2.md) | **Rdzeń.** JSON-RPC 2.0, cztery kanały, autoryzacja digest, kody błędów |
| [03-komponenty.md](03-komponenty.md) | Koncepcja komponentu, pełna lista, `Shelly.*`, `Switch.*`, `PM1.*`, notyfikacje |
| [04-mqtt.md](04-mqtt.md) | Konfiguracja, topiki, interfejs sterowania, pułapka z rebootem |
| [05-skrypty-on-device.md](05-skrypty-on-device.md) | JavaScript **na urządzeniu** — to jest przewaga Shelly nad resztą |
| [06-gen1-legacy.md](06-gen1-legacy.md) | Stare API REST + CoIoT, żeby rozpoznać w poradnikach |
| [07-porownanie-sonoff.md](07-porownanie-sonoff.md) | Shelly vs Sonoff — kto od kogo zżynał i co z tego wynika |
| [08-plan-integracji-js.md](08-plan-integracji-js.md) | Wspólna warstwa dla obu producentów, szkice kodu |
| [09-offline-i-chmura.md](09-offline-i-chmura.md) | Praca bez internetu, wyłączenie chmury, sprzęt na szynę DIN |
| [10-asortyment.md](10-asortyment.md) | **Cała gama** — linie Gen3/Gen4/Pro/BLU/Wave, liczniki energii, smart MCB |
| [11-moduly-dopuszkowe-wybor.md](11-moduly-dopuszkowe-wybor.md) | Który moduł dopuszkowy wybrać: 1 / 1 Mini / 1PM / 2PM, Gen3 vs Gen4, przewód N i głębokość puszki |

Folder siostrzany: [`../sonoff/`](../sonoff/) — te same tematy dla BASIC-1GSP.

## Legenda pewności

- **[DOK]** — wprost z dokumentacji, przepisane z konkretnej strony
- **[DEDUKCJA]** — mój wniosek, logiczny ale niepotwierdzony
- **[DO SPRAWDZENIA]** — do weryfikacji na sprzęcie

## Najważniejsza obserwacja

**Sonoff skopiował API Shelly.** Nie „zainspirował się" — skopiował strukturę ramek, nazwy
metod (`GetStatus` / `SetConfig` / `GetConfig`), koncepcję komponentów, `NotifyStatus` /
`NotifyEvent`, układ topików MQTT (`<id>/rpc`, `<src>/rpc`, `<id>/events/rpc`, `<id>/online`),
a nawet mechanizm digest SHA-256 i konstrukcję pola `auth`. Nagłówek `Server: ShellyHTTP/1.0.0`
u Shelly, `Server: SonoffHTTP/1.0.0` u Sonoffa.

**Praktyczny wniosek: jedna warstwa abstrakcji obsłuży oba producenty.** Różnice są realne, ale
policzalne — zestawione w [07-porownanie-sonoff.md](07-porownanie-sonoff.md). Shelly jest przy
tym stroną dojrzalszą: więcej funkcji, lepsza dokumentacja, dłuższa historia.

## Shelly ma oficjalny serwer MCP dla dokumentacji

Warte uwagi przy tym, jak pracuję. **Beta**, ale działa bez instalacji — remote MCP przez
Streamable HTTP:

```
https://shelly-api-docs.mcp.shelly.link/mcp
```

Osiem narzędzi: `search_docs`, `search_changelog`, `get_document`, `get_api_method`,
`get_script_api`, `list_devices`, `list_methods`, `get_example`. Plus zasoby `shelly://docs`,
`shelly://methods`, `shelly://devices`, `shelly://components` i cztery gotowe prompty.

`get_example` generuje kod w czterech formatach: HTTP URL, curl, mos i **Shelly Script**.
`get_script_api` opisuje API dostępne wewnątrz skryptów na urządzeniu.

Jeśli zamierzasz robić coś większego na Shelly — podpięcie tego pod Claude Code oszczędzi
mnóstwo klikania po dokumentacji. Konfiguracja stoi na stronie `/mcp/`.

## Struktura dokumentacji

Pięć osobnych API pod jedną domeną:

| Sekcja | Ścieżka | Co to |
|---|---|---|
| Gen 1 Device API | `/gen1/` | Stare urządzenia, REST + CoIoT |
| **Gen 2+ Device API** | `/gen2/` | ← **to nas interesuje**, lokalne RPC |
| BLU Devices | `/docs-ble/` | Urządzenia Bluetooth |
| Integrator API | `/integrator-api/` | Cloud-to-cloud |
| Cloud Control API | `/cloud-control-api/` | Sterowanie przez chmurę |

Gen2 ma wersjonowanie dokumentacji: aktualna `1.0`, archiwalna `0.14`.

## Strony przeczytane w całości

`gen2/General/`: `Authentication`, `RPCChannels`, `Notifications`, `mDNS`, `CommonErrors`
`gen2/ComponentsAndServices/`: `Switch`, `Mqtt`, `Shelly`, `PM1`, strona wprowadzająca
`gen2/Scripts/Overview`, `/mcp/`, indeks Gen1 (streszczony)

**Nieprzeczytane, do doczytania gdy będą potrzebne:** `RPCProtocol`, `ComponentConcept`,
`LocalNetworkMessaging`, `IPv6`, `SafeMode`, `SleepManagementForBatteryDevices`,
`DebugLogs`, `CustomHTTPSCertificates`, `gen1Compatibility`, `DynamicComponents`,
oraz większość komponentów (`Cover`, `EM`, `EMData`, `KVS`, `Webhook`, `Schedule`,
`Modbus`, `Matter`, `Zigbee`, `BLE`, `Script`, `Sys`, `WiFi`, `HTTP`…).
