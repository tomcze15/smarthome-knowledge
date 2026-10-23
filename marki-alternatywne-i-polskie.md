# Alternatywne marki — ze szczególnym uwzględnieniem Polski

Zebrane 2026-08-17. Kryterium doboru: **lokalne API albo standardowy protokół** —
zgodnie z regułą z [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md).
Nie „popularne marki", tylko „marki, które przejdą test odcięcia WAN".

---

## 🇵🇱 1. Polskie, local-first z założenia — najciekawsze odkrycie

### SUPLA — otwarty system z własnym serwerem

Powstał w **Paczkowie**, działa od ~10 lat. **Open source i open hardware.**

| Cecha | Szczegół |
|---|---|
| Praca lokalna | działa po Wi-Fi 2,4 GHz **bez internetu**; internet tylko do dostępu z zewnątrz |
| Serwer | **można postawić własny** — Raspberry Pi, Arduino, ESP8266 |
| Integracje | **MQTT**, Home Assistant, Google Home, Alexa |
| Kod | publiczny, modyfikowalny |
| Producenci sprzętu | **Zamel** dołączył w pierwszym roku i mocno napędził rozwój |

🟢 **To jest polski, otwarty, lokalny ekosystem z realnym producentem sprzętu za plecami.**
Warto znać choćby po to, żeby wiedzieć, co się dzieje na rynku.

🔴 **Ale uwaga — to także konkurencja, i to kłopotliwa.** Ich hasło przewodnie brzmi
dosłownie **„open smart home system with **no subscription**"**. Czyli komunikują wprost
to, co u Ciebie jest płatne. Patrz sekcja 5.

### BleBox — polskie moduły Wi-Fi z otwartym API

Firma na rynku **od 2013**, produkcja **w UE, głównie w Polsce**.

| Cecha | Szczegół |
|---|---|
| API | **otwarte REST/HTTP**, dokumentacja: `technical.blebox.eu` |
| Praca bez chmury | harmonogramy i zegar astronomiczny **w urządzeniu** — działają bez internetu i bez Wi-Fi |
| Home Assistant | **integracja automatyczna** (oficjalna) |
| Biblioteka | `blebox_uniapi` (Python, GitHub) |

🟢 **Model bardzo podobny do Shelly: moduły dopuszkowe Wi-Fi z udokumentowanym lokalnym
REST API.** Do tego produkcja w Polsce — czyli krótszy łańcuch dostaw, polskie wsparcie,
gwarancja w kraju.

**[DO SPRAWDZENIA]** Czy mają coś na szynę DIN i czy jest pomiar energii.

---

## 🇵🇱 2. Polska aparatura DIN z Modbusem — to jest Twój Alibaba, tylko bliżej

Bezpośrednio do strategii z [strategia-modbus-rs485.md](strategia-modbus-rs485.md).

### F&F (Pabianice)

Liczniki energii z **RS-485 / Modbus RTU**, zdalny odczyt i konfiguracja:

| Model | Co to |
|---|---|
| **LE-01MR** | jednofazowy, 5/100 A, 230 V, **MID**, Modbus RTU RS-485, szyna DIN |
| **LE-03MP** | trójfazowy, RS-485, Modbus RTU |
| **LE-03MQ-CT** | trójfazowy **dwukierunkowy**, pomiar półpośredni, przekładniki, **MID** |

### ORNO

| Model | Co to |
|---|---|
| **OR-WE-516** | trójfazowy **dwukierunkowy**, RS485 Modbus, **MID**, 80 A, 4,5 modułu |

### 🔴 Dlaczego to potencjalnie lepsze niż Alibaba

| | Chiny (Eastron, Acrel…) | **F&F / ORNO** |
|---|---|---|
| Cena jednostkowa | niższa | wyższa |
| **Certyfikat MID** | bywa | **jest** |
| Dokumentacja | angielska, czasem słaba | **polska** |
| Wsparcie techniczne | mail, strefa czasowa +7 h | **telefon, po polsku** |
| Gwarancja i RMA | wysyłka do Chin | **w kraju** |
| Cło, VAT, logistyka | Twój problem | **nie dotyczy** |
| Czas dostawy | tygodnie | **24 h z hurtowni** |
| MOQ przy private label | 500–1000 szt. | **[DO SPRAWDZENIA] — prawdopodobnie negocjowalne** |

🎯 **Wniosek do Twojej strategii ODM:** zanim napiszesz do chińskiej fabryki, **zapytaj
F&F o private label**. Polska firma, ta sama strefa czasowa, ten sam język, **certyfikaty
i zgodność z UE już załatwione**, a przy klasie ryzyka 1 (licznik) to zdejmuje z Ciebie
najtrudniejszą część — dokumentację techniczną i deklarację zgodności.

Może być drożej za sztukę. Ale przy pierwszej partii **liczy się ryzyko, nie cena
jednostkowa** — a tu ryzyko jest o rząd wielkości niższe.

### MID — dlaczego to ważne

