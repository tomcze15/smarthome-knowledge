# Ściąga: co jest potrzebne, żeby sprzedać w Polsce towar z Chin

Spisane 2026-09-19. Wersja prosta — szczegóły i źródła w notatkach powiązanych.
Dotyczy naszego przypadku: **urządzenie na baterie, z Bluetoothem lub Wi-Fi** (klamka, czujnik,
lampka), kupione w Chinach, sprzedawane **pod marką producenta**.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [certyfikaty-od-chinskiego-dostawcy-jak-czytac.md](certyfikaty-od-chinskiego-dostawcy-jak-czytac.md) ·
[kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md) ·
[klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md](klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md)

---

## 1. Najpierw prostuję jedno słowo

**W Unii nie ma urzędu, który wydaje „certyfikat CE".** Producent sam zleca badania,
sam podpisuje jedną kartkę i sam stawia znak CE. Dlatego:

- **„certyfikat CE" od Chińczyka = nic.** To może być samodzielna deklaracja albo papier
  z byle laboratorium
- liczy się **deklaracja + raporty z badań**, w których zgadza się **model**

---

## 2. Co MUSISZ dostać od dostawcy — dwie rzeczy

### 📄 A. Deklaracja zgodności UE (EU Declaration of Conformity)

Jedna kartka, podpisana przez producenta. Sprawdzasz, czy jest na niej:

- [ ] dokładny **model** (np. S31)
- [ ] **nazwa i adres producenta**
- [ ] wymienione **dyrektywy**: `2014/53/UE` i `2011/65/UE`
- [ ] wymienione **normy** (lista niżej)
- [ ] data i podpis

### 📊 B. Raporty z badań — pliki PDF z laboratorium

| Co bada | Norma |
|---|---|
| radio 2,4 GHz (Bluetooth, Wi-Fi) | **EN 300 328** |
| zakłócenia elektromagnetyczne | **EN 301 489-1** i **EN 301 489-17** |
| bezpieczeństwo elektryczne | **EN 62368-1** |
| pole elektromagnetyczne | **EN 62311** lub **EN 62479** |
| 🔴 **cyberbezpieczeństwo** | **EN 18031-1** i **EN 18031-2** |
| substancje niebezpieczne (RoHS) | **EN IEC 63000** |

🔴 **EN 18031 to ten, którego chińscy dostawcy najczęściej nie mają.** Obowiązuje od
**1.08.2025**. Pytaj o niego jako pierwszy — jak go nie ma, nie ma o czym rozmawiać.

---

## 3. Dlaczego tylko DWIE dyrektywy — po ludzku

**Zwykłe urządzenie bez radia** (np. czajnik) musi spełnić dwa osobne zestawy przepisów:

- **LVD** — żeby nie kopnęło prądem
- **EMC** — żeby nie zakłócało radia i telewizora sąsiada

**Urządzenie z radiem** (Bluetooth, Wi-Fi) ma **jeden przepis zamiast tych dwóch**:
dyrektywę **RED (2014/53/UE)**. Ona mówi to samo, tylko w jednym miejscu:

| Artykuł RED | Co wymaga | Odpowiada dawnemu |
|---|---|---|
| art. 3.1(a) | ma być bezpieczne | LVD |
| art. 3.1(b) | ma nie zakłócać | EMC |
| art. 3.2 | ma poprawnie używać fal radiowych | — |
| art. 3.3(d–f) | **ma być cyberbezpieczne** (EN 18031) | — |

➡️ **RED to pakiet „wszystko w jednym".** Nie dokładasz do niego LVD ani EMC — byłoby
to samo dwa razy. Do tego dochodzi tylko **RoHS (2011/65/UE)** — ołów, kadm, rtęć.

🔴 **Praktyczny test dostawcy:** jeśli na deklaracji wypisze **LVD i EMC zamiast RED**,
to znaczy, że albo nie wie, co robi, albo badał wersję **bez radia**. Czerwona lampka.

**I jeszcze jedno:** LVD i tak by nie dotyczyła klamki — zaczyna się od 50 V, a tam są
baterie 6 V.

---

## 4. Co robisz SAM, po swojej stronie

