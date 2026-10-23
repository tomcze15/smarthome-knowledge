# Shelly — przegląd asortymentu

Zebrane 2026-08-17 z `shelly.com` (kategorie sklepu) + dokumentacja Gen2+.
Ceny widoczne w PLN, sklep lokalizuje się na Polskę.

⚠️ **Konkretne parametry (prądy, moce, napięcia) weryfikuj na stronie modelu przed
zakupem.** Producent wypuszcza rewizje (np. „Pro 3EM 120A v2", „Pro 4PM v2"), a poniższa
lista to mapa rodzin, nie karta katalogowa.

---

## Dwie osie podziału

Shelly tnie asortyment **równolegle** według typu produktu i według technologii łączności.
To samo urządzenie funkcjonalnie występuje często w kilku wariantach radiowych.

### Technologie (kolekcje na sklepie)

| Technologia | Co to znaczy dla local-first |
|---|---|
| **Wi-Fi** | Główna linia, pełne Gen2+ RPC API |
| **LAN (Ethernet)** | Seria Pro — **najstabilniejsza**, kabel zamiast radia |
| **Bluetooth (BLU)** | Czujniki bateryjne, wymagają bramki |
| **ZigBee (ZB)** | Nowa linia, warianty czujników BLU |
| **Z-Wave (Wave)** | Osobna linia, wymaga kontrolera Z-Wave |
| **Matter** | Wybrane modele |
| **KNXnet/IP** | Instalacje profesjonalne |

🔴 **Kluczowe dla Ciebie: Gen2+ RPC API dotyczy linii Wi-Fi/LAN.** Linie Wave (Z-Wave)
i czyste BLU/ZB to inne protokoły — tam nie ma `/rpc`, tylko odpowiednio Z-Wave i
Zigbee/BLE przez kontroler.

### Typy produktów

Smart Switches and Dimmers · Sensors and Thermostats · Energy meters · Smart Controllers ·
Smart Plugs · Smart Door Locks · Smart Bulbs · Professional devices · Accessories & Add-ons

---

## Linie generacyjne (Wi-Fi, pełne API)

| Linia | Charakter |
|---|---|
| **Gen4** | Najnowsza — `Shelly 1 Gen4`, `1PM Gen4`, `2PM Gen4`, `EM Gen4`, `EM Mini Gen4`, `1PM Mini Gen4`, `Flood Gen4`, `Flood S Gen4`, `Dimmer 0/1-10V PM Gen4` |
| **Gen3** | `Shelly 1 Gen3`, `1PM Gen3`, `2PM Gen3`, `1PM Mini Gen3`, `PM Mini Gen3`, `EM Gen3`, `3EM-63T Gen3`, `H&T Gen3`, `Plug M Gen3`, `Plug PM Gen3` |
| **Plus** | Starsza Gen2 — `Plus Uni`, `Plus Add-on`, `Plus Smoke Alarm` |
| **Mini** | Miniaturowe moduły dopuszkowe, warianty Gen3/Gen4 |

Wzorzec nazewnictwa, przydatny przy czytaniu katalogu:

- **`PM`** = Power Metering, czyli pomiar mocy
- **`EM`** = Energy Meter, licznik energii (przekładniki prądowe)
- **`1`/`2`/`3`/`4`** = liczba kanałów
- **`Mini`** = wersja miniaturowa do puszki
- **`Pro`** = szyna DIN + Ethernet
- **`Wave`** = Z-Wave
- **`BLU`** = Bluetooth
- **`ZB`** = Zigbee

---

## 🟢 Seria Pro — szyna DIN, Ethernet, pełne API

To jest linia istotna dla rozdzielnicy.

### Przekaźniki

`Shelly Pro 1` (styki bezpotencjałowe) · `Pro 1PM` · `Pro 2PM` · `Pro 3` · `Pro 4PM` ·
`Pro Dual Cover / Shutter PM` · `Pro Dimmer 1PM`

### 🔴 Smart MCB — `Shelly Pro 1CB` / `2CB` / `3CB` / `4CB`

**To jest ważne odkrycie i koryguje to, co napisałem wcześniej o braku sprzętu 32 A.**

To nie jest przekaźnik — to **prawdziwy wyłącznik nadprądowy (MCB) ze zdalnym sterowaniem**.
Cytat ze strony produktu:

> „1-pole miniature circuit breaker (MCB) with remote control capability for single-phase
> installations without Neutral protection, featuring overvoltage and undervoltage
> protection. Available in six rated currents: **10 A, 16 A, 25 A, 32 A, 40 A, and 63 A**,
> with **C tripping curve** and **6 kA breaking capacity**."

Warianty: 1CB (1-polowy), 2CB, 3CB, 4CB. Modele `SPCB-01VENEU` … `SPCB-04V`.
SSID `ShellyPro1CB-XXXXXXXXXXXX`.

Deklarowane funkcje:

- ochrona przed przeciążeniem, zwarciem, **przepięciem i zanikiem napięcia**
- **fizyczny przełącznik blokujący zdalne sterowanie** — istotne dla bezpieczeństwa
- łączność: **Wi-Fi + Bluetooth + Ethernet**
- **repeater Wi-Fi** — retransmituje sygnał
- **skrypty i webhooki**
- **audit log** — wszystkie przełączenia ze znacznikiem czasu i źródłem
- **komponenty wirtualne i KVS** (trwały storage na urządzeniu)
- ciągły monitoring napięcia linii

