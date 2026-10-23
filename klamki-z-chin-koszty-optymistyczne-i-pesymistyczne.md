# 30 klamek z Chin bez własnego loga — widełki kosztów

Policzone 2026-09-19 na danych od usera: **290,22 zł/szt × 30 szt**, waga przyjęta 2,5 kg/szt.
Scenariusz: **marka producenta na obudowie**, nasza marka tylko na aplikacji.
Podstawy: [klamki-30-sztuk-rachunek-wlasna-marka.md](klamki-30-sztuk-rachunek-wlasna-marka.md) (cło 2,7%, TARIC) ·
[oplaty-za-standardy-bluetooth-matter-zigbee.md](oplaty-za-standardy-bluetooth-matter-zigbee.md) (SIG = 0 zł) ·
[kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md) (obowiązki importera) ·
[certyfikaty-od-chinskiego-dostawcy-jak-czytac.md](certyfikaty-od-chinskiego-dostawcy-jak-czytac.md).
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).

---

## 0. Co odpada dzięki braku własnego loga 🟢

| Pozycja | Kwota |
|---|---|
| Bluetooth SIG — kwalifikacja projektu | **0 zł** (było 43 698 zł) |
| Company Identifier | 0 zł |
| Własne badania RED / EN 18031 / EMC / LVD | 0 zł — robi je producent |
| Podpisanie własnej deklaracji zgodności | 0 zł |

🔴 **Ale nadal JESTEŚ IMPORTEREM**, bo kupujesz spoza UE. Zostają: nazwa i adres na produkcie
lub opakowaniu, instrukcja po polsku, przechowanie deklaracji zgodności przez 10 lat, BDO
i pełna odpowiedzialność wobec klienta i UOKiK-u.

---

## 🔴 KOREKTA 2026-09-19 — stawka 1,49 zł/kg nie dotyczy 75 kg

Sprawdziłem realia rynku. **Drobnica morska (LCL) rozlicza się za metr sześcienny**, nie za
kilogram: **200–320 USD za 1 m³, z minimum 1 m³** [DOK — spedytorzy PL]. Trzydzieści klamek
to ok. 0,25 m³ — **i tak zapłacisz za cały metr**. Stawka 1,49 zł/kg to ok. 0,39 USD/kg,
czyli **ośmiokrotnie mniej** niż wychodzi z rachunku LCL. Taka cena istnieje tylko wtedy,
gdy dostawca **dorzuca Twoje pudła do swojej dużej konsolidacji**. Trzeba to dopytać wprost.

Poniższe widełki liczę już na realnym frachcie.

## 1. Koszt samej dostawy 30 sztuk

| Pozycja | 🟢 Optymistycznie | 🔴 Pesymistycznie |
|---|---|---|
| Towar 30 × 290,22 | 8 706,60 | 8 706,60 |
| **Fracht all-in do drzwi** | **1 800** (LCL, min. 1 m³ + opłaty lokalne + dowóz z Gdańska) | **3 400** (kurier lotniczy drzwi–drzwi, ~12 USD/kg) |
| Agencja celna | 200 | 600 |
| Cło 2,7% | 283,68 | 326,88 |
| Przewalutowanie i przelew SWIFT | 50 | 250 |
| Ubezpieczenie cargo | 0 | 120 |
| Rewizja celna | 0 | 800 |
| Braki i uszkodzenia (1 szt = 3%) | 0 | 290,22 |
| **RAZEM dostawa** | **11 040,28** | **14 493,70** |
| **Na sztukę** | **368 zł** | **483 zł** |

### Dlaczego fracht tak skacze [DOK + DEDUKCJA]

Bo przy 75 kg **koszty stałe przesyłki są większe niż sam przewóz**:

| Składnik | Kwota | Czy zależy od wielkości |
|---|---|---|
| Przewóz morski, min. 1 m³ | ~950 zł | 🔴 **nie** — płacisz za metr, choćbyś miał ćwierć |
| Opłaty lokalne w porcie: THC, dokumentacja, dekonsolidacja | 400–900 zł | 🔴 **nie** — ryczałt od przesyłki |
| Dowóz Gdańsk → miejsce docelowe | 400–900 zł | 🔴 nie |
| Magazynowanie, jeśli celnicy zatrzymają | 0–800 zł | loteria |

**Alternatywa: kurier lotniczy drzwi–drzwi.** Ok. **11–13 USD/kg** przy małych wagach
[DOK], czyli dla 75 kg ~3 400 zł — ale **bez opłat portowych, bez agencji, w tydzień
zamiast 50–75 dni**. Przy tej wielkości różnica jest dużo mniejsza, niż się wydaje.

## 2. Koszty startowe — jednorazowe, ale bolesne przy 30 sztukach

| Pozycja | 🟢 Optymistycznie | 🔴 Pesymistycznie |
|---|---|---|
| Próbki przed zamówieniem (1–2 szt + kurier) | 400 | 900 |
| **Sprawdzenie dokumentów** (patrz sekcja 3) | 0 (sam) | 5 000 |
| Tłumaczenie instrukcji na polski | 0 (sam) | 1 500 |
| Etykieta importera (projekt + druk) | 50 | 300 |
| BDO: opłata roczna, sprawozdania, opakowania i baterie **[DO SPRAWDZENIA]** | 100 | 800 |
| **OC produktu (rocznie)** | 800 | 6 000 |
| **RAZEM start** | **1 350** | **14 500** |
| Na sztukę przy 30 szt | **45,00 zł** | **483,33 zł** |

