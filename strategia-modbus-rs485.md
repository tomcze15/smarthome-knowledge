# Modbus RTU / RS485 — przeoczona ścieżka dla rozdzielnicy i własnej marki

Powstało 2026-08-17 z pytania: „czy da się znaleźć urządzenie DIN, które nie gada
przez Wi-Fi/Matter/Zigbee, tylko udostępnia sensowne API — i czy da się je zrobić
pod własną marką".

Uzupełnia [strategia-protokoly.md](strategia-protokoly.md)
i [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md).

---

## 1. Doprecyzowanie pojęć: API potrzebuje transportu

„Urządzenie z API, bez Wi-Fi i bez Zigbee" brzmi jak sprzeczność, ale nie jest —
**API to warstwa aplikacji, transport to warstwa niżej.** Opcje dla sprzętu na szynę DIN:

| Transport | API | Uwagi |
|---|---|---|
| Wi-Fi (IP) | HTTP / WS / MQTT | to robi Shelly; jest droga do chmury |
| **Ethernet (IP)** | HTTP / WS / MQTT / Modbus TCP | przewód, brak radia, pełne API |
| **RS485 (szeregowy)** | **Modbus RTU** | **przewód, ZERO stosu sieciowego** |
| Zigbee / Thread (802.15.4) | model atrybutów | radio, ale bez IP |

🎯 **RS485 + Modbus RTU to odpowiedź na Twoje pytanie.** Urządzenie nie ma żadnego stosu
sieciowego — nie zna pojęcia adresu IP, nie umie wysłać pakietu do internetu nawet
teoretycznie.

---

## 2. Dlaczego to najmocniejsza gwarancja local-first, jaka istnieje

Hierarchia gwarancji, od najsłabszej:

| Poziom | Gwarancja | Czym poparta |
|---|---|---|
| Wi-Fi + firmware producenta | obietnica | dobrą wolą producenta |
| Matter over Wi-Fi | ścieżka sterowania lokalna | protokołem — ale radio zostaje |
| Zigbee / Thread | brak Wi-Fi | fizyką radia |
| **Modbus RTU / RS485** | **brak jakiejkolwiek sieci** | **fizyką — nie ma czym wysłać** |

**Urządzenie Modbus RTU nie może zadzwonić do chmury, bo nie ma stosu TCP/IP.**
To nie jest kwestia konfiguracji ani firewalla — po prostu nie istnieje mechanizm.

---

## 3. Argument techniczny, o którym się zapomina: rozdzielnica to klatka Faradaya

🔴 **Rozdzielnice bywają metalowe.** Metalowa obudowa tłumi 2,4 GHz — a na tym paśmie
siedzi **i Wi-Fi, i Zigbee, i Thread**.

Czyli w tym jednym miejscu instalacji **przewodowa magistrala jest technicznie lepsza
od każdego radia**, niezależnie od filozofii local-first:

- brak problemów z zasięgiem w metalowej skrzynce
- brak konkurencji o czas antenowy z Wi-Fi klienta
- brak zależności od routera operatora (Twoje ryzyko z pakietu BYON!)
- jedna magistrala, wiele urządzeń (typowo do 32 adresów na segment)
- odporność przemysłowa, kable robią setki metrów

**[DEDUKCJA]** Dla Twojego pakietu BASIC to szczególnie istotne: rozdzielnica na Modbusie
w ogóle nie dotyka Wi-Fi klienta, więc znika cała klasa problemów z routerem operatora.

---

## 4. 🔴 Dlaczego nie znalazłeś tego na Alibabie

**Bo szukałeś w kategorii „smart home", a to jest towar z branży „elektrotechnika
przemysłowa i pomiary energii".** To dwa różne światy, dwa różne zestawy dostawców,
dwa różne słownictwo.

### Zapytania, które działają

```
DIN rail energy meter Modbus RTU RS485
single phase kWh meter RS485 Modbus DIN
smart circuit breaker RS485 Modbus
Modbus RTU relay module DIN rail
intelligent miniature circuit breaker RS485
reclosing circuit breaker RS485
```

### Zapytania, które Cię zwodziły

```
smart home DIN rail          ← trafiasz na Sonoff/Tuya i chmurę
wifi din rail switch         ← to samo
matter din rail              ← wąska, nowa nisza
```

### Producenci, którzy realnie istnieją

Sprawdzone 2026-08-17, wszystkie mają strony B2B:

