# Zigbee: co się „łamie", a co jest standardem publicznym

Spisane 2026-08-17. Pytanie: skoro ludzie na świecie „łamią i deszyfrują" komunikaty
z urządzeń Zigbee, to czy Sonoff i Shelly mają dokumentację, jak gadać z ich Zigbee?

---

## 1. 🔴 Nieporozumienie u źródła: „łamanie" to nie deszyfrowanie

Krążą dwa różne pojęcia, które łatwo pomylić:

| Co ludzie robią | Co to naprawdę jest |
|---|---|
| „łamią szyfrowanie Zigbee" | **prawie nikt tego nie robi** — i nie o to chodzi |
| „reverse-engineering urządzeń Tuya" | odgadywanie **formatu danych**, nie łamanie szyfru |

**Szyfrowanie Zigbee (AES-128) nie jest tym, co się obchodzi.** Twój koordynator
(dongle) dostaje klucz sieciowy przy parowaniu i **legalnie deszyfruje wszystko** —
to normalna praca sieci, nie włamanie. Nikt nie siedzi i nie łamie AES.

To, co społeczność faktycznie robi, to **zgadywanie, co znaczą pola w danych** —
gdy producent użył własnego, nieudokumentowanego formatu. To zupełnie inny problem.

---

## 2. Zigbee to publiczny standard — i to jest sedno

Istnieje **Zigbee Cluster Library (ZCL)** — otwarta, publiczna specyfikacja wydawana
przez Connectivity Standards Alliance (ta sama organizacja, co Matter).

Definiuje standardowe „klastry" — czyli udokumentowane zestawy funkcji:

| Klaster | Co robi | Kod |
|---|---|---|
| On/Off | włącz/wyłącz | 0x0006 |
| Level Control | ściemnianie | 0x0008 |
| Temperature Measurement | temperatura | 0x0402 |
| Electrical Measurement | napięcie, prąd, moc | 0x0B04 |
| Metering | zużycie energii | 0x0702 |
| IAS Zone | czujniki (ruch, otwarcie, dym) | 0x0500 |

🟢 **Jeśli urządzenie trzyma się ZCL, nie ma NIC do łamania ani zgadywania.**
Odczytujesz atrybut 0x0000 z klastra 0x0402 i wiesz, że to temperatura w setnych stopnia.
To jest w publicznej specyfikacji. Zigbee2MQTT obsługuje takie urządzenia **od ręki**,
bez żadnego reverse-engineeringu.

---

## 3. Więc kiedy w ogóle trzeba „zgadywać"? Tylko przy Tuya (i podobnych)

**Tuya wyłamała się ze standardu.** Zamiast używać standardowych klastrów, wsadziła
wszystko do jednego własnego klastra **0xEF00**.

Ten klaster to w praktyce **tunel do chmury Tuya** — upakowuje komendy tak, żeby jej
serwery je parsowały, a nie żeby były czytelne dla kogokolwiek innego. Dane lecą jako
„data pointy" (DP) bez opisu, co który znaczy.

🔴 **I dopiero TO społeczność reverse-engineeruje** — nie szyfrowanie, tylko „DP 1 to
chyba temperatura, DP 4 to chyba tryb". Stąd bierze się cała robota z dodawaniem
konwerterów Tuya do Zigbee2MQTT.

**Wniosek: „łamanie" dotyczy Tuya, bo Tuya złamała standard. Nie dotyczy Zigbee jako
takiego.**

---

## 4. Odpowiedź wprost: Sonoff i Shelly

### Sonoff Zigbee (SNZB-*) — 🟢 standardowy ZCL

Czujniki SNZB trzymają się standardowych klastrów. **Nie potrzebujesz dokumentacji
Sonoffa** — potrzebujesz specyfikacji ZCL, która jest publiczna, i Zigbee2MQTT, który
te urządzenia zna od dawna.

To jest zaleta, nie wada: Sonoff **nie zmusza Cię do swojej dokumentacji, bo używa
wspólnego języka.** Dodatkowo Sonoff publicznie wspiera ścieżkę lokalną (poradniki
o ZBBridge i Home Assistant — [sonoff/08-asortyment.md](sonoff/08-asortyment.md)).

