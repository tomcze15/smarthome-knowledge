# Opłaty za standardy: Bluetooth SIG vs CSA (Matter, Zigbee) — co płacimy w naszym modelu

Spisane 2026-09-19. Źródła: Bluetooth SIG („Dues and fees", „Qualify your product",
„What are the requirements to use the Bluetooth Trademarks"), CSA (poziomy członkostwa
i opłaty certyfikacyjne — Matter Alpha, Matter Handbook), blog Home Assistant o certyfikacji
Matter. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md](mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md) ·
[klamki-30-sztuk-rachunek-wlasna-marka.md](klamki-30-sztuk-rachunek-wlasna-marka.md) ·
[jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md)

---

## 1. Nasz model

Sprzedajemy **cudze urządzenia pod marką ich producentów** (klamki, lampki Matter/Zigbee,
ASUS NUC 14 Essential), a naszą marką jest **aplikacja i usługa**.

## 2. 🟢 Bluetooth SIG — w tym modelu 0 zł

| Element | Czyja kwalifikacja | Nasza opłata |
|---|---|---|
| NUC, klamka, lampka, telefon klienta | producenta | 0 zł |
| Nasza apka (SDK TTLock, BLE przez API systemu) | **nie implementuje własnego stosu** | 0 zł |

Podstawa [DOK — SIG]: *„If you are a retailer or supplier simply selling or distributing another
company's Bluetooth product and not branding or representing the product as your own, you do not
need to qualify or declare the product."*

🟡 Jedyne, co warto: **członkostwo Adopter za 0 $**, bo znaku „Bluetooth" i logo mogą używać
wyłącznie firmy członkowskie [DOK]. Zero kosztu.

**[DO SPRAWDZENIA]** SIG nie publikuje osobnego scenariusza „aplikacja mobilna" — przeszukałem
bazę wiedzy, nie ma. Pewność daje bezpłatne zapytanie do support SIG przed startem sprzedaży.

## 3. Matter i Zigbee — inna organizacja, inne zasady

**Connectivity Standards Alliance (CSA)**, nie Bluetooth SIG. Cennik odczytany ze strony CSA
**20.09.2026** [DOK]:

| Poziom | Składka roczna | Do czego |
|---|---|---|
| **Associate** | **0 $** | używasz cudzych certyfikowanych produktów; white-label przez Certification Transfer Program |
| **Adopter** | **7 500 $** | budujesz i certyfikujesz własne produkty, używasz logo Matter |
| Participant | 21 500 $ | współtworzysz standard, dostęp do grup roboczych |
| Promoter | 112 500 $ + wpisowe | zarząd Sojuszu |

### 🔴 KOREKTA 2026-09-20

Wcześniej podałem „Adopter 7 000 $ i 3 000 $ za produkt" i pominąłem poziom **Associate za 0 $**.
Poprawne liczby są w tabeli wyżej, a Associate jest dla nas najważniejszy.

## 3a. Trzy sytuacje — i tylko jedna coś kosztuje

### 🟢 A. Sprzedajesz cudze urządzenia Matter pod marką producenta — **0 zł**

Nasz model. Nie musisz być nawet członkiem CSA. Certyfikat ma producent, logo Matter jest jego.

### 🟡 B. Cudze certyfikowane urządzenie pod **Twoją marką** (white-label) [DOK]

Przez **Certification Transfer Program**: **Associate 0 $ rocznie + 2 500 $ za produkt
+ 500 $ rocznie za każdy produkt**. Warunek: fabryka musi być członkiem **Participant
albo Promoter**.

**[DEDUKCJA] Porównanie, które warto zapamiętać:** rebranding urządzenia **Matter** kosztuje
**2 500 $**, a rebranding urządzenia **Bluetooth** — **12 000 $**. Ale uwaga: urządzenia Matter
parują się **po Bluetooth LE**, więc white-label odpala **obie** opłaty naraz.

### 🔴 C. Twój hub jako **kontroler Matter** — tu jest prawdziwy koszt

To jedyne miejsce, gdzie Matter różni się od Bluetootha zasadniczo: **CSA certyfikuje także
sterujących i oprogramowanie**, nie tylko urządzenia.

| Pozycja | Koszt |
|---|---|
| Członkostwo **Adopter** | **7 500 $ rocznie** |
| Certyfikacja produktu | ok. **2 000 $** (pochodny ok. 1 500 $) **[DO SPRAWDZENIA]** |
| Badania w autoryzowanym laboratorium | **[DO SPRAWDZENIA]** — osobna faktura, nie ma tego w cenniku CSA |

Dowód, że to realne [DOK — blog Home Assistant]: Open Home Foundation przeszło certyfikację —
**Matter Server** jako „Software Component", **Home Assistant** jako „User Interface Component".
Dopiero to daje prawo do logo i wpisu na stronie Matter.

### 🔴 KOREKTA 2026-09-20 — „Beta" było moim błędem