| Producent | Co robi | ODM/OEM |
|---|---|---|
| **Eastron** | Klasyka: `SDM120`, `SDM220-MT`, `SDM230`, `SDM630`, wersje CT i PV | tak, „custom supplier" |
| **Acrel** | `ADL200`, `ADL100-ET` (liczniki), **`ASCB1-63-C32-1P`** (smart MCB!) | tak, duży producent |
| **HOCH** | wyłączniki RS485/Modbus, ~30 lat na rynku | **tak, wprost** |
| **TONGOU** | smart MCB: Tuya, eWeLink, Wi-Fi, Zigbee, 4G, **RS485** | **tak, „OEM/ODM"** |
| **CHAYO** | smart circuit breaker, Tuya/Zigbee/RS485 | **tak, wprost OEM** |
| **CNC Electric** | `YCB9ZF`, 32 A, RS485/Modbus/MQTT | tak |
| Changzhou ZERO | elektroniczne MCB z Modbusem | — |
| Yueqing Long Road | Wi-Fi + RS485 Modbus, 2-polowe MCB | — |

🟢 **`Acrel ASCB1-63-C32-1P` to bezpośredni odpowiednik `Shelly Pro 1CB C32`**, tylko
z magistrali przemysłowej zamiast Wi-Fi.

**Region:** większość tych firm siedzi w **Yueqing / Wenzhou** (prowincja Zhejiang) —
to światowy klaster aparatury elektrycznej. Tam się jedzie albo tam się pisze.

---

## 5. 🎯 Dlaczego to jest DUŻO łatwiejsza droga do własnej marki

Porównanie własnego produktu w dwóch wariantach:

| | Własny ODM Wi-Fi/Matter | **Własny ODM Modbus RS485** |
|---|---|---|
| Firmware do negocjacji | ✅ jest, i to problem | ❌ **brak — Modbus to gotowy standard** |
| Aplikacja mobilna | trzeba | **nie dotyczy** |
| Chmura dostawcy | trzeba negocjować wyjście | **nie istnieje** |
| Ryzyko „brickingu" po zerwaniu umowy | realne | **zerowe** |
| **Certyfikacja radiowa** (RED/FCC) | **wymagana, kosztowna** | ❌ **nie ma radia = nie ma certyfikacji radiowej** |
| Certyfikacja elektryczna | LVD, EMC (+IEC 60898 dla MCB) | to samo |
| Dojrzałość rynku ODM | średnia | **bardzo wysoka, towar masowy** |
| Branding | logo, opakowanie, apka | **logo, nadruk, opakowanie** |

🟢 **Brak radia to prawdopodobnie największa oszczędność.** Certyfikacja radiowa
(RED w UE) to osobny, drogi proces z badaniami w akredytowanym laboratorium.
Urządzenie bez nadajnika w ogóle jej nie podlega.

### ⚠️ Ale uwaga prawna, której nie wolno przeoczyć

**W UE ten, kto umieszcza swoją markę na produkcie, staje się „producentem"
w rozumieniu przepisów CE.** Przejmujesz:

- odpowiedzialność za zgodność wyrobu
- obowiązek posiadania dokumentacji technicznej
- deklarację zgodności wystawioną na siebie
- odpowiedzialność cywilną za szkody

**Przy wyłączniku nadprądowym 32 A to nie jest formalność.** Norma `IEC/EN 60898-1`
i konsekwencje ewentualnej awarii są poważne — mówimy o urządzeniu, którego zadaniem
jest zapobiegać pożarom instalacji.

**[DEDUKCJA] Rekomendacja:** jeśli już własna marka na sprzęcie w rozdzielnicy, to
zacznij od **licznika/pomiaru** (`SDM120`-podobny), a nie od zabezpieczenia.
Licznik nie chroni życia — wyłącznik tak. Zupełnie inna waga ryzyka przy tej samej
łatwości private label.

---

## 6. Jak to wpiąć do Twojej architektury

```
   ┌─────────────────────────── ROZDZIELNICA ───────────────────────────┐
   │                                                                     │
   │   [licznik Modbus] [MCB RS485] [przekaźnik Modbus] [licznik PV]     │
   │         │              │              │                │           │
   │         └──────────────┴──────────────┴────────────────┘           │
   │                    magistrala RS485 (para skręcana A/B)             │
   └────────────────────────────────┬────────────────────────────────────┘
                                    │
                       konwerter USB↔RS485  albo  bramka Modbus TCP
                                    │
                        ┌───────────────────────┐
                        │  Odroid M1S + Node.js │
                        └───────────────────────┘
                                    │
                              wystawiasz jako MATTER na północ
```

Po stronie kodu: biblioteka Modbus RTU w Node (`modbus-serial` albo podobna),
odczyt rejestrów po adresach, mapowanie na Twój wewnętrzny model urządzenia.
Dalej wszystko jak w [wyjasnienie-polnoc-poludnie.md](wyjasnienie-polnoc-poludnie.md).

🟢 **To idealnie pasuje do modelu „północ/południe"** — Modbus jest jednym z najbogatszych
źródeł na południu (surowe rejestry, wszystko co licznik mierzy), a na północ i tak idzie
Matter.

### Potwierdzenie, że to sensowny kierunek