W dokumentacji API jest odpowiadający komponent **`CB`** — nieczytany, doczytać przed
zakupem.

Cena orientacyjna (sklep PL, 2026-08): 1CB od 349 zł, 4CB 493 zł.

⚠️ **[DO SPRAWDZENIA] Czy `Pro 1CB` mierzy energię, czy tylko napięcie.** Lista funkcji
mówi o „Monitoring. Continuously tracks line voltage" — czyli napięcie na pewno, ale
o pomiarze mocy/energii nie ma słowa. Jeśli potrzebujesz i zabezpieczenia, i pomiaru,
może być konieczne `Pro 1CB` + osobny `Pro EM`.

### Dodatki Pro

`Shelly Pro 3EM Switch Add-on` · `Shelly PRO Modbus Add-on RS485` · `Shelly Plus Add-on` ·
`Shelly Temp. Sensor DS18B20`

---

## Liczniki energii — najbogatsza część oferty

Skoro zbieranie danych jest Twoim celem, to jest sekcja do przejrzenia:

| Model | Charakter |
|---|---|
| `Shelly Pro 3EM 120A v2` | 3-fazowy, przekładniki 120 A |
| `Shelly Pro 3EM-400` | 3-fazowy, do 400 A |
| `Shelly Pro 3EM 3CT 63` | 3-fazowy, 63 A |
| `Shelly Pro EM-50` | |
| `Shelly EM Gen4`, `EM Gen3`, `EM Mini Gen4` | Kompaktowe |
| `Shelly 3EM-63T Gen3` | |

Do tego przekładniki prądowe (CT): **50 A, 80 A, 120 A, 400 A**, w wersjach klipsowych
i z konektorem.

🟢 **Model 3-fazowy z przekładnikami to właściwe narzędzie do pomiaru całego domu** —
zamiast mierzyć obwód po obwodzie. `Pro 3EM` siedzi w rozdzielnicy i widzi wszystko.

---

## Czujniki i sterowanie

**Linia BLU (Bluetooth, bateryjne):** `BLU Button Tough 1` · `BLU Motion` ·
`BLU Door/Window` · `BLU H&T` · `BLU TRV` (głowica termostatyczna) · `BLU Gateway`

**Warianty Zigbee tych samych czujników:** `BLU Motion ZB` · `BLU H&T ZB` ·
`BLU Door/Window ZB` · `BLU H&T Display ZB` · `BLU Button Tough 1 ZB` ·
`BLU Wall Switch 4 ZB Standalone`

**Bezpieczeństwo:** `Plus Smoke Alarm` · `Shelly GAS (LPG)` · `Flood Gen4` / `Flood S Gen4`

**Linia Wave (Z-Wave):** `Wave 1PM`, `Wave 2PM`, `Wave PM Mini`, `Wave Pro 1PM`,
`Wave Pro 2PM`, `Wave Pro Shutter`, `Wave Pro Dimmer 1PM`, `Wave Motion` + warianty LR
(long range)

**Panele:** `Shelly Wall Display X1i` (biały i czarny)

**Rolety:** `Shelly Shutter`, `Pro Dual Cover / Shutter PM`

---

## Ekosystem partnerski

Shelly firmuje też produkty obce: **LOQED** (zamki: Touch Smart Lock, Touch Smart Lock 2s,
Pure, Power Kit), **LinkedGo** (termostaty HVAC i podłogówka), **Zendure** (magazyny
energii, kolekcja „Works with Shelly"), oraz linie `Powered by Shelly` i `Cury by Shelly`.

⚠️ To **nie są** urządzenia z Gen2+ RPC API. Sprzedawane pod parasolem Shelly, ale
integrowane inaczej. Nie zakładaj, że mają `/rpc`.

---

## Zasoby producenta warte znania

| Zasób | Gdzie |
|---|---|
| **Product Catalog PDF** | link „Product Catalog" w stopce — pełny katalog 2026 |
| **Shelly Device Finder** | wyszukiwarka urządzeń wg zastosowania |
| **Developers API** | dokumentacja, którą już mamy w [README.md](README.md) |
| **Scripts Knowledge Base** | baza skryptów on-device |
| **Shelly Academy** | szkolenia i certyfikacja |

---

## Jak wybierać pod local-first

1. **Chcesz pełne API i skrypty?** → linia Wi-Fi/LAN: Gen3, Gen4, Pro. Unikaj Wave
2. **Rozdzielnica?** → seria **Pro** (Ethernet to duży plus — kabel nie gubi pakietów)
3. **Zabezpieczenie obwodu 32 A+?** → **`Pro 1CB` w wariancie 32 A**, nie przekaźnik
4. **Pomiar całego domu?** → `Pro 3EM` z przekładnikami zamiast pomiaru per obwód
5. **Czujniki bateryjne?** → BLU lub BLU ZB; potrzebna bramka albo koordynator Zigbee.
   Uwaga: **czujniki bateryjne śpią** — dokumentacja ma osobną stronę
   `SleepManagementForBatteryDevices` (nieczytana)
