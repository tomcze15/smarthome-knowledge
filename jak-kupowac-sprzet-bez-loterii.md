# Jak kupować sprzęt bez loterii — procedura zamiast szukania

Spisane 2026-09-15. Źródła: Tuya Developer Platform, dokumentacja Zigbee2MQTT, Komisja Europejska
(Blue Guide, CRA, EPREL), SCHUTZWERK i SGS (RED), poradniki branżowe (Trade Assurance, BDO).
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md) ·
[strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md) ·
[przyjazni-producenci-zamiast-tuya.md](przyjazni-producenci-zamiast-tuya.md) ·
[zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md) ·
[ocena-szczera-modelu.md](ocena-szczera-modelu.md) ·
[strategia-modbus-rs485.md](strategia-modbus-rs485.md)

---

## 1. Skąd mętlik — pięć obaw, jedna przyczyna

Obawy, z którymi przyszło pytanie:

1. AliExpress blokuje pomiar, a ta sama rzecz z Alibaby pod OEM go udostępnia
2. odwrotnie — AliExpress udostępnia, Alibaba blokuje
3. na Alibabie nie ma dokładnie tego samego, a „podobny w 95%" okazuje się słaby
4. nowe urządzenie Zigbee, którego nikt jeszcze nie rozgryzł
5. pod spodem Tuya — dzwoni do chmury i blokuje funkcje, w Zigbee i w Matterze

🎯 **Wspólna przyczyna: kupujesz firmware, którego nie widać na aukcji.** Zdjęcie, nazwa
i opis to opakowanie. O tym, czy pomiar działa lokalnie, decyduje program w środku —
a ten potrafi się różnić przy identycznym zdjęciu i zmienić między dostawami.

**[DEDUKCJA] Dlatego kolejne godziny przeglądania aukcji nie pomogą** — na aukcji nie ma
odpowiedzi na te pytania. Potrzebna jest **procedura sprawdzania** (sekcja 3), a nie
kolejne wyszukiwanie.

---

## 2. Pięć scenariuszy — co się naprawdę dzieje

### 2.1 Scenariusze 1 i 2 — „ten sam sprzęt", inny pomiar #pulapka

**Oba są realne.** O funkcjach nie decyduje sklep, tylko ten, kto skonfigurował produkt.

- na platformie Tuya **marka tworzy produkt i sama wybiera jego funkcje** — standardowe
  i własne. Każda funkcja to „data point" (DP), a produkt dostaje identyfikator PID [DOK]
- czyli ta sama płytka może wyjść w kilku zestawach funkcji **[DEDUKCJA]**
- w Zigbee widać to po różnych kodach `_TZ3000_…` przy tym samym modelu —
  [gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md), sekcja 4

| | AliExpress | Alibaba (zamówienie OEM) |
|---|---|---|
| Konfiguracja | **cudza, taka, jaka jest** | **możesz ją zamówić** |
| Czy się zmieni | następna dostawa może być inna | tylko jeśli umowa na to pozwala |
| Ochrona | spór i zwrot | Trade Assurance — **egzekwuje zapisy umowy, sama nie ocenia jakości** [DOK — poradnik branżowy] |

🔴 **Zapisane w zamówieniu = masz. Niezapisane = loteria, także na Alibabie.**
Ogólniki typu „high quality" są praktycznie nie do wyegzekwowania [DOK — poradnik branżowy].

### 2.2 Scenariusz 3 — „podobny w 95%"

Te 5% różnicy to zwykle właśnie środek: układ radiowy, firmware, układ pomiarowy.
Obudowa może być identyczna **[DEDUKCJA]**.

Zamiast szukać „takiego samego":

1. spisz **kartę wymagań** (sekcja 3, krok 1)
2. weź próbki od **2–3 dostawców naraz**
3. sprawdź wszystkie **tą samą listą**
4. wygrana próbka staje się **wzorcem w zamówieniu**: produkcja identyczna z próbką,
   zmiana układu, płytki albo firmware'u tylko za Twoją pisemną zgodą