| Obowiązek | Co to znaczy w praktyce |
|---|---|
| **Twoja nazwa i adres** na produkcie lub opakowaniu | naklejka: nazwa firmy, adres pocztowy, słowo „Importer" |
| **Instrukcja po polsku** | przetłumaczyć i dołożyć do pudełka |
| **Trzymać deklarację i raporty 10 lat** | folder na dysku, kopia zapasowa |
| **BDO** | rejestracja przed pierwszą sprzedażą: sprzęt elektroniczny + baterie + opakowania. Numer BDO na fakturach |
| **Informacje w ofercie online** (GPSR) | dane producenta i Twoje, zdjęcia, ostrzeżenia |
| **OC produktu** | ubezpieczenie — nieobowiązkowe, ale przy zamku konieczne |

### Po co ta naklejka — trzy powody

1. **Przepis wymaga, żeby na produkcie był ktoś z siedzibą w UE.** Chiński producent jest
   nieosiągalny dla polskiego urzędu i dla klienta — musi być ktoś „na miejscu"
2. **Klient musi wiedzieć, do kogo pisać** przy reklamacji, a UOKiK — kogo wezwać przy kontroli
3. 🔴 **Jeśli nikogo nie ma na produkcie, to Ty odpowiadasz jak producent** — bo nie masz
   kogo wskazać. Naklejka nie dokłada Ci obowiązków, tylko **pokazuje te, które i tak masz**,
   i chroni przed przeskoczeniem do cięższej roli —
   [kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md)

Może być na opakowaniu, jeśli na urządzeniu się nie mieści.

---

## 4a. 💰 Ile to kosztuje

| Pozycja | Koszt | Źródło |
|---|---|---|
| **BDO — opłata rejestrowa** (jednorazowo, przy wpisie) | **200 zł** mikroprzedsiębiorca / **800 zł** pozostali | [DOK — stawki od 1.01.2025] |
| **BDO — opłata roczna** (co rok, w roku wpisu nie płacisz) | **200 zł** / **800 zł** | [DOK] |
| **Zabezpieczenie finansowe albo umowa z organizacją odzysku** | 🔴 obowiązkowe dla sprzętu **dla gospodarstw domowych**. Albo depozyt/gwarancja u marszałka, albo umowa z organizacją odzysku — dla małego importera umowa jest prostsza, rząd **kilkuset zł do ~1 500 zł rocznie** **[DO SPRAWDZENIA — wziąć wycenę]** | [DOK — ustawa o ZSEiE, art. 27] |
| **Publiczne kampanie edukacyjne** | obowiązek wprowadzającego; **organizacja odzysku zwykle przejmuje to w ramach umowy** **[DO SPRAWDZENIA]** | [DOK — GIOŚ] |
| **Baterie i opakowania** | osobne wpisy i sprawozdania, zwykle w tej samej umowie z organizacją odzysku | |
| **Tłumaczenie instrukcji** | 0 zł, jeśli sam · **800–2 800 zł** w biurze tłumaczeń (ok. 40–70 zł za stronę) **[DO SPRAWDZENIA]** | |
| **OC produktu** | **800–6 000 zł rocznie** — zależy od sumy gwarancyjnej i tego, że zamek to produkt podwyższonego ryzyka **[DO SPRAWDZENIA — wziąć 2–3 oferty]** | |
| **RAZEM pierwszy rok** | **ok. 1 300 zł** (sam tłumaczysz, tanie OC) **… do ok. 9 000 zł** | |

**[DEDUKCJA]** To są koszty **firmy na rok**, nie koszty partii. Rozliczanie ich na 30 sztuk
zniekształca obraz — patrz [klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md](klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md).

---

## 5. Czego NIE potrzebujesz — nie daj się nabrać

| Znak | Dlaczego bez znaczenia |
|---|---|
| **FCC** | Stany Zjednoczone |
| **UL** | USA i Kanada |
| **UKCA** | Wielka Brytania |
| **CCC** i inne chińskie | rynek chiński |
| **ISO 9001 / 14001 / 45001** | dotyczą **fabryki**, nie produktu |
| **„IP66"**, **„Fireproof"**, **„Waterproof"** | 🔴 to nie certyfikaty, tylko hasła. Żądaj raportu albo wykreśl z opisu |

---

## 5a. Co napisać chińskiemu dostawcy — gotowy tekst

