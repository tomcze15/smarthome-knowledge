# Strategia doboru sprzętu pod produkt local-first

Uzupełnienie: [strategia-protokoly.md](strategia-protokoly.md) — Matter vs Thread vs Zigbee.

Notatka do modelu biznesowego (B2C, hub Odroid M1S, Matter + Zigbee, pakiety
BASIC/COMFORT/PRO/VIP). Spisane 2026-08-17 jako wnioski z rozpoznania
[`sonoff/`](sonoff/) i [`shelly/`](shelly/).

---

## Teza: obawa „local-first się nie uda" opiera się na nieporozumieniu

Nieporozumienie brzmi: *„marka X jest chmurowa, więc nie mogę jej sprzedawać
w produkcie local-first"*.

**Local-first nie jest cechą marki. Jest cechą pojedynczego SKU i użytego protokołu.**

Dowód z naszego rozpoznania: ta sama firma (Sonoff) sprzedaje jednocześnie
`BASIC-1GSP` (dane pomiarowe zależne od chmury) i `SNZB-03P` (Zigbee, fizycznie
niezdolny do połączenia z internetem). To nie jest niekonsekwencja producenta —
to dwie różne linie produktowe dla dwóch różnych rynków.

**Twoja specyfikacja już to zakłada**, sekcja 1:

> „fundamentem jest otwarty ekosystem i integracja najlepszych, ogólnodostępnych
> na rynku urządzeń wielomarkowych, natomiast własny sprzęt OEM/ODM wprowadzany
> jest punktowo"

To jest właściwa odpowiedź. Brakuje tylko **kryterium doboru** tych urządzeń —
i o tym jest ta notatka.

---

## Dlaczego architektura z sekcji 2 rozwiązuje problem strukturalnie

Wybrałeś **Matter + Zigbee**. To nie jest wybór wygody — to wybór, który daje
**gwarancję protokołową zamiast obietnicy producenta**:

| Protokół | Gwarancja |
|---|---|
| **Zigbee** | Urządzenie **nie ma Wi-Fi**. Fizycznie nie istnieje droga do chmury producenta. Gwarancja absolutna |
| **Matter** | Ścieżka sterowania działa w lokalnej fabric, bez pośrednictwa chmury. Gwarancja protokołowa |
| Wi-Fi + firmware producenta | Tylko obietnica. Może zniknąć z aktualizacją |

🟢 **Konsekwencja: możesz sprzedawać czujniki Sonoff Zigbee i być w 100% local-first.**
Problem `BASIC-1GSP` istnieje wyłącznie w torze „Wi-Fi + własny protokół producenta" —
czyli w torze, którego Twoja architektura w ogóle nie używa.

Innymi słowy: **wybierając Matter i Zigbee już wykluczyłeś klasę urządzeń, która
sprawia kłopot.** Zostaje tylko dyscyplina przy zakupach.

---

## 🔴 Pułapka Matter, o której trzeba wiedzieć przy pozycjonowaniu na prywatność

**Matter gwarantuje lokalną ścieżkę STEROWANIA. Nie gwarantuje, że urządzenie
nie rozmawia z chmurą producenta równolegle.**

Urządzenie Matter over Wi-Fi ma pełnoprawne Wi-Fi i nic nie stoi na przeszkodzie, żeby
firmware producenta jednocześnie:

- utrzymywał połączenie z własną chmurą
- wysyłał telemetrię
- pobierał aktualizacje

Sterowanie z Twojego huba pójdzie lokalnie — ale urządzenie może przy okazji raportować
na zewnątrz. **Jeśli sprzedajesz produkt na obietnicy prywatności, to jest różnica
istotna prawnie, nie tylko technicznie.**

Rozwiązanie jest po Twojej stronie i masz już do tego infrastrukturę: **VLAN dla IoT
z blokadą ruchu wychodzącego do internetu**. Poziom „Dedykowany AP" i „Mesh" z sekcji 7
to umożliwiają. Wtedy nieważne, co firmware chce robić — nie ma dokąd.

⚠️ Ale to nie działa w poziomie **BYON**, gdzie klient ma router operatora bez VLAN-ów.
Tam gwarancję daje wyłącznie Zigbee.

**[DEDUKCJA] Wniosek dla oferty:** w pakiecie BASIC obiecuj prywatność ostrożniej niż
w PRO/VIP, albo w BASIC ogranicz się do Zigbee.

---

## Test akceptacyjny — Twój proces certyfikacji sprzętu

To jest realny produkt uboczny całego rozpoznania: **powtarzalna procedura, która
decyduje, czy urządzenie trafia na Twoją listę kompatybilności.**

### Poziom 1 — test odcięcia (obowiązkowy)

1. Sparuj urządzenie z hubem
2. **Odłącz WAN na routerze**
3. Sprawdź: sterowanie działa? telemetria się aktualizuje? automatyzacje reagują?
4. Odczekaj godzinę — czy stan nie „zamarza"

🔴 To jest dokładnie test, który obalił `BASIC-1GSP`: odczyty mocy zamarzały bez
otwartej aplikacji producenta.

### Poziom 2 — test szczelności (dla pozycjonowania na prywatność)

5. Zrzut ruchu (`tcpdump` na porcie huba albo mirror na switchu) — do jakich adresów
   zewnętrznych urządzenie próbuje się dobijać
6. Zablokuj urządzeniu ruch wychodzący na firewallu — czy nadal działa poprawnie
7. Sprawdź, czy nie ma degradacji funkcji po kilku dniach blokady

### Poziom 3 — test niezależności od producenta

8. **Reset fabryczny → czy da się sparować BEZ aplikacji producenta?**
   (Matter: kod QR prosto do huba)