### Shelly Zigbee (linia ZB, nowa) — 🟡 standard + własne dodatki

Ciekawostka, świeża (2026): Shelly wchodzi w Zigbee i robi coś nietypowego. Ich
urządzenia Zigbee mają, obok standardowych klastrów, **własne klastry producenta**:

- **„Shelly RPC Cluster"** — tunelują swoje RPC (to samo API co po WS/HTTP!) **przez
  Zigbee**
- **„Shelly WiFi Setup Cluster"** — konfiguracja Wi-Fi urządzenia po Zigbee

🟢 **Kluczowe: to są rozszerzenia, nie zamiennik.** Podstawowe funkcje (on/off, pomiar)
działają przez standardowe klastry, więc Zigbee2MQTT obsłuży urządzenie **bez** znajomości
klastrów Shelly. Klastry własne dają *dodatkowy* dostęp do bogatszych funkcji — i Shelly
je **dokumentuje** (są opisane w dyskusjach ZHA/Z2M, bo Shelly z tymi projektami
współpracuje).

⚠️ To młoda linia — wsparcie w Z2M/ZHA dopiero dojrzewa, część modeli wymaga zewnętrznych
konwerterów. Stan zmienny, sprawdzaj per model przed zakupem.

---

## 5. Gdzie jest prawdziwa dokumentacja — i dlaczego jej „brak" nie jest problemem

| Źródło | Co opisuje | Dla kogo |
|---|---|---|
| **Specyfikacja ZCL (CSA)** | wszystkie standardowe klastry — [pobieranie](https://csa-iot.org/developer-resource/specifications-download-request/), formularz, bezpłatnie | podstawa, publiczna |
| **Baza urządzeń Zigbee2MQTT** | konkretne modele + co wystawiają | **to czytasz w praktyce** |
| Konwertery Z2M (kod) | jak zmapowano dany model | gdy piszesz własne wsparcie |
| Dokumentacja producenta | rzadko potrzebna dla standardowego ZCL | tylko klastry własne |

🟢 **W praktyce nie czytasz dokumentacji Sonoffa ani Shelly do Zigbee.**
Otwierasz stronę modelu w bazie **zigbee2mqtt.io**, widzisz listę wystawianych wartości
(„temperature", „power", „voltage", „contact") i tyle. Społeczność zrobiła robotę raz,
Ty korzystasz z gotowego.

To jest cała siła ekosystemu Zigbee: **dokumentacja producenta jest opcjonalna, bo język
jest wspólny i społeczność go spisała.**

---

## 6. Konsekwencja dla Twojego produktu

To wzmacnia argument z [strategia-protokoly.md](strategia-protokoly.md):

1. **Kupując Zigbee zgodne z ZCL** (Sonoff SNZB, większość rynku) — masz gotowe wsparcie
   w Z2M, zero reverse-engineeringu, zero zależności od dokumentacji producenta
2. **Unikaj urządzeń Tuya-0xEF00**, jeśli nie chcesz loterii — działają, ale przez
   odgadnięty format, który producent może zmienić firmware'em bez ostrzeżenia
3. **Twój hub gada z Zigbee przez Zigbee2MQTT** (masz już `core/zigbee2mqtt-data/`),
   a Z2M wystawia wszystko przez MQTT do Twojej apki. To jest „południe" z modelu
   północ/południe — bogate, ustandaryzowane, lokalne

### 🎯 Reguła zakupowa

Przy urządzeniu Zigbee pytanie nie brzmi „czy producent ma dokumentację", tylko:

> **„Czy ten model jest w bazie zigbee2mqtt.io i czy używa standardowych klastrów,
> czy Tuya 0xEF00?"**

Standardowe klastry = kupuj bez obaw. Tuya 0xEF00 = działa, ale to inny reżim ryzyka.

---

## 7. Jedno zdanie do zapamiętania

> **Zigbee to publiczny, udokumentowany standard — „łamią" tylko ci producenci (Tuya),
> którzy z niego uciekli. Sonoff SNZB gra standardem, więc nie ma czego łamać; Shelly
> dokłada własne klastry jako bonus, nie jako barierę.**
