# Kamery — gdzie i JAK kupować w Polsce

Uzupełnienie do [kamery-dobor.md](kamery-dobor.md) (o kryteriach).
Ta notatka jest o **kanale zakupowym i realnym budżecie**.
Spisane 2026-08-17, **skorygowane tego samego dnia** po weryfikacji cen przez usera.

---

## 🔴 0. KOREKTA: pomyliłem kanały dystrybucji

W pierwszej wersji wysłałem do **AAT/NOVUS, BCS, SECAM, Chron.pl**. User sprawdził trzy
z nich: **ceny kamer 1000–3000 zł.**

**To nie jest wina tych hurtowni — to mój błąd w doborze kanału.**

W Polsce (i wszędzie) istnieją **dwa rozłączne światy dystrybucji kamer**:

| | Dystrybucja **zabezpieczeń** | Dystrybucja **IT / elektroniki** |
|---|---|---|
| Kto tam kupuje | instalatorzy alarmowi, agencje ochrony | integratorzy IT, sklepy, sieciowcy |
| Typowy sprzęt | 4K, PoE, varifocal, IP67, gwarancja 3–5 lat | konsumencki i prosumencki |
| **Przedział cen** | **1000–3000 zł** | **150–900 zł** |
| Marki | NOVUS, BCS, IPOX, Hikvision, Dahua | **Tapo, Reolink, TP-Link VIGI** |
| Do czego | obiekty komercyjne, przemysł, instytucje | mieszkania, domy, małe biura |
| Firmy | AAT, BCS, SECAM, Chron.pl, Volta, Janex | **Incom Group, Exclusive Networks, WISP.pl** |

🎯 **Do bloków i mieszkań w budżecie 50–800 zł idziesz do dystrybucji IT, nie do
dystrybucji zabezpieczeń.**

Poprzednia lista zostaje ważna — ale **tylko na wypadek zlecenia komercyjnego**
(biuro, magazyn, wspólnota z wymaganiami), nie do pakietu BASIC.

---

## 1. Właściwy kanał [zweryfikowane 2026-08-17]

| Firma | Co ma | Uwagi |
|---|---|---|
| **Incom Group SA** | **Reolink** i **TP-Link** w polskiej dystrybucji | **B2B, ceny widoczne po rejestracji firmy** — to jest ten mechanizm |
| **reolink-sklep.pl** | oficjalny dystrybutor Reolink w Polsce | kamery, rejestratory, akcesoria |
| **tplinkpolska.pl** | dystrybucja i sprzedaż TP-Link w Polsce | |
| **WISP.PL** | TP-Link, MikroTik, Ubiquiti, Tenda, Teltonika, Cudy | dystrybutor sprzętu sieciowego |
| **Exclusive Networks Poland** | dystrybutor TP-Link | bardziej korporacyjny |
| **Chron.pl** | ma **i** CCTV, **i** TP-Link | jedyny z poprzedniej listy, który spina oba światy |

**[DEDUKCJA] Plan:** zacznij od **Incom Group** (mają Reolink i TP-Link pod jednym dachem)
oraz **WISP.pl**. Rejestracja firmy → ceny netto.

---

## 2. Realne przedziały cenowe i co w nich dostajesz

| Przedział | Co tam jest | Local-first |
|---|---|---|
| **~150–350 zł** | **Tapo** C-series (C210, C310…) | RTSP ✅ ONVIF ✅ — **z zastrzeżeniem, patrz niżej** |
| **~250–700 zł** | **Reolink** (Wi-Fi i PoE) | RTSP ✅ ONVIF ✅ natywnie |
| **~500–900 zł** | **TP-Link VIGI** (wejściowe PoE) | linia profesjonalna TP-Linka |
| 1000–3000 zł | NOVUS, BCS, Hikvision, Dahua | poza Twoim budżetem B2C |

Dla orientacji: Tapo C310 pojawia się w okolicach **~50 €**.

---

## 🔴 3. Tapo — działa lokalnie, ale ma jeden haczyk, który musisz znać

**Dobra wiadomość:** C210 i C310 mają **RTSP i ONVIF**, są w pełni obsługiwane w Home
Assistancie, a strumień RTSP przez kartę WebRTC działa praktycznie bez opóźnienia.

Konkrety techniczne:
- **RTSP: port 554**, **ONVIF: port 2020**
- 🔴 **Do RTSP logujesz się „kontem kamery" (camera account), a NIE loginem do aplikacji
  Tapo** — to jest najczęstsza przyczyna „nie działa mi"
- **ONVIF jest potrzebny do czujnika ruchu** (bez niego masz sam obraz)
- W C310 rozdzielczość strumienia 1 zależy od ustawienia jakości wideo **w aplikacji Tapo**
  — chcesz max, ustawiasz „Best Quality"