🔴 **Nie pytaj „czy macie certyfikat LVD / EMC".** To nie są certyfikaty, tylko dyrektywy —
a przy urządzeniu z radiem obie siedzą w środku RED. Pytanie w tej formie brzmi jak pytanie
laika i dostaniesz odpowiedź „yes we have CE".

**Pytaj o dwie rzeczy: deklarację i raporty z konkretnych norm.** Do wklejenia:

> For model [MODEL], we need the following documents before placing an order:
>
> 1. **EU Declaration of Conformity** stating Directive **2014/53/EU (RED)** and Directive
>    **2011/65/EU (RoHS)**, with the model number and the manufacturer's name and address.
> 2. **Test reports** (PDF) for:
>    - **EN 300 328** (2.4 GHz radio)
>    - **EN 301 489-1** and **EN 301 489-17** (EMC)
>    - **EN 62368-1** (safety)
>    - **EN 62311** or **EN 62479** (RF exposure)
>    - **EN 18031-1** and **EN 18031-2** (cybersecurity, mandatory in the EU since 1 August 2025)
>    - **EN IEC 63000** (RoHS technical documentation)
> 3. The **name of the test laboratory** and its accreditation number.
>
> Please note: FCC, UL and UKCA are not valid in the European Union, and a "CE certificate"
> issued by a test house is not a Declaration of Conformity.
>
> Also: is the Declaration issued by **your company** or by the factory? If by the factory,
> please provide the factory's name and address as they appear on the document.

## 5b. 🔴 „CE / RoHS / FCC / LVD" w ofercie — co to naprawdę znaczy [DEDUKCJA]

Taki zestaw przy klamce z Bluetoothem to **sygnał ostrzegawczy**, nie certyfikat:

| Co widać | Jak to czytać |
|---|---|
| jest **LVD**, nie ma **RED** | albo badali wersję **bez radia**, albo przepisali listę z innego produktu. Przy 6 V z baterii LVD w ogóle nie ma zastosowania |
| nie ma **EMC** | przy radiu EMC jest częścią RED (EN 301 489) — brak wzmianki znaczy, że nikt tego nie składał w całość |
| nie ma **EN 18031** | najczęstszy brak na rynku. Obowiązuje od 1.08.2025 i **to jest dziś główny powód, dla którego towar nie wjedzie do UE** |
| jest **FCC** | sprzedają głównie do USA — Europa jest dla nich dodatkiem |

**Hurtownia często sama tych dokumentów nie ma** — ma je fabryka. Dlatego zawsze pada pytanie,
czyja jest deklaracja.

## 6. Kolejność działania

1. **Pytasz o deklarację i raporty** — zanim zamówisz próbkę
2. **Sprawdzasz, czy jest model i czy jest EN 18031**
3. Nie ma → **nie kupujesz.** Nie da się tego obejść płacąc samemu za badania: deklarację
   wystawia producent, nie Ty —
   [klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md](klamki-z-chin-koszty-optymistyczne-i-pesymistyczne.md)
4. Jest → **zamawiasz próbkę**, sprawdzasz tabliczkę znamionową na urządzeniu
5. Rejestrujesz się w **BDO**, kupujesz **OC produktu**, tłumaczysz instrukcję
6. Zamawiasz partię

---

## 7. Jedno zdanie

> **Potrzebujesz od Chińczyka dwóch rzeczy — deklaracji zgodności i raportów z badań —
> a najważniejszy z nich to EN 18031; reszta znaczków na ich stronie (FCC, UL, UKCA, ISO,
> „IP66") do sprzedaży w Polsce nie służy do niczego.**

---

## Źródła

- [BDO — ile wynosi opłata rejestrowa i roczna](https://bdo.mos.gov.pl/baza-wiedzy/ile-wynosi-oplata-rejestrowa-roczna/) · [BDO — nowe stawki opłat](https://bdo.mos.gov.pl/news/nowe-stawki-oplaty-rejestrowej-i-oplaty-rocznej/)
- [Biznes.gov.pl — zabezpieczenie na poczet zbierania zużytego sprzętu](https://www.biznes.gov.pl/en/publikacje/2222-zabezpieczenie-sprzet-elektryczny) · [GIOŚ — zabezpieczenia finansowe, opłaty produktowe, kampanie edukacyjne](http://www.gios.gov.pl/pl/eea/12-gospodarka-odpadami/zuzyty-sprzet-elektryczny-i-elektroniczny/39-zseie-finanse)
