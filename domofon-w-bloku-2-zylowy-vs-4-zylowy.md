# Domofony 2-żyłowe i 4-żyłowe — co to znaczy i co z tym zrobić w bloku

Spisane 2026-09-18. Źródła: DNAKE, poradniki instalatorskie PL, Urmet, Dahua (system 2-Wire
Hybrid), DoorBird (konwerter A1072, moduł D301), Nuki, sklepy PL.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [kamery-wideodomofony.md](kamery-wideodomofony.md) (przegląd marek domofonów) ·
[kamery-specyfikacja-zakupowa.md](kamery-specyfikacja-zakupowa.md) (RTSP vs ONVIF) ·
[local-first.md](local-first.md)

---

## 1. O co chodzi w tych żyłach

To po prostu **liczba przewodów między panelem przy drzwiach a urządzeniem w mieszkaniu**.

| | **4-żyłowy** | **2-żyłowy (magistrala)** |
|---|---|---|
| Zasada | analogowy, **osobny przewód do każdej funkcji**: mikrofon, głośnik, wywołanie, rygiel, zasilanie [DOK] | cyfrowy, **wszystko jedną parą**: dźwięk, obraz, zasilanie i sterowanie [DOK] |
| Zasilanie | osobną parą | **tą samą parą co sygnał** („Power over Wire") [DOK] |
| Topologia | punkt–punkt: panel → monitor | **magistrala z adresowaniem**, wiele monitorów i paneli [DOK] |
| Polaryzacja | trzeba pilnować | u części producentów **nie ma znaczenia**, np. Urmet 2Voice [DOK] |
| Typowe zastosowanie | tanie zestawy do domu jednorodzinnego | **bloki i budynki wielorodzinne**, rozbudowane instalacje |
| Odległości | krótkie | długie, cały pion w budynku |

**[DEDUKCJA] Po ludzku:** 4 żyły to „każdy sygnał własnym drutem", a 2 żyły to „jedna rura,
w której wszystko jedzie razem, a urządzenia mają adresy jak mieszkania".

⚠️ Spotkasz też instalacje 5-, 6- i 8-żyłowe — to warianty analogowe z dodatkowym obrazem
albo drugim wejściem [DOK — poradniki PL].

---

## 2. 🔴 Pułapka: 2-żyłowy to nie jest jeden standard

Każdy producent ma **własną magistralę**: Urmet 2Voice, Comelit Simplebus, Bticino 2 Wire,
Dahua 2-Wire i tak dalej. **[DEDUKCJA]** Urządzeń różnych marek nie wymieszasz — monitor jednej
firmy nie zagra z panelem innej, mimo że „oba są 2-żyłowe".

🎯 **Pytanie, które zawsze zadajesz przed zakupem:** nie „ile żył?", tylko
**„jaki system i czyjej firmy jest ta instalacja?"**

---

## 3. Dwa różne „2-żyłowe": analogowy i IP

| Rodzaj | Co jedzie po żyłach | Czy wpniesz do huba |
|---|---|---|
| **2-żyłowy analogowy** (klasyka w blokach) | dźwięk, obraz i zasilanie w formacie firmowym | ❌ nie wprost — potrzebny moduł pośredniczący (sekcja 4) |
| **2-żyłowy „IP"** | **sieć IP po dwóch żyłach** | ✅ tak, bo na końcu masz normalne IP |

Przykłady drugiego rodzaju [DOK]:

- **Dahua 2-Wire Hybrid** — zasilanie i sygnał po dwóch żyłach, wejście do sieci przez Wi-Fi;
  pomyślane jako modernizacja bez kucia ścian. Model `VTO2202F-P-S2` ma **2-wire i RJ45**
  oraz ONVIF
- **DoorBird A1072** — konwerter **2 żyły → Ethernet PoE+**, zasięg do **80 m**: po starych
  drutach jedzie prąd i dane do nowego panelu IP

**[DEDUKCJA]** To najtańsza droga do domofonu IP w domu, w którym już są dwie żyły — bez
kucia, bez ciągnięcia skrętki.

---

## 4. Blok: czego NIE ruszasz i co możesz zrobić

🔴 **Panel przy wejściu i magistrala w pionie należą do wspólnoty albo spółdzielni.**
Nie wymieniasz ich i nie przerabiasz bez zgody administracji. Twój teren kończy się
na unifonie w mieszkaniu **[DEDUKCJA]**.

Co da się zrobić po swojej stronie:

| Sposób | Co daje | Cena | Uwagi |
|---|---|---|---|
| **Nuki Opener** | wykrywa dzwonek i otwiera bramę wejściową; dopina się do unifonu | **599 zł** [DOK — Komputronik] | Bluetooth, na bateriach; do integracji z hubem potrzebny **Nuki Bridge** z lokalnym HTTP API; 🔴 **najpierw sprawdź listę kompatybilnych domofonów u producenta** [DOK] |
| **DoorBird D301** | zamienia analogowy domofon w IP — mała skrzynka przy unifonie [DOK] | **[DO SPRAWDZENIA]** | producent z UE, API lokalne jak w innych DoorBirdach |
| własny moduł na ESPHome | wykrycie dzwonka i otwieranie przekaźnikiem | grosze | **[DEDUKCJA]** ingerencja w cudzą instalację, do własnych testów, nie do produktu |
| wymiana unifonu na monitor tej samej marki | ładniejszy ekran | — | **nic nie daje integracyjnie** [DEDUKCJA] |

⚠️ Dzwonek z kamerą przy własnych drzwiach (Reolink, Aqara) to **osobne urządzenie** i nie ma
nic wspólnego z domofonem budynkowym. W mieszkaniu często mają sens oba naraz:
domofon budynkowy do bramy, dzwonek z kamerą do własnych drzwi.

---

## 5. Dom jednorodzinny — co wybrać

| Sytuacja | Wybór |
|---|---|
| nowy dom, kładziesz kable | **IP z PoE** — [kamery-wideodomofony.md](kamery-wideodomofony.md) |
| są już dwie żyły od bramy | **konwerter 2-żyłowy → IP** (np. DoorBird A1072) i panel IP |
| jest instalacja 4-żyłowa i budżet ma być mały | zestaw analogowy 4-żyłowy — ale **do huba nie wpniesz go bez modułu pośredniczącego** |

---

## 6. Wniosek dla produktu [DEDUKCJA]

W polskich mieszkaniach **domofon budynkowy już jest** i prawie zawsze jest 2-żyłowy,
firmowy. Dlatego w ofercie potrzebujesz **modułu domofonowego**, a nie wymiany panelu:
- w pakiecie: urządzenie typu Nuki Opener albo DoorBird D301 + integracja w hubie
- w opisie dla klienta: „otwierasz bramę z apki i widzisz, kto dzwonił" — bez dotykania
  instalacji wspólnoty
- 🔴 przed wyceną **zawsze sprawdzasz markę domofonu w klatce** — to decyduje o zgodności

---

## 7. Otwarte pytania #do-sprawdzenia

- lista domofonów zgodnych z Nuki Opener — które marki spotykane w polskich blokach na niej są
- cena i dostępność DoorBird D301 w Polsce
- czy Nuki Bridge wystarczy do pełnej integracji w hubie, czy potrzebna jest chmura Nuki
- jakie systemy dominują w polskich blokach (Laskomex, Cyfral, Urmet, Proel, Aco) i czy
  któryś ma udokumentowany interfejs

---

## 8. Jedno zdanie

> **4 żyły to analogowy punkt–punkt do domu jednorodzinnego, 2 żyły to firmowa magistrala
> w blokach — a że jest firmowa, do własnej apki wchodzisz przez moduł przy unifonie,
> a nie przez wymianę panelu.**

---

## Źródła

- [DNAKE — domofony 2-żyłowe a IP](https://dnake-global.pl/wideodomofon-2-wire-vs-ip-porownanie-systemow/) · [Poland IT Hub — jak podłączyć domofon 2, 4, 5, 6, 8-żyłowy](https://polandithub.pl/jak-podlaczyc-domofon-2-4-5-6-8-zylowy/) · [RM Solar — ile żył do domofonu](https://rmsolar.pl/domofon-ile-zyl-potrzeba-do-prawidlowego-podlaczenia/)
- [Urmet — system 2Voice i 4+n](https://www.urmet.com/en-us/Professional/Products/Video-door-phone/Video-door-phone-systems/4-n-Wire-system)
- [Dahua — 2-Wire Hybrid Video Intercom](https://www.dahuasecurity.com/in/5E/2) · [Security Informed — Dahua 2-Wire IP intercom](https://www.securityinformed.com/dahua-technology-2-wire-ip-intercom-audio-video-or-keypad-entry-technical-details.html)
- [DoorBird — konwerter 2-żyłowy A1072](https://www.doorbird.com/en/blog/614/modernization_made_easy_-_with_the_doorbird_2-wire_converter) · [DoorBird D301 — IP upgrade dla analogowego domofonu](https://shop.domo-supply.com/en/modules-supplementaires/1121-doorbird-door-intercom-ip-upgrade-d301a.html)
- [Komputronik — Nuki Opener, 599 zł](https://www.komputronik.pl/product/755278/nuki-opener.html) · [MarkLabs — Nuki w Home Assistancie](https://marklabs.pl/nuki-home-assistant-integracja/)