## 3. Jakie dokumenty w ogóle sprawdzamy

| Dokument | Co musi zawierać |
|---|---|
| **Deklaracja zgodności UE (DoC)** — jedna kartka, podpisana przez producenta | model, nazwa i adres producenta, wymienione **dyrektywy**, wymienione **normy zharmonizowane z rokiem wydania**, jednostka notyfikowana (jeśli była), miejsce, data, podpis |
| **Raport: radio** | EN 300 328 (pasmo 2,4 GHz — BLE i Wi-Fi) |
| **Raport: EMC** | EN 301 489-1 i EN 301 489-17 |
| **Raport: bezpieczeństwo** | EN 62368-1 — tu przez RED art. 3.1(a) |
| **Raport: pole elektromagnetyczne** | EN 62311 lub EN 62479 |
| 🔴 **Raport: cyberbezpieczeństwo** | **EN 18031-1 i EN 18031-2** — obowiązkowe od 1.08.2025 |
| **RoHS** | dokumentacja wg EN IEC 63000 albo raport z badania |
| **Baterie i REACH** | karta ogniwa, deklaracja SVHC powyżej 0,1% |
| **Zdjęcie tabliczki znamionowej** | znak CE, nazwa i adres producenta na samym wyrobie |
| **Instrukcja** | treść do tłumaczenia na polski |

🔴 **Korekta do maila:** wcześniej wpisałem prośbę o raport **LVD**. Dla klamki na baterie 6 V
dyrektywa niskonapięciowa **nie ma zastosowania** (zaczyna się od 50 V AC / 75 V DC) —
bezpieczeństwo idzie przez **RED art. 3.1(a)**. Do poprawienia w
[mail-liliwise-probki.md](mail-liliwise-probki.md).

## 4. 🔴 A co, jeśli dostawca tych dokumentów NIE MA

**Nie, nie uwzględniłem tego w pozycji „sprawdzenie dokumentów" — bo to nie jest koszt,
tylko bramka idź / nie idź.** Te 5 000 zł to sprawdzenie papierów, **które istnieją**.

Trzy możliwe wyniki:

| Sytuacja | Co robisz | Koszt |
|---|---|---|
| 🟢 Komplet jest i się zgadza | zamawiasz | 0–5 000 zł na weryfikację |
| 🟡 Brakuje części (typowo EN 18031) i dostawca **chce** uzupełnić | czekasz 2–4 miesiące; dostawca może doliczyć koszt badań do ceny | opóźnienie + wyższa cena jednostkowa |
| 🔴 Nie ma i dostawca nie zrobi | **nie kupujesz** | strata = próbki + Twój czas |

**Dlaczego trzeciego wariantu nie da się „obejść", płacąc samemu za badania:**
deklarację zgodności wystawia **producent**. Nie możesz zbadać cudzego produktu i sprzedawać
go dalej pod **jego** marką z własnym papierem. Jedyna legalna droga to **wziąć na siebie rolę
producenta** — czyli własne logo, własna deklaracja, własne badania (15–40 tys. zł)
**i z powrotem opłata Bluetooth SIG 43 698 zł**
([klamki-30-sztuk-rachunek-wlasna-marka.md](klamki-30-sztuk-rachunek-wlasna-marka.md)).

➡️ **[DEDUKCJA] Wniosek: brak dokumentów to nie jest problem do wyceny, tylko powód,
żeby odejść od stołu.** Dlatego pytanie o raporty musi paść **przed** zamówieniem próbki,
a nie po.

## 4. Dlaczego tak szeroko i jak zawęzić

**Cała różnica siedzi w dwóch miejscach**, nie w cenie towaru:

| Czynnik | Wpływ na sztukę | Jak zamknąć |
|---|---|---|
| **Koszty startowe rozłożone na 30 sztuk** | 45 zł ↔ 483 zł | zamówić więcej **albo** policzyć je jako koszt firmy, nie partii |
| **Fracht drzwi–drzwi** | 60 zł ↔ 113 zł | jedna oferta od spedytora zamyka temat |
| Cena towaru | stała | — |

**To samo przy większych seriach:**

| Sztuk | 🟢 netto / brutto | 🔴 netto / brutto |
|---|---|---|
| 30 | 413 / **508 zł** | 966 / **1 189 zł** |
| 100 | 382 / **469 zł** | 628 / **773 zł** |
| 300 | 373 / **458 zł** | 531 / **654 zł** |

## 5. Trzy rzeczy, które robią największą różnicę [DEDUKCJA]

1. **Zapytaj spedytora o cenę all-in do drzwi** — to jedyny sposób, żeby wyciąć największą
   niepewność. Jedna rozmowa telefoniczna
2. **OC produktu jest realnym kosztem, nie opcją** — przy zamku 800 zł rocznie to optymizm,
   6 000 zł to pesymizm, ale zero nie wchodzi w grę —
   [kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md)
3. **Nie licz kosztów startowych na pierwszą partię.** Próbki, OC, instrukcja i BDO służą
   całej działalności przez lata. Rozliczanie ich na 30 sztuk sztucznie zawyża cenę
   i prowadzi do złych decyzji

## 6. Jedno zdanie

> **Bez własnego loga klamka kosztuje w Polsce od 508 do 1 189 zł brutto na zero — a cała ta
> różnica to nie towar, tylko fracht i koszty startowe rozsmarowane na zaledwie 30 sztuk.**