**Shelly sam to robi.** W ich API są komponenty **`Modbus`** i **`MbRtuClient`**,
a w sklepie **`Shelly PRO Modbus Add-on RS485`**. Czyli producent premium uznał, że
warto mostkować do Modbusa — patrz [shelly/10-asortyment.md](shelly/10-asortyment.md).

### Praktyczne pułapki Modbusa

- **adresacja** — każde urządzenie na magistrali musi mieć unikalny adres, ustawiany
  przełącznikami albo komendą
- **terminacja** — rezystory 120 Ω na końcach magistrali przy dłuższych trasach
- **prędkość i parzystość** — muszą się zgadzać na całej magistrali
- **polaryzacja A/B** — zamiana przewodów to najczęstszy błąd montażowy
- **mapy rejestrów różnią się między producentami** — to nie jest plug&play jak Zigbee;
  każdy model wymaga własnego mapowania. **Przy własnej marce to akurat zaleta:
  Ty definiujesz mapę i dokumentujesz ją raz**

---

## 7. Plan działania

1. **Zweryfikuj koncepcję za ~200 zł, zanim pomyślisz o własnej marce.**
   Kup `Eastron SDM120-Modbus` (albo odpowiednik Acrela) plus konwerter USB↔RS485.
   Wepnij w Odroida, odczytaj rejestry z Node.js. Zobaczysz, czy ta droga Ci pasuje,
   zanim wydasz cokolwiek na ODM
2. **Równolegle napisz do 3–5 producentów z tabeli** — HOCH, TONGOU, CHAYO deklarują
   OEM wprost. Pytaj o MOQ, private label, mapę rejestrów, certyfikaty
3. **Sprawdź, czy dostaniesz produkt już certyfikowany** i na jakich warunkach możesz
   go brandować — to przesądza o kosztach
4. **Zacznij od licznika, nie od wyłącznika** — mniejsza odpowiedzialność prawna przy tej
   samej łatwości brandingu
5. Wyłącznik z lokalnym API kupuj gotowy (**`Shelly Pro CB`**) do czasu, aż skala
   uzasadni własny

---

## 8. Podsumowanie

**Twoje pytanie „czy istnieje urządzenie DIN z sensownym API bez radia" ma odpowiedź:
tak, to cały dojrzały przemysł liczący dekady — tylko nie nazywa się „smart home",
tylko „aparatura pomiarowa i łączeniowa".**

Dla Twojego produktu Modbus w rozdzielnicy ma trzy zalety naraz: **najmocniejsza możliwa
gwarancja local-first, lepsza niezawodność w metalowej skrzynce, i najtańsza droga
do własnej marki, bo znika cała certyfikacja radiowa.**

---

## 9. DECYZJA (2026-08-17): własna marka tylko na urządzeniach niebezpiecznych

Ustalone z userem. **Własny branding zaczynamy od urządzeń pomiarowych, nie od
zabezpieczeń.** Wyłączniki nadprądowe kupujemy gotowe (`Shelly Pro CB`) do czasu,
aż skala uzasadni własny produkt.

### Kryterium, które z tego wynika — klasy ryzyka przy własnej marce

Pytanie kontrolne przed każdą decyzją o brandowaniu:
**„czy awaria tego urządzenia usuwa ochronę życia lub mienia?"**

| Klasa | Co to | Własna marka |
|---|---|---|
| **0 — nie dotyka 230 V** | czujniki, przyciski, bramki, huby, akcesoria | 🟢 **śmiało** |
| **1 — mierzy, nie przerywa** | liczniki energii, przekładniki | 🟢 **tak, tu zaczynamy** |
| **2 — przełącza** | przekaźniki, gniazdka, styczniki | 🟡 ostrożnie, dopiero przy skali |
| **3 — chroni** | MCB, RCD, RCBO, ochronniki przepięciowe | 🔴 **nie brandować** bez poważnego zaplecza technicznego i ubezpieczenia |

Logika: licznik, który się zepsuje, przestaje mierzyć. Wyłącznik, który się zepsuje,
**przestaje chronić** — a instalacja dalej pracuje, tyle że bez zabezpieczenia.
Ta sama łatwość private label, nieporównywalna waga konsekwencji.

⚠️ Klasa 1 to nadal urządzenie wpięte w 230 V — obowiązuje LVD i EMC, dokumentacja
techniczna i deklaracja zgodności na siebie. Jest łatwiej, nie jest „za darmo".

### Dodatkowy argument sprzedażowy za licznikiem jako pierwszym produktem

Licznik pod własną marką **domyka zestaw z Twoim hubem**: „nasz hub + nasz licznik =
gotowy moduł pomiarowy do rozdzielnicy". Sprzedajesz rozwiązanie, nie komponent —
i to jest lepsza marża niż na samym pudełku.