9. Czy aktualizacja firmware wymaga chmury producenta
10. Czy urządzenie działa po usunięciu konta u producenta

### Klasyfikacja wyniku

| Klasa | Kryterium | Gdzie w ofercie |
|---|---|---|
| **A — Certyfikowane** | Przechodzi wszystkie trzy poziomy | Sprzedaż i wsparcie w każdym pakiecie |
| **B — Kompatybilne** | Poziom 1 tak, poziom 2/3 nie | Lista kompatybilności, bez sprzedaży |
| **C — Niewspierane** | Nie przechodzi poziomu 1 | Jawnie na liście wykluczeń |

🟢 **Publiczna lista wykluczeń to atut marketingowy**, nie przyznanie się do słabości.
Pokazuje, że testujesz naprawdę. Nikt z konkurencji tego nie robi.

---

## Jeśli jednak własny ODM — rób go w Zigbee, nie w Wi-Fi

Twoja specyfikacja słusznie ogranicza własny sprzęt do „pojedynczych, drobnych rozwiązań
i akcesoriów". Przy takim zakresie **wybór protokołu przesądza o całej reszcie**:

| | ODM Wi-Fi | **ODM Zigbee** |
|---|---|---|
| Zależność od chmury dostawcy | trzeba wynegocjować | **nie istnieje** |
| Ryzyko „bricking" po zerwaniu umowy | realne | **zerowe** |
| Kto kontroluje aktualizacje | dostawca | **Ty, przez koordynator** |
| 15 pytań z [`sonoff/09-odm-partnership.md`](sonoff/09-odm-partnership.md) | wszystkie aktualne | **większość odpada** |
| Certyfikacja | CE/RoHS | CE/RoHS (+ Zigbee Alliance jeśli logo) |
| Certyfikacja **Matter (CSA)** | dochodzi koszt i członkostwo | nie dotyczy |

🎯 **Urządzenie Zigbee od ODM jest z definicji local-first, bo nie ma jak nie być.**
To eliminuje cały obszar negocjacyjny, ryzyko prawne i uzależnienie od dostawcy.

**[DEDUKCJA] Rekomendacja:** własny ODM tylko tam, gdzie rynek nie ma dobrej opcji
Zigbee/Matter, albo gdzie branding ma znaczenie — obudowa huba, firmowy przycisk,
akcesorium. Nie w przekaźnikach i czujnikach, gdzie rynek jest nasycony i towarowy.

---

## Reseller czy lista kompatybilności — decyzja o ryzyku

| | Odsprzedaż sprzętu | Lista kompatybilności |
|---|---|---|
| Marża na sprzęcie | ✅ | ❌ |
| Kapitał zamrożony w magazynie | 🔴 | ✅ brak |
| **Gwarancja i RMA** | **na Tobie** | na producencie |
| Ryzyko end-of-life modelu | na Tobie | na kliencie |
| Kontrola nad jakością wdrożenia | ✅ pełna | ograniczona |

⚠️ Przy pakietach z SLA (VIP/SECURITY: „najwyższy priorytet niezawodności") odsprzedaż
oznacza, że **przejmujesz odpowiedzialność za cudzy sprzęt**. Przy jednoosobowej albo
małej firmie to realne obciążenie operacyjne.

**[DEDUKCJA] Model pośredni wart rozważenia:** sprzedajesz **hub + własne akcesoria**,
a resztę klient kupuje sam z Twojej listy klasy A. Zarabiasz na subskrypcji i wdrożeniu,
nie na pudełkach. Zgodne z tym, co masz w sekcji 1.

---

## 🔴 Dwa ryzyka techniczne w specyfikacji, warte uwagi

### 1. Matter na poziomie BYON — największe ryzyko operacyjne

Matter over Wi-Fi wymaga do działania:

- **IPv6** (Matter jest oparty o IPv6, to nie jest opcja)
- **działającego mDNS** w segmencie sieci

Routery operatorów bywają w obu tych rzeczach fatalne — wyłączony IPv6, mDNS nieprzechodzący
między pasmami 2,4 i 5 GHz, izolacja klientów włączona domyślnie.

🔴 **To znaczy, że Twój najtańszy pakiet (BYON) stoi na sprzęcie, nad którym nie masz
kontroli, a od którego zależy podstawowy protokół systemu.** Każde zgłoszenie „nie paruje
się" będzie kosztować Cię czas na diagnozę cudzego routera.

**Rekomendacje:**
- Zrób **listę zweryfikowanych routerów operatorskich** (Orange, Play, Vectra, T-Mobile) —
  które przepuszczają Matter, a które nie
- Rozważ, czy BASIC nie powinien być **wyłącznie Zigbee** (koordynator w hubie = zero
  zależności od routera klienta), a Matter dopiero od COMFORT z dedykowanym AP
- Przy VLAN dla IoT pamiętaj o **reflektorze mDNS** (Avahi) — inaczej Matter i tak nie
  zadziała między segmentami

### 2. Certyfikacja CSA przy własnym urządzeniu Matter

Jeśli ODM miałby być urządzeniem Matter, dochodzi **certyfikacja w Connectivity Standards
Alliance** — członkostwo plus opłata za certyfikację. [DO SPRAWDZENIA] aktualne stawki,
ale to koszt rzędu tysięcy dolarów, nie setek. Kolejny argument za Zigbee przy własnym
sprzęcie.

---

## Podsumowanie w jednym zdaniu

**Local-first Ci wyjdzie — pod warunkiem, że kryterium doboru sprzętu przeniesiesz
z poziomu marki na poziom protokołu i pojedynczego modelu, a to co obiecujesz klientowi
zweryfikujesz testem odcięcia WAN, a nie deklaracją producenta.**
