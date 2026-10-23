# Mini PC z Alibaby — sprzedaż w Polsce i własne logo

Spisane 2026-09-17. Źródła: Komisja Europejska (Blue Guide, rozporządzenie 2019/1020, GPSR,
ekoprojekt komputerów), EUR-Lex (zasilacze 2019/1782), SGS (RED i EN 18031), Bluetooth SIG,
HDMI Licensing Administrator, poradniki branżowe (BDO, baterie, SCIP, import z Chin, cło,
weryfikacja dostawców Alibaby). Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md) (sekcja 5.3 —
producent, RED, CRA, BDO) · [ocena-szczera-modelu.md](ocena-szczera-modelu.md) (marka na hubie) ·
[gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md) ·
[strategia-modbus-rs485.md](strategia-modbus-rs485.md) (brak radia = brak certyfikacji radiowej)

**Produkt:** „12th Gen Mini PC Intel N95" —
[aukcja](https://www.alibaba.com/product-detail/12Th-Gen-Mini-Pc-Intel-N95_1601524209410.html).
Na aukcji znaczki: DOC „CE Certified", RoHS, FCC, CE.

⚠️ **Strony nie udało się odczytać** — Alibaba wymaga weryfikacji CAPTCHA. Specyfikację trzeba
uzupełnić: karta Wi-Fi/Bluetooth, zasilacz, system, bateria. #do-sprawdzenia

---

## 1. Odpowiedź w skrócie

- **Pod własnym logo legalnie — tak. Łatwo — nie.**
- kto sprzedaje produkt pod swoją nazwą lub marką, jest **producentem** i przejmuje jego
  obowiązki [DOK — Blue Guide]
- dokumenty dostawcy to punkt wyjścia; deklarację zgodności podpisujesz Ty
- największe pułapki kosztowe: opłata Bluetooth SIG, jednostka notyfikowana przy EN 18031,
  powtórne badania, obowiązki BDO
- największe uproszczenia: wersja **bez Wi-Fi i Bluetooth**, **bez Windowsa** (sekcja 6)

---

## 2. Co znaczą „Certificates" na Alibabie

| Znaczek | Co to jest | Co daje Tobie |
|---|---|---|
| **CE** | **deklaracja producenta**, że produkt spełnia przepisy UE — nie certyfikat z laboratorium [DOK — poradniki PL] | przy Twoim logo nic; potrzebna Twoja deklaracja |
| **DOC** | deklaracja zgodności **wystawiona przez dostawcę** | podstawa do Twojej, jeśli dostaniesz pełną dokumentację |
| **RoHS** | zgodność z ograniczeniem substancji niebezpiecznych | potrzebna; liczą się raporty dla tego modelu |
| **FCC** | przepisy **USA** | w UE bez znaczenia |

**Znaczek weryfikacji dostawcy** [DOK — poradniki o Alibabie]:
- płatny program; audyt firmy przez zewnętrznych audytorów (np. SGS, TÜV, Intertek)
- obejmuje m.in. licencję firmy, zakład, produkcję i „verification of claimed certifications"
- „not a guarantee of product quality"

🔴 „Fabryka ma certyfikat" nie wystarczy — musisz mieć deklarację zgodności i dokumentację
techniczną w ręku [DOK — poradniki PL]. **[DEDUKCJA]** Dokumenty muszą dotyczyć **dokładnie tego
modelu i konfiguracji**: ta karta Wi-Fi, ten zasilacz, europejska wtyczka.

---

## 3. Trzy role — od nich zależy zakres obowiązków

| Jak sprzedajesz | Rola | Obowiązki |
|---|---|---|
| kupujesz w UE, sprzedajesz pod marką producenta | **dystrybutor** | najlżejsze: sprawdzasz oznakowanie, instrukcję, dane producenta **[DEDUKCJA]** |
| sprowadzasz z Chin, sprzedajesz pod marką chińską | **importer** | sprawdzasz CE, deklarację i dokumentację producenta; **Twoja nazwa i adres na produkcie** [DOK — poradniki PL] |
| **sprzedajesz pod swoim logo** | **producent** | **wszystko** z sekcji 4 [DOK — Blue Guide] |

---

## 4. Obowiązki przy sprzedaży pod własnym logo

### 4.1 Zgodność i bezpieczeństwo produktu

| Przepis | Czego wymaga | Pewność |
|---|---|---|
| **RED** 2014/53/UE — przy Wi-Fi lub Bluetooth | badania radiowe; **[DEDUKCJA]** obejmuje też bezpieczeństwo i kompatybilność elektromagnetyczną | — |
| **RED — cyberbezpieczeństwo**, od 1.08.2025 | normy EN 18031 zharmonizowane **z ograniczeniami**: jeśli użytkownik może nie ustawić hasła, brak domniemania zgodności → **jednostka notyfikowana** | [DOK — SGS] |
| **LVD** i **EMC** — przy wersji bez radia | bezpieczeństwo elektryczne i kompatybilność elektromagnetyczna | **[DEDUKCJA]** |
| **RoHS** 2011/65/UE | ograniczenie substancji, dokumentacja | [DOK] |
| **Ekoprojekt komputerów** 617/2013 | zużycie energii, tryby uśpienia i wyłączenia, zarządzanie energią; obowiązuje od 2014, **trwa przegląd** — KE konsultowała w 2024 nowe wymogi i etykietę energetyczną dla komputerów | [DOK — eceee, KE] |
| **Ekoprojekt zasilaczy** 2019/1782 | zasilacze zewnętrzne do 250 W: sprawność, pobór bez obciążenia, oznakowanie | [DOK — EUR-Lex, poradniki] |
| **CRA** | zgłaszanie luk od 11.09.2026, pełne wymagania od 11.12.2027 — [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md), sekcja 5.3 | [DOK] |
| **GPSR** 2023/988, art. 19 | oferta online: nazwa, adres pocztowy i elektroniczny producenta (lub osoby odpowiedzialnej), identyfikacja produktu ze zdjęciem, ostrzeżenia | [DOK] |
| **2019/1020, art. 4** | podmiot gospodarczy z UE; jego nazwa i dane kontaktowe z adresem na produkcie, opakowaniu, paczce albo w dokumencie; od 16.07.2021 | [DOK] |
| **Język polski** | instrukcja i informacje o bezpieczeństwie po polsku | [DOK — poradniki PL] |

### 4.2 Środowisko — BDO i chemia

- **Sprzęt elektroniczny:** wpis do BDO, ewidencja masy, sprawozdanie do 15 marca, opłaty
  [DOK — poradniki PL]
- **Baterie:** **[DEDUKCJA]** mini PC ma zwykle baterię podtrzymującą zegar. Rozporządzenie
  2023/1542 obejmuje baterie zamontowane w sprzęcie [DOK]; wprowadzający baterie potrzebuje
  wpisu w BDO (dział V, tabela 1) i umieszcza numer BDO na paragonach i fakturach [DOK — poradniki PL].
  Nowe obowiązki od 18.08.2025 [DOK]
- **Opakowania:** wprowadzający produkty w opakowaniach również podlega wpisowi do BDO [DOK — poradniki PL]
- **Odbiór starego sprzętu:** sprzedawca sprzętu dla gospodarstw domowych przyjmuje nieodpłatnie
  zużyty sprzęt tego samego rodzaju, w liczbie nie większej niż sprzedany; informacja w punkcie
  sprzedaży albo na stronie [DOK — art. 37 ustawy o zużytym sprzęcie]
- **SCIP i REACH:** wyrób z substancją z listy kandydackiej powyżej 0,1% wagi → zgłoszenie do
  bazy SCIP w ECHA; obowiązek spada na importera z UE, bo ECHA nie przyjmuje zgłoszeń spoza UE
  [DOK — poradniki]. Potrzebna deklaracja SVHC od dostawcy

### 4.3 Licencje i znaki

**Bluetooth** [DOK — Bluetooth SIG] — ⚠️ to **nie przepis państwowy**, tylko warunki licencyjne
organizacji, która trzyma technologię i znak Bluetooth:

| Kto | Czy musi zgłaszać produkt |
|---|---|
| sprzedajesz cudzy produkt **bez swojego logo** | ❌ nie — „If you are a retailer or supplier simply selling or distributing another company's Bluetooth product and not branding or representing the product as your own, you do not need to qualify or declare the product" |
| **rebranding / white label pod Twoją marką** | ✅ tak — „a new Declaration ID is required if you are rebranding an already qualified design" |
| produkt, który przedstawiasz jako swój | ✅ tak — „Your supplier or other member companies cannot qualify your products on your behalf" |

**Opłata liczy się za projekt, nie za sztukę ani nie za model** [DOK]:

> „A Product Qualification Fee will be required for the first product submission that includes
> a specific design. However, subsequent products you submit that include the same design will
> not be charged a fee."

| Członkostwo | Roczna opłata | Kwalifikacja produktu |
|---|---|---|
| Adopter | 0 $ | **12 000 $** |
| Contributing Adopter (small) | 3 500 $ | 8 000 $ (−33%) za **pierwszą** w roku, kolejne 12 000 $ |
| Associate (small) | 11 250 $ | 6 000 $ (−50%) za **każdą** |

### 🟢 Nasz scenariusz: cudze urządzenia + własna apka [DEDUKCJA]

Architektura: **ASUS NUC 14 Essential** (marka ASUS), **klamki pod marką producenta**,
**nasza apka na telefonie** używająca SDK TTLock i łącząca się po BLE.

| Element | Czyj Bluetooth | Opłata SIG |
|---|---|---|
| NUC | ASUS zakwalifikował | 0 zł |
| Klamka | producent zakwalifikował | 0 zł |
| Telefon klienta | Samsung/Apple zakwalifikował | 0 zł |
| **Nasza apka** | **nie implementuje własnego stosu** — woła API systemu | **0 zł** |

➡️ **Opłata kwalifikacyjna nas nie dotyczy.** Płaci się za **konstrukcję (design)**, czyli
implementację stosu Bluetooth. Apka mobilna korzysta ze stosu telefonu, który jest już
zakwalifikowany przez jego producenta.

🟡 **Co jednak warto zrobić:** SIG pisze wprost, że **tylko firmy członkowskie** mają licencję
na znak „Bluetooth" i logo [DOK]. Jeśli w apce albo materiałach pojawi się słowo „Bluetooth",
należy być **Adopterem — składka 0 $**. Zero kosztu, sama formalność.

**[DO SPRAWDZENIA]** SIG nie publikuje w bazie wiedzy osobnego scenariusza „aplikacja mobilna".
Szukałem — nie ma. Jeśli chcemy to mieć na papierze, wystarczy **bezpłatne zapytanie do
support SIG** przed startem sprzedaży.

### Za co dokładnie płacisz — trzy częste nieporozumienia [DOK + DEDUKCJA]

| Mit | Jak jest |
|---|---|
| „to opłata za aplikację" | ❌ **apka nie kosztuje nic.** Telefon jest już zakwalifikowany przez swojego producenta. Opłata dotyczy **sprzętu** z układem Bluetooth |
| „płacę raz za firmę i mam spokój" | ❌ płacisz **raz za każdą konstrukcję (design)**. Klamka od fabryki A = jedna opłata, czujnik od fabryki B = druga, gniazdko od C = trzecia |
| „opłata przypisuje mnie do jednej nazwy" | ❌ **nazwa nie ma z tym nic wspólnego.** Pod jedną zakwalifikowaną konstrukcją możesz wypuścić dowolnie wiele produktów i nazw — kolejne zgłoszenia są **bezpłatne** [DOK: „subsequent products you submit that include the same design will not be charged a fee"] |

