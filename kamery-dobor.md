# Kamery — dobór pod produkt local-first

Zebrane 2026-08-17. Kryterium jak zawsze: **nie „najlepsza kamera", tylko „kamera, która
pasuje do modelu biznesowego z local-first i obietnicą prywatności"**.

Powiązane: [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md),
[ocena-szczera-modelu.md](ocena-szczera-modelu.md).

---

## 🔴 1. Zacznijmy od problemu, nie od marek: Hikvision i Dahua

Znasz te marki, bo są największe i najtańsze. **I dokładnie dlatego są dla Ciebie
ryzykowne.**

### Stan faktyczny [zweryfikowane 2026-08-17]

**USA:**
- **NDAA sekcja 889** — zakaz dla agencji federalnych, wykonawców i beneficjentów dotacji
- **FCC** zablokowała nowe autoryzacje sprzętu w **listopadzie 2022**
- **26 czerwca 2026** FCC **rozszerzyła zakaz** na starsze, wcześniej autoryzowane modele
  Hikvision, Dahua, Huawei, ZTE i Hytera — import i marketing zabronione m.in. dla
  bezpieczeństwa publicznego, obiektów rządowych i infrastruktury krytycznej

**Europa — brak polityki unijnej, ale trend jest jednoznaczny:**
- **Parlament Europejski usunął kamery Hikvision** ze swojego budynku (głosowanie 2021),
  powołując się na udokumentowane zarzuty dostarczania sprzętu do obozów dla Ujgurów
  w Xinjiangu
- **Wielka Brytania** — zakaz na obiektach wrażliwych
- **Dania** — druga w Europie z formalnym odradzeniem instalacji Hikvision i Dahua
- **Hiszpania** — temat interpelacji w PE (2025)

NSA i europejscy eksperci ostrzegali przed ryzykiem zdalnego dostępu bez autoryzacji.

### 🔴 Dlaczego to Twój problem, a nie tylko problem instytucji

**Sprzedajesz produkt, którego głównym argumentem jest prywatność i niezależność od
obcych chmur.** Instalowanie w domach klientów kamer marki usuniętej z Parlamentu
Europejskiego za łamanie praw człowieka to **sprzeczność pozycjonowania**, którą
konkurencja albo dziennikarz wyciągnie przy pierwszej okazji.

Do tego dochodzi **ryzyko regulacyjne**: trend idzie w jedną stronę i tylko przyspiesza.
Jeśli za trzy lata Polska albo UE wprowadzi ograniczenia, zostajesz z flotą u klientów,
której nie możesz serwisować ani rozbudowywać.

**[DEDUKCJA] Rekomendacja: nie budować oferty na Hikvision/Dahua.** Nie z powodów
technicznych — technicznie to dobry sprzęt z porządnym RTSP i ONVIF. Z powodu
**pozycjonowania i ryzyka regulacyjnego**.

⚠️ Uwaga na marki-córki i OEM-y: część tańszych marek to przemianowany sprzęt Dahua albo
Hikvision (np. Amcrest to OEM Dahua na rynek USA). Sprawdzaj, kto naprawdę produkuje.

---

## 2. Kryteria doboru — co naprawdę ma znaczenie

| Kryterium | Dlaczego |
|---|---|
| **RTSP niezablokowany** | podstawa. Bez tego nie zrobisz nic lokalnie |
| **ONVIF** | standard: wykrywanie, PTZ, zdarzenia. To dla kamer to, czym ZCL dla Zigbee |
| **Działa bez internetu** | test odcięcia WAN, jak przy każdym innym urządzeniu |
| **Konfiguracja bez konta w chmurze** | inaczej uzależniasz klienta od producenta |
| **Zapis na karcie SD** | Twój pakiet BASIC opiera się na tym |
| **PoE** | jeden kabel = zasilanie + dane, brak problemów z Wi-Fi. Dla VIP praktycznie obowiązkowe |
| **Dystrybucja w UE** | gwarancja, RMA, brak cła |

### 🚫 Marki do wykluczenia z automatu

**Wyze · Blink · Arlo · Ring · Google Nest** — produkty **cloud-first**. ONVIF albo
nie istnieje, albo bywa wyłączany aktualizacją firmware'u. Zbudowane wokół abonamentu
u producenta, czyli konkurują z Twoim abonamentem i jednocześnie łamią Twoją obietnicę.

---

## 3. Marki wg poziomu oferty

### Poziom BASIC / COMFORT — **Reolink**

Najczęstszy wybór w społeczności Home Assistant i Frigate. RTSP, ONVIF, zapis na karcie SD,
warianty PoE i Wi-Fi, sensowne ceny, dostępność w UE.

Kwirki techniczne, warto znać z góry:
- do stabilności zalecany **strumień `http-flv`**, a `rtsp` dokładany jako drugi
  dla dwukierunkowego audio
- nowsze modele obsługują audio dwukierunkowe **przez go2rtc**

⚠️ Też chińska firma — ale **nie jest objęta zakazami** i nie ciągnie za sobą bagażu
Xinjiangu. To kompromis cena/ryzyko, nie rozwiązanie idealne.

### Poziom VIP / SECURITY — marki spoza Chin

Dla klienta, który płaci za najwyższy poziom i któremu sprzedajesz prywatność, warto mieć
w ofercie sprzęt bez tego bagażu:

