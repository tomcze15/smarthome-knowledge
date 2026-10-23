# Sonoff — przegląd asortymentu

Zebrane 2026-08-17 z drzewa kategorii `help.sonoff.tech` (autorytatywne — tam żyje
dokumentacja każdego modelu).

---

## 🔴 Najważniejsza rzecz: u Sonoffa o local-first decyduje PROTOKÓŁ, nie kategoria

To jest inaczej niż u Shelly, gdzie prawie wszystko Wi-Fi ma to samo API. U Sonoffa
asortyment rozpada się na **cztery światy** o skrajnie różnym stopniu niezależności od chmury:

| Świat | Rozpoznasz po | Local-first |
|---|---|---|
| **Zigbee** | `ZB` w nazwie, `SNZB-*` | 🟢 **najlepszy** — z własnym koordynatorem zero chmury |
| **Matter over Wi-Fi** | `*GS`, `*GSP`, `M5 Matter`, `MINIR4M` | 🟡 sterowanie lokalne, funkcje okrojone |
| **Wi-Fi eWeLink (klasyczne)** | `BASICR4`, `MINIR4`, `POWR*`, `TH*`, `S31` | 🔴 zorientowane na chmurę |
| **Z lokalnym API** | garstka modeli, patrz niżej | 🟢 pełne |

**Wniosek, który zmienia obraz: jeśli chcesz Sonoffa w trybie local-first, kupuj Zigbee.**
Nie Wi-Fi. Zigbee z Twoim własnym koordynatorem (dongle + Zigbee2MQTT lub ZHA) w ogóle nie
dotyka infrastruktury producenta — urządzenie nie ma jak zadzwonić do chmury, bo nie ma Wi-Fi.

W repo jest już `core/zigbee2mqtt-data/` — czyli ta ścieżka jest u Ciebie zaczęta.

---

## Modele z prawdziwym lokalnym API

Pełna lista sekcji „Developer API Protocol" w help center:

| API | Czego dotyczy | Charakter |
|---|---|---|
| **MINI-1GSP API** | MINI-1GSP | 🟢 **nowe JSON-RPC 2.0** — to opisują pliki [02](02-api-rpc-lokalne.md)–[04](04-mqtt-i-home-assistant.md) |
| **SPM-Main HTTP API** | SPM-Main / SPM-4Relay | 🟢 osobne HTTP API, licznik + przekaźniki na szynę |
| **NSPanel Pro API** | Panel centralny | 🟢 |
| **iHost API** | Lokalny hub Sonoffa | 🟢 |
| **DIY MODE API** | BASICR3, RFR3, MINIR2 | 🟡 stare, port 8081 — plik [05](05-diy-mode-legacy.md) |
| **MINIR3 API** | MINIR3 | 🟡 stare |
| **D1 API** | Ściemniacz D1 | 🟡 stare |
| **B02-BL / B05-BL API** | Żarówki | 🟡 stare |

🔴 **To jest cała lista.** Jeśli modelu tu nie ma, zakładaj brak lokalnego API — dokładnie
tak jak wyszło z BASIC-1GSP ([07-offline-i-chmura.md](07-offline-i-chmura.md)).

**`iHost`** zasługuje na uwagę: to własny hub Sonoffa z lokalnym API, pozycjonowany jako
alternatywa dla chmury. Do rozważenia, jeśli chcesz zostać w ekosystemie Sonoffa,
ale bez eWeLink.

---

## Kategorie i modele

### DIY Smart Switches — moduły dopuszkowe i na szynę

**Matter over Wi-Fi (nowa fala):** `BASIC-1GSP` (szyna DIN, 32 A) · `MINI-1GSP` ·
`MINI-2GS` · `MINI-DIM` · `Basic-1GS` (Gen5) · `MINIR4M`

**Zigbee:** `BASIC-ZB1GSP` · `MINI-ZB1GSP` · `MINI-ZB1GP` · `MINI-ZB2GS` · `MINI-ZB2GS-L` ·
`MINI-ZBD` · `MINI-ZBDIM` · `MINI-ZBRBS` · `ZBMINIR2` · `ZBMINIL2` · `ZBMicro`

**Wi-Fi eWeLink:** `BASICR4` · `MINIR4` · `DUALR3` · `DUALR3 Lite` · `RFR2` · `MINI-RBS`
(rolety) · `MINI-D`

**Pomiar i termostaty:** `POWR3` · `POWR316` · `POWR316D/POWR320D` · `POWCT` (przekładnik) ·
`THR316/THR320` · `THR316D/THR320D`

**Na szynę DIN z API:** `SPM-Main` + `SPM-4Relay` — modułowy system pomiarowo-przekaźnikowy
z **własnym HTTP API**. To jedyna alternatywa dla BASIC-1GSP w rozdzielnicy, która ma
udokumentowane lokalne API.

### Bramki i czujniki — tu mieszka Zigbee

**Koordynatory/dongle:** `ZBDongle-E` · `ZBDongle-P` · `Dongle-M` · `Dongle-PMG24` ·
`Dongle-LMG21` · `Dongle-PZG23` · `Dongle-MZG23` · `Dongle-PP10`

**Bramki:** `ZBBridge-U` · `ZB Bridge-P` · `Bridge-M` · **`iHost`**

**Czujniki SNZB:** `SNZB-01P` (przycisk) · `SNZB-02P` / `02D` / `02LD` / `02WD` / `02B` /
`02M` / `02DR2` (temperatura i wilgotność) · `SNZB-03P` / `03PR2` (ruch) · `SNZB-04P` /
`04PR2` (kontaktron) · `SNZB-05P` (zalanie) · `SNZB-06P` / `06P24` (obecność) · `SNZB-09P`