Certyfikat **MID** (Measuring Instruments Directive) jest wymagany, jeśli pomiar ma
służyć do **rozliczeń finansowych**. Jeśli kiedyś chciałbyś rozliczać najemców,
podliczniki w budynku albo ładowanie EV — bez MID nie wolno.

**[DEDUKCJA]** Warto brać MID od razu, nawet jeśli dziś nie rozliczasz. Różnica w cenie
jest mała, a otwiera całą kategorię zastosowań B2B.

---

## 🌍 3. Zagraniczne alternatywy warte znania

### Athom — sprzęt z fabrycznie wgranym otwartym firmware

Sprzedają urządzenia **preflashowane** na: **Tasmota, ESPHome, WLED, BTHome,
Zigbee2MQTT**.

🟢 **To rozwiązuje problem, który omawialiśmy przy OpenBeken** — zero lutowania, zero
ryzyka flashowania, zero utraty gwarancji. Kupujesz urządzenie, które **od pierwszego
włączenia jest lokalne i otwarte**.

Dla Twojego produktu: idealny kandydat na listę klasy A i ewentualny private label
(firmware i tak jest otwarty, więc nie ma czego negocjować).

### Bituo Technik — liczniki Zigbee w **standardzie**, nie Tuya

Producent, który **explicite komunikuje się jako „standard Zigbee 3.0, nie Tuya"**
przy licznikach energii. Publikują nawet poradniki porównujące oba podejścia.

🟢 Dokładnie ten typ dostawcy, którego szukasz po lekturze
[zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md) — standardowe klastry ZCL
zamiast tunelowania przez 0xEF00.

### Chińscy producenci DIN/Modbus (z wcześniejszego rozpoznania)

**Eastron** (`SDM120`, `SDM630` — klasyka), **Acrel** (`ADL200`, `ASCB1-63-C32-1P`),
**HOCH**, **TONGOU**, **CHAYO**, **CNC Electric**. Szczegóły i kontakty:
[strategia-modbus-rs485.md](strategia-modbus-rs485.md).

---

## 4. Marki warte sprawdzenia — ⚠️ NIE weryfikowałem

Wymieniam z pamięci jako tropy do własnego researchu. **Nie traktuj tego jako ustaleń** —
przy każdej trzeba sprawdzić, czy faktycznie ma lokalne API:

**Polskie:** Grenton · Ampio (magistrala CAN, rynek instalatorski) · Fibaro (Z-Wave) ·
Tinycontrol (sterowniki LAN) · Lumel (Zielona Góra, mierniki przemysłowe) ·
Apator (liczniki)

**Zagraniczne:** Frient (Dania, Zigbee) · NodOn (Francja) · Homematic IP (Niemcy, lokalna
centrala CCU) · Nous (wersje z Tasmotą)

**[DEDUKCJA]** Ampio i Grenton celują w segment instalatorski premium i są z natury
lokalne (magistrala przewodowa) — ale to inna półka cenowa i inny model sprzedaży
niż Twój B2C.

---

## 🔴 5. Uczciwa uwaga konkurencyjna: SUPLA uderza w Twój model

To najważniejszy wniosek z tej notatki i nie jest przyjemny.

**SUPLA to polski, otwarty, lokalny system smart home, którego hasłem sprzedażowym jest
„bez abonamentu".** Ma za sobą 10 lat rozwoju, społeczność, producenta sprzętu (Zamel)
i dostępność w polskich sklepach.

Czyli w Polsce istnieje już darmowa odpowiedź na pytanie, na które Ty odpowiadasz
płatnie. To wprost dotyka ryzyka opisanego w [ocena-szczera-modelu.md](ocena-szczera-modelu.md),
sekcja o abonamencie.

**Co z tym zrobić — i to jest realne, nie pocieszenie:**

SUPLA i Home Assistant to **narzędzia dla ludzi, którzy chcą sami**. Ty nie sprzedajesz
narzędzia — sprzedajesz **rezultat**: dobrany sprzęt, montaż przez uprawnionego
elektryka, skonfigurowany system, wsparcie gdy przestanie działać, i odpowiedzialność
za całość.

Twoja konkurencja to **nie SUPLA — tylko firmy instalatorskie**, które robią to samo
gorzej i bez local-first.

⚠️ Ale wniosek praktyczny jest twardy: **abonament musi być za usługę i odpowiedzialność,
nie za software.** Jeśli klient poczuje, że płaci co miesiąc za aplikację — porówna
z darmową SUPLĄ i przegrasz to porównanie.

---

## Podsumowanie: trzy rzeczy do zrobienia

1. **Zapytaj F&F o private label licznika** — zanim napiszesz do Chin
2. **Obejrzyj BleBox** jako polski odpowiednik Shelly z otwartym API (`technical.blebox.eu`)
3. **Przeanalizuj SUPLĘ jako konkurencję** i przetestuj swoją narrację sprzedażową
   przeciwko pytaniu „a czemu nie SUPLA za darmo?"