- Jest dedykowana integracja HA: `JurajNyiri/HomeAssistant-Tapo-Control`

### ⚠️ Haczyk: „konto kamery" zakłada się w aplikacji Tapo

Czyli Tapo:

| Test | Wynik |
|---|---|
| Działa bez internetu (po konfiguracji) | ✅ **tak** |
| Strumień lokalny bez chmury | ✅ **tak** |
| **Konfiguracja bez konta u producenta** | ❌ **nie — trzeba aplikacji Tapo** |

**[DEDUKCJA] Ocena dla Twojego biznesu:** to jest akceptowalne, **bo konfigurację robisz
Ty, nie klient**. Zakładasz konto serwisowe, ustawiasz „camera account", oddajesz
działający system. Klient nigdy nie widzi aplikacji Tapo.

Ale w klasyfikacji z [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md) to jest
**klasa B, nie A** — i tak trzeba to nazwać, żeby nie kłamać we własnych materiałach.

Reolink pod tym względem wypada lepiej — konfiguracja przez interfejs lokalny.

---

## 4. Twarda prawda o marży na kamerach

Przy kamerze za 150–350 zł i rabacie B2B rzędu kilkunastu–kilkudziesięciu procent
**zarabiasz 30–100 zł na sztuce**.

🔴 **To znaczy, że na samych kamerach biznesu nie ma.** Trzy kamery w pakiecie BASIC
to marża rzędu 100–300 zł — mniej niż jedna godzina pracy elektryka.

**Pieniądze są w:**
- montażu i konfiguracji (robocizna)
- abonamencie
- odpowiedzialności za działającą całość

To dokładnie potwierdza wniosek z [ocena-szczera-modelu.md](ocena-szczera-modelu.md):
**sprzedawaj rezultat, nie pudełka.** Kamery traktuj jako element kosztowy wdrożenia,
nie jako źródło marży.

**[DEDUKCJA]** Rozważ nawet model, w którym **klient kupuje kamery sam** z Twojej listy
klasy A/B, a Ty bierzesz za dobór, montaż i konfigurację. Zero magazynu, zero RMA
za cudzy sprzęt, zero zamrożonego kapitału — a marża i tak była symboliczna.

---

## 5. O co pytać przy zakładaniu konta B2B

**Handlowe:** progi rabatowe · terminy płatności · **dropshipping do klienta końcowego**
(oszczędza magazyn!) · obsługa RMA

**Techniczne:** czy RTSP niezablokowany · czy ONVIF pełny · czy działa bez konta w chmurze
· czy konfiguracja bez aplikacji producenta · zapis na karcie SD · czy aktualizacje
wymagają chmury

---

## 6. Kolejność działań

1. **Incom Group** + **WISP.pl** → rejestracja firmy, ceny netto
2. Kup **po jednej sztuce**: Tapo C310, Reolink (Wi-Fi), Reolink lub VIGI (PoE)
3. **Test odcięcia WAN** na każdej + sprawdzenie RTSP w `go2rtc`
4. Sklasyfikuj: Tapo prawdopodobnie **klasa B**, Reolink celuj w **klasę A**
5. Wybierz **jeden model Wi-Fi i jeden PoE** — nie dziesięć
6. Policz marżę na realnych cenach netto **zanim** oprzesz na niej model finansowy

---

## 7. Studium przypadku: BCS-L-PTHOME02 — dlaczego cena to nie wszystko

Sprawdzone 2026-08-17 na stronie producenta.

**Cena: 119 zł netto / 146,37 brutto.** Idealnie w budżecie. Specyfikacja wygląda dobrze:

5 Mpx (2880×1620) · **Wi-Fi 6** · obrotowa PAN 355°/TILT 85° · **auto-tracking** ·
AI z rozpoznawaniem człowieka i zwierzęcia · mikrofon i głośnik · **microSD do 256 GB** ·
**RJ-45** (jest Ethernet!) · 2 strumienie · IR 10 m

### 🔴 Ale w całej karcie katalogowej NIE MA słowa „ONVIF" ani „RTSP"

Jedyna pozycja opisująca sterowanie brzmi:

> **Obsługa: Mobile App (iOS, android)**

Dla marki z branży CCTV, która przy kamerach profesjonalnych **standardowo wypisuje
profile ONVIF**, brak tej informacji przy linii **BCS Home** jest wymowny.

**[DEDUKCJA] Robocza ocena: to kamera konsumencka, sterowana aplikacją i prawdopodobnie
chmurą — nie kamera ONVIF.** Linia „BCS Home" jest u nich osobna od „Kamery IP".

⚠️ Brak wzmianki to nie dowód. **Ale ciężar dowodu jest po stronie produktu.**

### Jak to rozstrzygnąć w 10 minut