**Jakość powietrza:** `SAWF-07P` (PM2.5) · `SAWF-08P` (CO₂) · `Orb 4-In-1` · `BMT01`

W help center jest osobny poradnik **„How to use ZBBridge-U locally to control your Home"** —
producent sam dokumentuje ścieżkę lokalną dla Zigbee.

### Włączniki ścienne

`M5 Series` i `M5 Matter Series` · `TX Ultimate (T5)` · `TX Gen2` · `ZBM5 Series` (Zigbee) ·
`R5`/`R5W` · `NSPanel` · **seria Fusion** (`Orb-RBS`, `Orb-ZBRBS`, `Orb-ZBW1L`, `Orb-MW1`,
`Orb-ZBW2L`, `Orb-DIM`, `Orb-ZBDIM`, `Orb-ZBW2`, `Orb-MW2`, `Orb-ZBW1`)

### Panele centralne

`NSPanel Pro` · `NSPanel Pro Gen2` — **mają własne API**

### Gniazdka

`S31` / `S31 Lite` · `S40TPB` / `S40TPB Lite` · `S60TPF` / `S60TPG` · `S60ZBTPF` /
`S60ZBTPG` (Zigbee) · `S61STPF` · `S61s Outdoor` · `S41STPB` · `Orb Socket` (Type E / F)

### AGD i kamery

`CAM-B1P` · `CAM-PT2` · `CAM-S2` · `TRVZB` (głowica termostatyczna Zigbee) ·
zawory wody `SWV-*` · `TP-WGZBA`

### Akcesoria

Seria Fusion — ramki i obudowy: `E1GS/E1GSL` · `ERBS` · `E2GS` · `EDIM` · `EWPB`

---

## Wzorce nazewnictwa

| Fragment | Znaczenie |
|---|---|
| **`ZB`** | **Zigbee** — najważniejszy marker dla local-first |
| **`M`** na końcu (`MINIR4M`) | Matter |
| **`GS` / `GSP`** | nowa linia Matter over Wi-Fi; `P` = z pomiarem (Power) |
| **`R2`/`R3`/`R4`** | rewizja sprzętowa |
| **`POW`** | pomiar mocy |
| **`TH`** | termostat / temperatura i wilgotność |
| **`SNZB`** | czujnik Zigbee |
| **`S`** + cyfry | gniazdko |
| **`L`** | wersja bez przewodu neutralnego (no-neutral) |
| **`D`** | wyświetlacz albo ściemniacz, zależnie od rodziny |

---

## Piąta droga: własny firmware

Sonoff ma coś, czego Shelly nie ma — **ogromną scenę alternatywnych firmware'ów**.
Marka wyrobiła sobie nazwę właśnie na tym, że jej sprzęt dawało się przegrać.

| Układ | Firmware |
|---|---|
| ESP8266 / ESP32 (starsze modele) | **Tasmota**, **ESPHome** |
| **BK7231T / BK7231N / BK7238** (Beken, nowsze) | **[OpenBeken](https://github.com/openshwprojects/OpenBK7231T_App)** — zamiennik Tasmoty/ESPHome z MQTT i integracją HA |
| Realtek, BL602, W800, XR809, LN882H | też OpenBeken |

Istnieje udokumentowany przypadek konwersji **Sonoffa S61STPF z firmware'u Matter na
OpenBeken** na układzie BK7238 — narzędziem `BK7231GUIFlashTool`, typ układu „BK7238 (T1)".

To zmienia rachunek: nawet model bez lokalnego API można teoretycznie uwolnić.

⚠️ Ale realistycznie:

- wymaga **fizycznego dostępu i zwykle lutowania UART** — inaczej niż flashowanie
  ESP przez OTA
- relacje z forum: „success in the flasher does not guarantee a bootable runtime image
  on BK7238" — potrafi się nie udać
- **traci się gwarancję i certyfikaty**
- przy sprzęcie na 32 A wpiętym na stałe w rozdzielnicę to zła relacja ryzyka do zysku

[DEDUKCJA] Sensowne przy tanich wtyczkach i modułach dopuszkowych. Bez sensu przy
zabezpieczeniach obwodów siłowych.

---

## Jak wybierać pod local-first

1. **Domyślnie: Zigbee.** `ZB*` / `SNZB*` + własny dongle + Zigbee2MQTT. Zero chmury,
   zero zależności od producenta, urządzenia bateryjne działają latami
2. **Potrzebujesz Wi-Fi z API?** → tylko `MINI-1GSP` albo `SPM-Main`/`SPM-4Relay`.
   Reszta Wi-Fi to chmura
3. **Rozdzielnica z API?** → `SPM-Main` + `SPM-4Relay` (HTTP API), **nie** `BASIC-1GSP`
4. **Chcesz hub Sonoffa zamiast eWeLink?** → `iHost` ma lokalne API
5. **Matter** traktuj jako minimum wspólne: włącz/wyłącz działa lokalnie, ale reszty
   funkcji nie dostaniesz

⚠️ **Reguła weryfikacji przed każdym zakupem Wi-Fi:** sprawdź na stronie produktu obecność
haseł „Local Web Console", „Open API Interfaces", „JSON-RPC 2.0". Brak = brak lokalnego API,
niezależnie od tego, co sugeruje wspólna dokumentacja w help center.