Napisałem wcześniej, że **niecertyfikowane aplikacje muszą oznaczać funkcję Matter jako „Beta"
z wymogu Sojuszu**. **To nieprawda — wycofuję.** Wziąłem to ze streszczenia wyszukiwarki.
Po przeczytaniu źródła: Home Assistant **sam** tak oznaczył integrację, pisząc w tym samym
akapicie o niegotowości implementacji [DOK — „The State of Matter", 25.01.2024]:

> „we plan for it to become officially certified by the CSA (…) But we are not there yet;
> that is why we still label Matter as Beta in the integrations list."

We wpisie o samej certyfikacji (10.03.2025) słowo „beta" **nie pada ani razu**.

**Co jest naprawdę udokumentowane** — reguła dotyczy **znaku towarowego**, nie etykiety
w aplikacji [DOK — CSA, strona członkostwa]:

> Adopters (…) can use Alliance technology **logos and trademarks for certified products**

➡️ Bez certyfikacji **nie wolno używać logo i nazwy Matter jako znaku swojego produktu**.
Nic o „Beta".

**[DO SPRAWDZENIA]** czy wolno napisać opisowo „działa z urządzeniami Matter" bez
certyfikacji — to już niuans prawa znaków towarowych, do zapytania w CSA.

**[DO SPRAWDZENIA]**
- czy sterowanie urządzeniami Matter **bez certyfikacji** jest dopuszczalne technicznie
  (Home Assistant robił to latami) i ogranicza tylko nazewnictwo
- jak to wygląda dla **Zigbee** przy Zigbee2MQTT i cudzym koordynatorze
- ile kosztują badania w laboratorium autoryzowanym przez CSA

## 3b. Co się stanie, jeśli NIE zapłacimy CSA i wypuścimy własny soft

### 🟢 Technicznie: będzie działać

**Matter SDK jest otwarty (Apache 2.0)**, specyfikacja jest publiczna. Nic nie blokuje
zbudowania kontrolera. Dowód: **Home Assistant sterował urządzeniami Matter przez ok. dwa lata
bez certyfikatu** — certyfikację dostał dopiero 10.03.2025 [DOK].

Ważne rozróżnienie: twardą bramką w Matterze jest **certyfikat urządzenia (DAC)** — to
*urządzenie* musi go mieć, żeby dało się je sparować. **Kontroler takiego certyfikatu nie
potrzebuje**, żeby działać.

### 🔴 Czego nie wolno

**Używać nazwy „Matter" i logo jako znaku swojego produktu** — to przywilej członków dla
produktów certyfikowanych [DOK — CSA].

**[DO SPRAWDZENIA]** gdzie leży granica między znakiem towarowym a opisem. Sam Home Assistant
przed certyfikacją nazywał integrację **„Matter (BETA)"** — czyli używał słowa. To obserwacja
z praktyki, nie reguła; pytanie do CSA.

### 🟡 Czego nie dostaniesz

| Brak | Skutek |
|---|---|
| wpisu w **Certified Products** | klient nie zweryfikuje nas w bazie CSA |
| dostępu do narzędzi, testów i wczesnych wersji SDK | wolniejsze nadążanie za wersjami Matter |
| programów „Works with" | zamknięta droga do współpracy z markami |
| **licencji patentowej** — royalty-free IP jest korzyścią członkostwa | **[DO SPRAWDZENIA]** czy implementacja bez członkostwa naraża na roszczenia patentowe. To realne pytanie, nie formalność |

### 🟢 Czym NIE ryzykujesz

Nie ma tu odpowiednika celnego zatrzymania towaru jak przy Bluetooth SIG — **CSA nie certyfikuje
sprzętu, który sprowadzasz**, tylko produkty swoich członków. Sprzedaż cudzych certyfikowanych
urządzeń jest nietknięta.

### [DEDUKCJA] Rekomendacja

Na etapie prototypu i pierwszych klientów: **budować, nie płacić**, unikać logo Matter
w materiałach i pisać ostrożnie opisowo. Certyfikacja ma sens, gdy sprzedaż uzasadni
7 500 $ rocznie. Przed pierwszą kampanią marketingową — **zapytać CSA na piśmie**, jak wolno
nazwać funkcję.

## 4. Jedno zdanie

> **Przy cudzych urządzeniach pod ich marką i Bluetooth, i Matter kosztują nas zero — ale
> w chwili, gdy nasz hub ma być certyfikowanym kontrolerem Matter, wchodzi 7 500 $ rocznie,
> a bez certyfikacji nie wolno używać logo ani nazwy Matter jako znaku naszego produktu.**

---

## Źródła

- [Bluetooth SIG — Dues and fees](https://www.bluetooth.com/fee-schedule/) · [Qualify your product](https://www.bluetooth.com/develop-with-bluetooth/qualify/) · [Wymogi użycia znaków Bluetooth](https://support.bluetooth.com/hc/en-us/articles/360049492171-What-are-the-requirements-to-use-the-Bluetooth-brand)
- [CSA — zostań członkiem](https://csa-iot.org/become-member/) · [Matter Alpha — poziomy członkostwa CSA](https://www.matteralpha.com/industry-news/connectivity-standards-alliance-confirms-membership-tiers-for-iot-development) · [Matter Handbook — członkostwo](https://handbook.buildwithmatter.com/certification/certifying-a-product/membership/)
- [Home Assistant — oficjalna certyfikacja Matter](https://www.home-assistant.io/blog/2025/03/10/matter-certification/)