| Marka | Kraj | Uwagi |
|---|---|---|
| **Axis** | 🇸🇪 Szwecja | wzorzec branży, doskonały ONVIF/RTSP, analityka na urządzeniu; drogo |
| **Bosch** | 🇩🇪 Niemcy | klasa profesjonalna, świetny ONVIF |
| **Hanwha Vision** | 🇰🇷 Korea | dawniej Samsung Techwin, bardzo dobre ONVIF |
| **i-PRO** | 🇯🇵 Japonia | dawniej Panasonic |
| **Vivotek** | 🇹🇼 Tajwan | dobre ONVIF, tańsze niż Axis |

🟢 **To jest naturalne uzasadnienie różnicy cen między pakietami** — VIP dostaje kamery
z UE/Japonii/Korei, BASIC dostaje Reolinka. Klient rozumie taki podział.

**[DO SPRAWDZENIA]** dostępność, ceny i dystrybucja tych marek w Polsce.

---

## 🎯 4. Warstwa software — prawdopodobnie ważniejsza niż marka kamery

To najważniejsza część tej notatki dla Twojej architektury.

### go2rtc — realizuje **dokładnie** Twoją sekcję 6

Twoja specyfikacja wymienia: **MJPEG** (podgląd LAN), **WebRTC** (niskie opóźnienia
mobilnie), **RTSP** (zgodność wsteczna).

**`go2rtc` robi dokładnie to** — przyjmuje RTSP z kamery i wydaje WebRTC, MJPEG, HLS.
Do tego **jedno połączenie do kamery jest restreamowane do wszystkich odbiorców**,
zamiast osobnego połączenia per klient (kamery mają limit sesji!).

🟢 **Nie musisz budować warstwy transportu wideo — ona istnieje i jest otwarta.**

Ciekawostka: autorem jest **AlexxIT** — ten sam, którego integrację SonoffLAN
analizowaliśmy przy BASIC-1GSP.

### Frigate — lokalna detekcja AI, czyli Twój „Cloud Event Vault" bez chmury

NVR z **detekcją obiektów na urządzeniu**. Działa z **dowolną kamerą RTSP** i używa
go2rtc pod spodem.

🎯 **To domyka Twój pakiet VIP w sposób zgodny z obietnicą:** detekcja dzieje się
**lokalnie**, a na VPS przez tunel VPN idzie tylko 10-sekundowy klip zdarzenia.
Żaden ciągły strumień nie opuszcza domu klienta. To jest coś, czego Ring ani Nest
nie zaoferują z definicji.

⚠️ **[DO SPRAWDZENIA] Wydajność na Odroidzie M1S.** Frigate do sensownej pracy chce
akceleratora — Coral TPU po USB/M.2 albo NPU w SoC. Odroid M1S stoi na Rockchipie
i Frigate ma detektor `rknn` dla tych układów, ale **nie weryfikowałem, czy M1S ma
wystarczającą moc dla 3–10 kamer**. To trzeba zmierzyć przed obiecaniem klientowi.

**[DEDUKCJA]** Możliwe, że pakiet VIP z 10+ kamerami będzie wymagał mocniejszej jednostki
niż BASIC — i to jest OK, wręcz uzasadnia różnicę w cenie pakietu.

---

## 🔴 5. Wątek prawny, którego nie wolno pominąć

Kamery nagrywają **ludzi**, więc wchodzisz w RODO na poważnie — inaczej niż przy
przełącznikach i czujnikach.

Do przemyślenia **przed** sprzedażą pierwszego zestawu:

- Przy „Cloud Event Vault" na Twoim VPS **jesteś podmiotem przetwarzającym** dane
  osobowe klienta → potrzebna **umowa powierzenia (DPA)**
- Gdzie stoi VPS, jak długo trzymasz klipy, kto ma dostęp, jak je kasujesz
- Kamera obejmująca **przestrzeń publiczną albo sąsiada** to problem prawny klienta,
  ale **Ty go instalowałeś** — warto mieć to opisane w dokumentacji wdrożenia
- Retencja i procedura na żądanie usunięcia danych

🟢 **Przy tym wszystkim Twoja architektura jest atutem:** domyślnie wszystko zostaje
lokalnie, a do chmury idzie wyłącznie to, na co klient świadomie się zgodzi.
**To jest realny wyróżnik wobec Ring i Nest** — i argument, który da się sprzedać.

---

## Podsumowanie

1. **Nie buduj oferty na Hikvision/Dahua** — nie z powodów technicznych, tylko
   pozycjonowania i ryzyka regulacyjnego
2. **Reolink** jako koń roboczy BASIC/COMFORT
3. **Axis, Bosch, Hanwha, i-PRO, Vivotek** jako uzasadnienie ceny pakietu VIP
4. **Wyklucz** Ring, Nest, Arlo, Blink, Wyze — cloud-first, i konkurują z Twoim abonamentem
5. **go2rtc + Frigate** to gotowa warstwa software — nie pisz jej sam
6. **Zmierz wydajność Frigate na M1S**, zanim obiecasz komukolwiek 10 kamer
7. **Przygotuj DPA i politykę retencji**, zanim sprzedasz pierwszy „Cloud Event Vault"