**Czy to wieczne:** od 1.07.2024 obowiązuje nowy proces (QPRD v3) — QDID zastąpiono **Design
Number**, a komponenty **nie wymagają już odnawiania co 3 lata** i zostają ważne, dopóki
producent je wspiera [DOK — Allion Labs, Ezurio, SGS]. Trzeba utrzymywać członkostwo, ale
Adopter kosztuje 0 $. **[DO SPRAWDZENIA]** co się dzieje, gdy SIG wycofa wersję rdzenia,
na której konstrukcja była kwalifikowana.

- 🔴 **KOREKTA 2026-09-19:** kwoty powyżej to cennik oznaczony **„Effective 01 March 2026"**,
  czyli **już po** podwyżce o ok. 8,7% — nie wolno doliczać jej drugi raz [DOK — SIG, „Dues and fees"]
- **Company Identifier** (własny identyfikator producenta): 1 250 $ dla Adoptera i Contributing
  Adoptera, 0 $ dla Associate [DOK]
- 🟢 **POTWIERDZONE 2026-09-20 [DOK — SIG, FAQ, akt. 28.07.2025]:** opłata dotyczy Twojego
  pierwszego produktu z danym projektem **także wtedy, gdy projekt zakwalifikował ktoś inny**.
  Wcześniej miałem to jako dedukcję; jest cytat:

  > „**Every company selling, branding, manufacturing, or representing a product as its own
  > must pay a Product Qualification Fee** to complete their product qualification."

  Kwalifikacja fabryki oszczędza Ci badań (powołujesz się na jej Design Number), ale **nie
  opłatę** — cennik ma jedną pozycję 12 000 $, bez taniejszego wariantu dla gotowej konstrukcji.
  Test nie brzmi „czyj napis jest na obudowie", tylko **czy przedstawiasz produkt jako swój**
- **[DEDUKCJA]** dlatego np. Raspberry Pi ma to po swojej stronie jako producent, a kupujący
  Pi i odsprzedający je bez własnego logo nie płaci nic. Na forum RPi ktoś dostał od SIG
  odpowiedź, że w jego przypadku „no declaration nor fees due" — ale odpowiedzi nie zacytowano,
  więc to poszlaka, nie reguła

**HDMI** [DOK — HDMI LA]:
- producent gotowego produktu z HDMI musi być licencjonowanym HDMI Adopterem
- znaków HDMI mogą używać tylko licencjobiorcy oraz ich autoryzowani dystrybutorzy i sprzedawcy
- **[DO SPRAWDZENIA]** czy licencja chińskiej fabryki obejmuje produkt pod Twoją marką

**Windows** **[DEDUKCJA]**: preinstalowany system musi mieć legalną licencję; na hub i tak
lepiej zamówić bez systemu.

**Własna marka** **[DEDUKCJA]**: sprawdzić, czy nazwa nie narusza cudzego znaku towarowego;
rejestracja w UPRP albo EUIPO chroni Twój.

### 4.4 Konsument **[DEDUKCJA]**

- 2 lata odpowiedzialności sprzedawcy za niezgodność towaru z umową
- 14 dni na zwrot przy sprzedaży online
- gwarancja producenta — dobrowolna, ale jeśli jest, to z warunkami po polsku

### 4.5 Import i podatki

- komputery (HS 8471): **0% cła** w UE dzięki umowie ITA [DOK — poradniki]
- VAT importowy **[DEDUKCJA]** — stawka podstawowa 23%
- przy kontroli trzeba okazać deklarację zgodności, dokumentację techniczną i polską instrukcję
  [DOK — poradniki PL]

---

## 5. Czego żądać od dostawcy przed zamówieniem

1. **raporty z badań dla dokładnie tego modelu i konfiguracji**, z akredytowanego laboratorium
2. typowy zestaw norm **[DEDUKCJA — potwierdzić z laboratorium]**:
   - bezpieczeństwo: EN 62368-1
   - EMC: EN 55032, EN 55035, EN 61000-3-2, EN 61000-3-3
   - RoHS: EN IEC 63000
   - przy radiu: EN 300 328, EN 301 893, EN 301 489-1, EN 301 489-17, EN 62311, **EN 18031-1 i -2**
3. dokumenty zasilacza: bezpieczeństwo, EMC, **zgodność z 2019/1782**, europejska wtyczka
4. **pełna dokumentacja techniczna** do Twojej deklaracji zgodności — nie sam certyfikat
5. deklaracja SVHC (REACH, SCIP)
6. status **HDMI Adopter** fabryki; przy Bluetooth — dane kwalifikacji modułu
7. system: brak albo legalna licencja
8. umowa OEM pozwalająca na Twoją markę; bez zmian podzespołów bez Twojej zgody —
   [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md), sekcja 4

---

## 6. Jak to uprościć [DEDUKCJA]

| Decyzja | Co odpada | Uwagi |
|---|---|---|
| **wersja bez karty Wi-Fi i Bluetooth**, zamówiona tak z fabryki | RED, EN 18031, opłata Bluetooth SIG | nie wyjmować karty samemu — to zmiana produktu; potrzebne dokumenty dla konfiguracji bez radia |
| **bez Windowsa** | ryzyko licencji | hub i tak działa na Linuksie |
| **start jako dystrybutor**: mini PC od unijnego dystrybutora, marka producenta na obudowie, Twoja marka w apce i usłudze | obowiązki producenta sprzętu | CRA i tak dotyczy Twojego oprogramowania |
| **własne logo dopiero przy skali** | kapitał i ryzyko na starcie | [ocena-szczera-modelu.md](ocena-szczera-modelu.md), fazy 2–3 |

⚠️ **Zigbee w hubie:** dongle to też urządzenie radiowe. **[DO SPRAWDZENIA]** czy dołączenie
cudzego dongla z własnym CE do zestawu pod Twoją marką nie czyni Cię producentem urządzenia radiowego.

---

## 7. Otwarte pytania #do-sprawdzenia

- pełna specyfikacja z aukcji: karta Wi-Fi/Bluetooth, zasilacz, system, bateria
- czy dostawca da pełną dokumentację techniczną i raporty dla konfiguracji bez radia
- HDMI: czy licencja fabryki obejmuje produkt pod Twoją marką
- ekoprojekt komputerów: czy do końca 2026 nie wejdą nowe wymogi albo etykieta energetyczna
- Zigbee: dongle w zestawie a rola producenta urządzenia radiowego
- koszt badań w laboratorium w Polsce dla mini PC

---

## 8. Jedno zdanie

> **Mini PC z Alibaby pod własnym logo sprzedasz legalnie, ale jako producent: znaczki z aukcji
> nie są Twoją zgodnością, a Bluetooth, radio i Windows dokładają koszty — najprościej zacząć od
> wersji bez radia i bez systemu albo jako dystrybutor.**

---

## Źródła

**Przepisy UE**
- [Komisja Europejska — Blue Guide 2022](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ%3AC%3A2022%3A247%3AFULL)
- [EUR-Lex — rozporządzenie 2019/1020](https://eur-lex.europa.eu/eli/reg/2019/1020/oj/eng) · [Komisja Europejska — nadzór rynku](https://single-market-economy.ec.europa.eu/single-market/goods/building-blocks/market-surveillance_en)
- [EUR-Lex — GPSR 2023/988](https://eur-lex.europa.eu/eli/reg/2023/988/oj/eng)
- [SGS — EN 18031 zharmonizowane z ograniczeniami](https://www.sgs.com/en/news/2025/02/safeguards-02625-eu-harmonizes-en-18031-standards)
- [eceee — ekoprojekt komputerów 617/2013](https://www.eceee.org/ecodesign/products/personal_computers/) · [Komisja Europejska — konsultacje: komputery, 2024](https://energy.ec.europa.eu/news/public-consultation-ecodesign-and-energy-labelling-requirements-computers-2024-04-25_en)
- [EUR-Lex — zasilacze zewnętrzne 2019/1782](https://eur-lex.europa.eu/eli/reg/2019/1782/oj/eng)

**Licencje**
- [Bluetooth SIG — opłaty](https://www.bluetooth.com/fee-schedule/) · [Bluetooth SIG — FAQ](https://support.bluetooth.com/hc/en-us/articles/38391050584077-Frequently-Asked-Questions-FAQs)
- [HDMI LA — adopterzy](https://www.hdmi.org/adopter/) · [HDMI — wytyczne marki i IPR](https://ir.ebaystatic.com/pictures/aw/pics/pdf/us/help/community/vpp/hdmilicensing.pdf)

**Polska: import, BDO, baterie**
- [CECE Polska — CE dla importerów z Chin](https://www.ce-polska.pl/ce-dla-importerow-z-chin-obowiazki-przed-wprowadzeniem-produktu-na-rynek-ue) · [Chiński Raport — deklaracja zgodności](https://www.chinskiraport.pl/blog/deklaracja-zgodnosci-oznaczenie-ce/)
- [LEX — art. 37 ustawy o zużytym sprzęcie](https://sip.lex.pl/akty-prawne/dzu-dziennik-ustaw/zuzyty-sprzet-elektryczny-i-elektroniczny-18233050/art-37)
- [Asekol — import z Chin a BDO](https://asekol.pl/blog/import-towaru-z-chin-a-bdo-co-musisz-wiedziec/) · [Asekol — baterie od 18 sierpnia 2025](https://asekol.pl/wazna-zmiana-dla-producentow-baterii-nowe-obowiazki-od-18-sierpnia-2025-r/)
- [Sozosfera — wprowadzający baterie](https://sozosfera.pl/odpady/wprowadzajacy-baterie-lub-akumulatory/) · [Prawo.pl — rozporządzenie o bateriach](https://www.prawo.pl/biznes/rozporzadzenie-o-bateriach-kogo-dotycza-nowe-obowiazki,532499.html)

**Chemia, cło, Alibaba**
- [ComplianceGate — baza SCIP](https://www.compliancegate.com/scip-database/) · [REACH24H — SCIP](https://en.reach24h.com/service/chemical/scip-notification-key-eu-requirements-for-svhc-in-articles)
- [Dutiable — cło na komputery w UE](https://dutiable.io/duty-rates/eu/laptops)
- [Cosmo Sourcing — co mówi znaczek Verified Supplier](https://www.cosmosourcing.com/blog/what-are-alibaba-verified-suppliers) · [Sinolinks — zakres weryfikacji Alibaby](https://www.sinolinksverify.com/guides/alibaba-supplier-verification)