🟢 **Tu akurat masz przewagę: BCS to polska firma z Warszawy** (marka należy do
**NSS Sp. z o.o.**), z telefonem i wsparciem po polsku:

```
tel. +48 (22) 846 25 31 · info@bcs.pl
```

Zapytaj wprost: **„Czy BCS-L-PTHOME02 udostępnia strumień RTSP i ONVIF, czy tylko przez
aplikację? Czy działa bez konta w chmurze?"**

Albo kup jedną sztukę i przeskanuj porty **554** (RTSP) i **2020** (ONVIF).

### ⚠️ Druga pułapka tej kamery: tylko H.265

Karta podaje **„Kompresja wideo: H.265"** i nic więcej. To problem dla Twojej **sekcji 6**:

- **H.265 w WebRTC jest słabo wspierany w przeglądarkach** → do podglądu mobilnego
  trzeba **transkodować**
- Transkodowanie = **obciążenie CPU Odroida**, przy każdej sesji podglądu
- Strumień pomocniczy to tylko **640×360** — wystarczy do detekcji, za mało do podglądu

**Pytaj o H.264 na strumieniu pomocniczym.** Jeśli go nie ma, ta kamera będzie Cię
kosztować moc obliczeniową przy każdym podglądzie — i to się mnoży przez liczbę kamer.

---

## 🔴 8. Problem z Ezviz, który wychwyciłeś — to jest poważne ryzyko biznesowe

> „miałem kamerę kolegi i nie mogłem jej użyć, bo była przypisana do innego użytkownika"

**To jest binding do konta w chmurze** i jest to jedno z większych zagrożeń dla Twojego
modelu, bo dotyczy **floty**, nie pojedynczego urządzenia.

### Co to znaczy operacyjnie

Kamera konsumencka wiąże się z **pierwszym kontem, które ją sparowało**. Skutki:

| Sytuacja | Co się dzieje |
|---|---|
| Klient rezygnuje, odbierasz sprzęt | **kamera przypisana do jego konta** — nie wdrożysz jej u kolejnego klienta bez odwiązania |
| Wymiana gwarancyjna | trzeba odwiązać starą, przypisać nową |
| Klient zapomniał hasła / porzucił konto | urządzenie **martwe**, trzeba przechodzić przez support producenta |
| Parujesz na swoje konto serwisowe | masz kontrolę — ale **całą flotę wiesz na jednym koncie u obcej firmy** |
| Producent zamyka konto albo zmienia regulamin | **cała flota** w jednym momencie |

🔴 **Ostatni wiersz to ryzyko egzystencjalne dla firmy, która obiecuje niezawodność.**

### 🎯 Twoja intuicja o kodzie QR jest trafna — ale znaczenie jest ODWROTNE

Zauważyłeś, że kamera miała kod QR „jak urządzenia Matter over Wi-Fi". Wyglądają tak samo,
a robią coś przeciwnego:

| | **Kod QR w Matter** | **Kod QR w kamerze chmurowej** |
|---|---|---|
| Co zawiera | poświadczenie do **lokalnego** parowania | identyfikator do **powiązania z kontem** u producenta |
| Kto przejmuje urządzenie | **Twoja fabric** — Ty | **chmura producenta** |
| Po resecie fabrycznym | wolne, paruj z kim chcesz | często **nadal przypisane** |
| Kto decyduje o właścicielu | **Ty** | **producent** |

**Ten sam obrazek, przeciwne znaczenie.** Matter używa QR do wpuszczenia urządzenia
do Twojego świata. Ezviz używa QR do wpuszczenia go do swojego.

### 🟢 Rozwiązanie: kamery ONVIF/RTSP nie mają bindingu w ogóle

Kamera ONVIF to **zwykłe urządzenie w sieci z loginem i hasłem, które Ty ustawiasz**.
Nie ma konta u producenta, nie ma właściciela w chmurze, reset fabryczny = czysty sprzęt.

**To jest dokładnie ta właściwość, której potrzebujesz do zarządzania flotą.**

I to jest kolejny argument, żeby w ofercie postawić na **Reolink** i **TP-Link VIGI**
(konfiguracja lokalna) zamiast **Tapo** — Tapo też wymaga konta producenta, więc niesie
to ryzyko w łagodniejszej formie.

### Dopisz to do testu akceptacyjnego

Do poziomu 3 z [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md) dochodzi pytanie:

> **„Czy po resecie fabrycznym mogę wdrożyć to urządzenie u INNEGO klienta,
> bez kontaktu z producentem?"**

Jeśli odpowiedź brzmi „nie" — **to urządzenie nie nadaje się do floty**, niezależnie
od ceny i parametrów. Cena 119 zł przestaje mieć znaczenie, gdy nie możesz odzyskać
sprzętu po rezygnacji klienta.