To zwykła praktyka produkcyjna („golden sample"), nie specjalny program Alibaby.

### 2.3 Scenariusz 4 — nowe urządzenie, którego nikt nie rozgryzł

Mniejszy problem, niż się wydaje:

- nikt nie łamie szyfrowania — „rozgryzanie" to odgadywanie, co znaczą pola w danych
  Tuya — [zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md)
- Zigbee2MQTT **sparuje każde urządzenie Zigbee**, także nieobsługiwane [DOK]
- przy urządzeniu na **standardowych klastrach** Z2M sam generuje definicję — mapuje
  wystawione klastry na funkcje zgodnie ze specyfikacją ZCL [DOK]
- przy **Tuya** (`manuSpecificTuya`) Z2M ma osobną instrukcję i narzędzia do odkrywania
  funkcji [DOK]. W praktyce: przełączasz funkcję na urządzeniu i patrzysz w logach, który
  DP się zmienił **[DEDUKCJA]**
- obsługę dopisujesz jako **external converter — plik JavaScript** w katalogu obok
  konfiguracji Z2M; działa tak samo jak obsługa wbudowana [DOK]. To Twój język

🟢 **Zamontowane urządzenie nie zmieni się samo.** Firmware urządzenia Zigbee aktualizuje
się przez sieć Zigbee, a tą zarządza Twój koordynator — chmura producenta nie ma do niego
drogi **[DEDUKCJA]**. Zmienić się może **następna partia**, dlatego każdą nową partię
sprawdzasz (sekcja 3, krok 5).

### 2.4 Scenariusz 5 — „Tuya dzwoni do chmury"

| Protokół | Czy może dzwonić | Co robisz |
|---|---|---|
| **Zigbee** | ❌ nie ma internetu, gada tylko z koordynatorem | nic — przy własnym dongle'u chmury Tuya po prostu nie ma |
| **Matter over Wi-Fi** | ⚠️ może próbować | blokujesz mu internet na routerze; sterowanie Matter jest lokalne i działa dalej — pułapki (VLAN, mDNS, router klienta): [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md) |

**„Blokuje fajne funkcje" to nie chmura, tylko firmware** — urządzenie tych funkcji
w ogóle nie wystawia. Wracasz więc do scenariuszy 1 i 2: sprawdzasz przed zakupem albo
zamawiasz w specyfikacji.

---

## 3. Sito — procedura w pięciu krokach

Robisz ją **dla kategorii urządzenia**, nie dla marki.

### Krok 1 — karta wymagań, zanim otworzysz sklep

Przykład:

> **Listwa z pomiarem**
> **Musi:** 4 gniazda z uziemieniem sterowane osobno · pomiar mocy i energii **każdego
> gniazda** · pomiar widoczny **w moim hubie, lokalnie, bez aplikacji producenta** ·
> jedna z dróg: Zigbee na standardowych klastrach / Matter z `0x0090` + `0x0091` /
> udokumentowane lokalne API
> **Nie może:** wymagać konta u producenta do działania
> **Mile widziane:** przycisk na obudowie, pamięć stanu po zaniku prądu

🎯 Szukasz **tylko** tego, co pasuje do karty. To kończy błąkanie.

### Krok 2 — sprawdzenie przed zakupem (ok. 10 minut)

| Droga | Gdzie sprawdzasz | Szczegóły |
|---|---|---|
| Zigbee | `zigbee2mqtt.io` — model, kod `_TZ…`, lista „exposes" | [gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md) |
| Matter | `matter-survey.org` — potrzebne klastry; DCL — prawdziwy certyfikat | [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md) |
| Wi-Fi | dokumentacja lokalnego API albo fabrycznie otwarty firmware | [przyjazni-producenci-zamiast-tuya.md](przyjazni-producenci-zamiast-tuya.md) |

🚩 „Tuya" / „Smart Life" w opisie · „energy monitoring" bez „przez Matter" · kilka wersji
pod jednym zdjęciem.

**Nigdzie nie ma?** Nie odrzucasz od razu — ale kupujesz **wyłącznie jako eksperyment**,
nie jako sprzęt dla klienta.

### Krok 3 — 1–2 sztuki do testu

Oficjalny sklep marki na AliExpress albo polski sklep z 14 dniami na zwrot —
[gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md), sekcja 6.

### Krok 4 — test i „odcisk palca"

Test akceptacyjny już istnieje: odcięcie WAN, szczelność, niezależność od producenta,
klasy A/B/C — [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md).
Do tego pytanie z [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md): czy pomiar
działa w hubie, lokalnie, bez aplikacji producenta.

Po teście **zapisujesz dokładnie, co przeszło**:

| Zapisz | Skąd |
|---|---|
| model i manufacturer name (`_TZ…`) | Zigbee2MQTT, strona urządzenia |
| wersja firmware'u | Zigbee2MQTT / panel WWW / kontroler Matter |
| Matter: VID i PID | kontroler Matter, DCL |
| sklep, link do aukcji, data zakupu | — |
| wynik i klasa A/B/C | test akceptacyjny |

### Krok 5 — pilnowanie wyniku

**Sprzęt ze sklepu:**
- każda nowa dostawa: porównujesz manufacturer name i wersję firmware'u z zapisem,
  **zanim** sprzęt trafi do klienta
- inaczej niż w zapisie = test od nowa, nie „pewnie działa"
- ⚠️ ten sam sprzedawca nie gwarantuje tej samej wersji **[DEDUKCJA]**

**Zamówienie z fabryki:**
- karta wymagań po angielsku **w zamówieniu** (sekcja 4)
- zatwierdzona próbka jako wzorzec, zmiany tylko za pisemną zgodą
- płatność przez Trade Assurance — inaczej ochrona nie działa
- przed wysyłką partii: kilka losowych sztuk sprawdzonych tą samą listą — próbki z partii
  do Ciebie albo firma inspekcyjna na miejscu (płatna usługa)

🟢 **[DEDUKCJA] Z czasem lista sprawdzonych urządzeń staje się kapitałem firmy** —
cenniejszym niż logo na pudełku, bo nie da się jej skopiować jednym zamówieniem.

---

## 4. Szablon wymagań do fabryki

Do wklejenia w zapytanie i do zamówienia. **[DO SPRAWDZENIA]** fabryka może części nie
spełnić albo podnieść MOQ — lepiej dowiedzieć się tego przed przelewem.

### Wariant Zigbee

```text
Mandatory requirements (part of the purchase order):

1. Zigbee 3.0, standard ZCL clusters only:
   On/Off 0x0006, Electrical Measurement 0x0B04, Metering 0x0702.
   No Tuya cluster 0xEF00 (manuSpecificTuya).
2. Power, voltage, current and energy reported automatically
   (configurable attribute reporting), not only when polled.
3. Full operation with a third-party coordinator (Zigbee2MQTT),
   without Tuya gateway, app or cloud account.
4. Separate measurement for every outlet (one endpoint per outlet).
5. OTA image files delivered to the buyer for every firmware release
   for at least 5 years, installable locally without cloud.
6. No change of chip, PCB or firmware versus the approved sample
   without the buyer's written approval.
7. Documents: test reports for LVD, EMC, RED including EN 18031,
   RoHS, and the technical documentation needed for the buyer's
   EU Declaration of Conformity.
```

### Wariant Matter

```text
1. Matter 1.3 or newer, CSA certified, listed in the DCL.
2. Electrical Power Measurement (0x0090) and Electrical Energy
   Measurement (0x0091) on every outlet endpoint.
3. Commissioning directly by a third-party controller via QR code,
   without vendor app or account.
4. Full local operation with internet access blocked.
5. Points 5-7 as in the Zigbee variant.
```

Skąd punkty:
- Zigbee 1–2 → [zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md) i wyłączone
  raportowanie w Tuya — [przyjazni-producenci-zamiast-tuya.md](przyjazni-producenci-zamiast-tuya.md)
- Zigbee 5 → CRA (sekcja 5.3); Zigbee2MQTT umie wgrywać firmware z **lokalnego indeksu OTA**
  (plik w katalogu danych albo adres URL) [DOK]
- Zigbee 7 → obowiązki producenta (sekcja 5.3)
- pozostałe pytania do fabryki (chmura, własność kodu, wyłączność):
  [sonoff/09-odm-partnership.md](sonoff/09-odm-partnership.md)

---

## 5. Shelly i Sonoff czy własne logo — uczciwy rachunek

### 5.1 Nie jesteś skazany na dwie marki

Przez sito przechodzi dużo więcej niż Shelly i Sonoff:

| Półka | Co gwarantuje | Przykłady | Notatka |
|---|---|---|---|
| Lokalne API w dokumentacji | obietnica na piśmie | Shelly, Sonoff MINI-1GSP i SPM, BleBox | [shelly/README.md](shelly/README.md), [marki-alternatywne-i-polskie.md](marki-alternatywne-i-polskie.md) |
| Standardowe Zigbee | działa bez producenta | Sonoff SNZB, IKEA INSPELNING, Bituo | [zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md) |
| Matter z potwierdzonym pomiarem | pomiar przez standard | IKEA GRILLPLATS, Tapo P110M, Eve | [przyjazni-producenci-zamiast-tuya.md](przyjazni-producenci-zamiast-tuya.md) |
| Otwarty firmware z fabryki | nie ma czego odblokowywać | Athom, Nous A5T, „Made for ESPHome" | [tanie-listwy-z-pomiarem.md](tanie-listwy-z-pomiarem.md) |
| Przewód w rozdzielnicy | brak sieci = brak chmury | Eastron, F&F, ORNO | [strategia-modbus-rs485.md](strategia-modbus-rs485.md) |

### 5.2 Za co płacisz w cenie Shelly

| W cenie Shelly | Przy własnym logo robisz sam |
|---|---|
| lokalne API opisane w dokumentacji | wymuszasz je w zamówieniu i sprawdzasz na próbce |
| aktualizacje firmware'u | zdobywasz pliki od fabryki i wgrywasz je u klientów |
| badania i deklaracja zgodności | zbierasz raporty, deklarację podpisujesz na siebie |
| gwarancja i reklamacje | magazyn, zwroty, wysyłki |
| odpowiedzialność za wadę | Twoja |

**[DEDUKCJA]** Różnica w cenie to więc nie tylko marża za logo Shelly, ale w dużej części
zapłata za rozwiązanie Twoich pięciu obaw.

### 5.3 🔴 Własne logo nie usuwa obaw — przenosi je na Ciebie

| Od kiedy | Co | Kogo dotyczy |
|---|---|---|
| zasada ogólna | kto sprzedaje produkt **pod własną nazwą lub marką**, staje się **producentem** — musi mieć dokumentację techniczną, raporty z badań i certyfikaty [DOK — Blue Guide] | każdy sprzęt z Twoim logo |
| **1.08.2025** | RED: wymagania cyberbezpieczeństwa dla sprzętu radiowego, który łączy się z internetem **bezpośrednio albo przez inne urządzenie**, np. hub; normy EN 18031 obejmują m.in. bezpieczne aktualizacje [DOK] | Wi-Fi, Zigbee, Thread, Bluetooth |
| **11.09.2026** | CRA: zgłaszanie aktywnie wykorzystywanych luk i poważnych incydentów — wczesne ostrzeżenie w 24 h, zgłoszenie w 72 h; **także sprzęt sprzedany wcześniej** [DOK] | producent |
| **11.12.2027** | CRA w pełni: wymagania cyberbezpieczeństwa, ocena zgodności, oznakowanie CE, **poprawki bezpieczeństwa przez okres wsparcia — co najmniej 5 lat**, chyba że urządzenie ma być używane krócej [DOK] | producent, nowy sprzęt |
| przed sprzedażą | rejestracja w bazie **EPREL** i etykieta energetyczna [DOK] | **żarówki** (źródła światła) |
| przed sprzedażą | wpis do **BDO**, ewidencja masy sprzętu, sprawozdanie do 15 marca, opłaty [DOK — poradniki branżowe] | wprowadzający sprzęt w PL, **także importer** |

**Importujesz z Chin pod chińską marką?** Jesteś **importerem** — też masz obowiązki
dotyczące zgodności. **Pod swoją marką?** Stajesz się producentem [DOK — Blue Guide].

🟢 **Dopóki kupujesz prywatnie pojedyncze sztuki do własnych testów, ta tabela Cię nie
dotyczy** — obowiązki zaczynają się, gdy sprzęt trafia do klienta **[DEDUKCJA]**.

🎯 **Najważniejsze pytanie przy „100 żarówkach z logo" nie brzmi „ile kosztuje sztuka",
tylko „kto przez 5 lat wydaje poprawki firmware'u i jak trafią do żarówek u klientów?".**
Jeśli fabryka — musi to być w umowie (szablon, punkt 5). Jeśli nikt — nie spełnisz
wymagań CRA **[DEDUKCJA]**.

⚠️ **To samo dotyczy Twojego huba.** Sprzedawany pod Twoją marką to sprzęt
z oprogramowaniem i połączeniem sieciowym, czyli najpewniej produkt objęty CRA
**[DEDUKCJA]**. Te obowiązki i tak Cię czekają — a każde własne urządzenie dokłada
**kolejną linię produktów do utrzymania przez lata**, nie tylko 100 sztuk w magazynie.

### 5.4 Kiedy własne logo ma sens

Nie „nigdy" — tylko później i w określonej formie:

- [ ] wiesz, które urządzenie montujesz u prawie każdego klienta —
      [ocena-szczera-modelu.md](ocena-szczera-modelu.md), faza 3
- [ ] niska klasa ryzyka — [strategia-modbus-rs485.md](strategia-modbus-rs485.md), sekcja 9
- [ ] Zigbee na standardowych klastrach — firmware aktualizujesz lokalnie przez Z2M,
      bez chmury fabryki — [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md)
- [ ] fabryka zaakceptowała szablon z sekcji 4, łącznie z plikami OTA i raportami
- [ ] obok ceny sztuki policzone: badania, próbki, BDO, EPREL przy żarówkach, Twój czas

Wszystkie pięć zaznaczone = to już nie loteria, tylko zamówienie.

---

## 6. Otwarte pytania #do-sprawdzenia

- czy urządzenie Tuya Matter over Wi-Fi sparowane kodem QR, bez aplikacji Tuya, próbuje
  łączyć się z chmurą Tuya
- czy zakup pojedynczych sztuk **na firmę** do testów wymaga wpisu do BDO
- czy fabryki Tuya zgodzą się na standardowe klastry i pliki OTA przy małym MOQ
- czy fabryki mają raporty EN 18031 dla urządzeń Zigbee

---

## 7. Jedno zdanie

> **Nie szukaj idealnego sprzętu — zbuduj sito i kupuj tylko to, co przez nie przeszło.
> Własne logo dopiero wtedy, gdy sito pokaże, co naprawdę warto brandować.**

---

## Źródła

**Tuya i Zigbee2MQTT**
- [Tuya — tworzenie produktu (funkcje, DP, PID)](https://developer.tuya.com/en/docs/iot/iot-platform?id=Kaytf2hpelxqs) · [Tuya — funkcje produktu](https://developer.tuya.com/en/docs/iot/define-product-features?id=K97vug7wgxpoq) · [Tuya — funkcje własne](https://developer.tuya.com/en/docs/iot/custom-functions?id=K937y38137c64)
- [Zigbee2MQTT — obsługa nowych urządzeń](https://www.zigbee2mqtt.io/advanced/support-new-devices/01_support_new_devices.html) · [Zigbee2MQTT — nowe urządzenia Tuya](https://www.zigbee2mqtt.io/advanced/support-new-devices/02_support_new_tuya_devices.html) · [Zigbee2MQTT — external converters](https://www.zigbee2mqtt.io/advanced/more/external_converters.html) · [Zigbee2MQTT — aktualizacje OTA, lokalny indeks](https://www.zigbee2mqtt.io/guide/configuration/ota-device-updates.html)

**Zakupy**
- [Guided Imports — Trade Assurance: co chroni, a co nie](https://guidedimports.com/blog/alibaba-trade-assurance-overview/)

**Przepisy**
- [Komisja Europejska — Blue Guide 2022 (Dz.U. C 247)](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ%3AC%3A2022%3A247%3AFULL)
- [SCHUTZWERK — RED: zakres i terminy](https://www.schutzwerk.com/en/compliance/red/) · [SGS — wymagania RED od 1 sierpnia 2025](https://www.sgs.com/en-se/news/2025/06/red-cybersecurity-requirements-mandatory-on-1-august-2025) · [CEN-CENELEC — normy EN 18031](https://www.cencenelec.eu/news-events/news/2025/newsletter/ots-59-cybersecurity-standards/)
- [Komisja Europejska — Cyber Resilience Act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act) · [Komisja Europejska — CRA: obowiązki zgłaszania](https://digital-strategy.ec.europa.eu/en/policies/cra-reporting) · [Freshfields — zgłaszanie od 11 września 2026](https://www.freshfields.com/en/our-thinking/blogs/technology-quotient/cyber-resilience-act-reporting-obligations-take-effect-on-11-september-2026-102nzmk) · [CRA — tekst artykułu 13](https://www.european-cyber-resilience-act.com/Cyber_Resilience_Act_Article_13.html)
- [Komisja Europejska — źródła światła, FAQ (EPREL)](https://energy-efficient-products.ec.europa.eu/faqs/product-faqs/light-sources-faqs_en) · [LightingEurope — baza EPREL](https://www.lightingeurope.org/eprel-database)
- [Asekol — import z Chin a BDO](https://asekol.pl/blog/import-towaru-z-chin-a-bdo-co-musisz-wiedziec/) · [Okiem Inspektora — import elektroniki a BDO](https://okieminspektora.pl/import-elektroniki-a-bdo/)
