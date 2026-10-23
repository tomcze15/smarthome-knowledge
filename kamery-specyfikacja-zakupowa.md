# Kamery — czego szukać w specyfikacji (lista zakupowa)

Gotowa lista wymagań do wysłania handlowcowi w hurtowni.
Uzupełnia [kamery-gdzie-kupowac.md](kamery-gdzie-kupowac.md). Spisane 2026-08-17.

---

## 🎯 Zasada nadrzędna: celuj w ZERO transkodowania

Transkodowanie to przeliczanie strumienia z jednego kodeka na drugi w locie. Kosztuje moc
obliczeniową **przy każdej sesji podglądu, dla każdej kamery**. Przy 10 kamerach w pakiecie
VIP to jest różnica między „działa" a „muli".

**Architektura docelowa — nic się nie transkoduje:**

| Zastosowanie | Strumień | Kodek | Co robi Odroid |
|---|---|---|---|
| **Nagrywanie** | główny, pełna rozdzielczość | **H.265** | kopiuje na dysk bez przeliczania |
| **Podgląd mobilny (WebRTC)** | pomocniczy | **H.264** | przepuszcza bez zmian |
| **Detekcja AI (Frigate)** | pomocniczy | **H.264** | tylko dekoduje, sprzętowo |

🟢 **H.265 na strumieniu głównym jest dobry** — o połowę mniejsze pliki przy tej samej
jakości, czyli dwa razy dłuższa historia na tym samym dysku.

🔴 **Problem zaczyna się, gdy kamera daje WYŁĄCZNIE H.265**, jak BCS-L-PTHOME02.
Wtedy do podglądu w przeglądarce musisz przeliczać.

---

## 1. Kodeki wideo — co znaczą

| Kodek | Ocena | Uwagi |
|---|---|---|
| **H.264 (AVC)** | 🟢 **wymagany** | wspierany wszędzie: przeglądarki, WebRTC, dekodery sprzętowe. Bezpieczny wybór |
| **H.265 (HEVC)** | 🟡 dobry, ale nie sam | ~50% mniejszy plik. Słabo wspierany w WebRTC i przeglądarkach |
| **MJPEG** | 🟡 pomocniczo | żadnej kompresji międzyklatkowej, żre pasmo. Ale działa wszędzie i ma niskie opóźnienie — dobry do podglądu w LAN (Twoja sekcja 6) |
| **H.265+ / H.264+** | ⚠️ uwaga | firmowe warianty „smart codec" (Hikvision, Dahua). Potrafią psuć zgodność z narzędziami zewnętrznymi. **Wyłączać** |
| AV1 | ⚪ rzadkość | w kamerach praktycznie nie występuje |

### 🔴 Wymóg numer jeden: **kodek ustawialny OSOBNO dla każdego strumienia**

To jest ważniejsze niż sama lista kodeków. Chcesz móc ustawić:
- strumień główny → H.265
- strumień pomocniczy → **H.264**

Kamery profesjonalne to potrafią. Konsumenckie często **narzucają jeden kodek na wszystko**.

**Pytanie do handlowca:** *„Czy mogę ustawić H.265 na strumieniu głównym i H.264
na pomocniczym, niezależnie?"*

---

## 2. Strumienie — ile i jakie

| Wymóg | Dlaczego |
|---|---|
| **Minimum 2 strumienie**, najlepiej **3** | główny = nagrywanie, pomocniczy = detekcja, trzeci = podgląd mobilny |
| **Rozdzielczość pomocniczego ≥ 704×576**, lepiej **1280×720** | 640×360 (jak w BCS) wystarczy do detekcji, ale jest za słaby do pokazania klientowi |
| **Regulowany bitrate i FPS per strumień** | detekcja nie potrzebuje 25 fps; 5–10 fps oszczędza moc |

🟢 **Trzy strumienie to duża wygoda przy Frigate** — jeden do zapisu, jeden okrojony
do detekcji AI, jeden średni do podglądu.

---

## 3. Protokoły dostępu — bez tego nie ma rozmowy

| Wymóg | Uwagi |
|---|---|
| **RTSP** niezablokowany, port **554** | podstawa. Bez tego nic nie zrobisz |
| **ONVIF Profile S** | standard streamingu — minimum |
| **ONVIF Profile T** | rozszerzony: **H.265**, metadane, detekcja ruchu. Lepszy |
| ONVIF Profile G | nagrywanie i zapis na urządzeniu — przydatny, opcjonalny |
| Port ONVIF **2020** lub 80 | zależnie od producenta |
| **Snapshot JPEG po HTTP** | do miniatur i powiadomień, bez ruszania strumienia |

**Pytanie do handlowca:** *„Jakie profile ONVIF wspiera ta kamera — S, T, G?"*
Jeśli nie wie albo nie ma w karcie — **to zwykle znaczy, że nie ma ONVIF w ogóle**.

### 3.1 RTSP czy ONVIF — co robi co (uzupełnione 2026-09-18)

🎯 **Najkrócej: RTSP to rura z obrazem, ONVIF to rozmowa z kamerą o wszystkim innym.**
Branżowe ujęcie: „ONVIF is the handshake; RTSP is the pipeline" [DOK].

| | **RTSP** | **ONVIF** |
|---|---|---|
| Czym jest | protokół strumienia wideo i audio | warstwa zarządzania i zgodności (SOAP po HTTP) |
| Co daje | **sam obraz i dźwięk** | wykrycie kamery w sieci, adres strumienia, ustawienia, PTZ, **zdarzenia** |
| Bez tego | **nie ma podglądu** | da się żyć, ale każdy model konfigurujesz ręcznie |

**Co ONVIF realnie załatwia w produkcie [DOK]:**

- **wykrywanie kamer w sieci** — WS-Discovery, multicast UDP na `239.255.255.250:3702`
- **`GetStreamUri`** — kamera sama podaje adres RTSP. Bez tego musisz znać ścieżkę każdego
  producenta (inna u Hikvision, inna u Dahua, inna u Reolinka, inna u Axisa) i utrzymywać
  własną bazę adresów
- **zdarzenia** — ruch, sabotaż, naciśnięcie dzwonka. Home Assistant robi z nich sensory
  przez subskrypcję **PullPoint** albo webhooki
- **audio dwukierunkowe** — backchannel w RTSP działa wg standardu **ONVIF Profile T**;
  `go2rtc` włącza go automatycznie, gdy kamera go ma (sprawdzasz przez „stream probe")

🔴 **Gdy kamera ma RTSP, ale nie ma ONVIF:** obraz owszem, ale adresy wpisujesz ręcznie per
model, a zdarzenia musisz wyliczyć sam z obrazu (Frigate to potrafi, kosztem mocy) **[DEDUKCJA]**.

### 3.2 ⚠️ Profile S wygasa — wymagaj Profile T

- ONVIF ogłosił **9.10.2025** wycofywanie **Profile S**; po **31.03.2027** producenci nie zgłoszą
  już do certyfikacji nowych produktów ani nowego firmware'u na Profile S
  [DOK — poradnik branżowy; **[DO SPRAWDZENIA]** na onvif.org]
- **Profile T** to dzisiejszy standard: H.264 i **H.265**, ustawienia obrazu, zdarzenia ruchu
  i sabotażu, strumień metadanych, **audio dwukierunkowe** [DOK]
- Profile M — metadane i analityka; Profile G — nagrywanie i odtwarzanie na urządzeniu [DOK]

🎯 **Nowa reguła zakupowa: RTSP obowiązkowo + ONVIF Profile T.** Profile S traktuj jako
minimum przejściowe, nie jako cel.

---

## 4. Audio

| Wymóg | Uwagi |
|---|---|
| **G.711 (PCMA/PCMU)** albo **AAC** | oba obsługiwane przez `go2rtc` i WebRTC |
| ⚠️ G.726 i formaty firmowe | mogą wymagać transkodowania |
| **Audio dwukierunkowe** (backchannel) | jeśli chcesz sprzedawać domofon/interkom — sprawdź, czy `go2rtc` obsługuje ten model |

Kamera BCS ma G.711A — to akurat OK.

---

## 5. Reszta wymagań technicznych

| Wymóg | Dlaczego |
|---|---|
| **PoE** przynajmniej w jednym modelu | jeden kabel = zasilanie i dane; brak problemów z Wi-Fi (pakiet VIP) |
| **Slot microSD** | pakiet BASIC opiera się na zapisie lokalnym |
| **Zasilanie 12 V DC albo PoE 802.3af** | unikaj 5 V microUSB — to sprzęt konsumencki, kiepskie zasilacze |
| **IP66/IP67** dla zewnętrznych | |
| **Konfiguracja przez interfejs WWW** | patrz sekcja o bindingu w [kamery-gdzie-kupowac.md](kamery-gdzie-kupowac.md) |
| **Bez wymuszonego konta w chmurze** | warunek konieczny |

---

## ⚠️ 6. Doprecyzowanie: na Rockchipie transkodowanie boli mniej, niż pisałem

Wcześniej napisałem, że H.265 → transkodowanie → obciążenie CPU. **To było uproszczenie.**

**Odroid M1S stoi na Rockchipie (RK3566), który ma sprzętowy VPU** — potrafi dekodować
H.264 i H.265 oraz kodować H.264 **bez udziału CPU**. Czyli transkodowanie H.265→H.264
może być w dużej części sprzętowe.

🔴 **Ale to nie znaczy „problem nie istnieje":**

- wykorzystanie VPU wymaga stosu **ffmpeg z `rkmpp`** — konfiguracja bywa upierdliwa,
  a wsparcie w gotowych obrazach Dockera niepełne
- Frigate ma detektor `rknn` dla Rockchipa, ale **[DO SPRAWDZENIA]**, jak to współgra
  z transkodowaniem w `go2rtc` na tej samej maszynie
- nawet sprzętowe transkodowanie ma **skończoną przepustowość** — VPU obsłuży kilka
  strumieni, nie kilkanaście

**[DEDUKCJA] Wniosek: unikaj potrzeby transkodowania przez dobór kamery — to darmowe.
Sprzętowy VPU trzymaj jako zapas, a nie jako plan.** Kamera z H.264 na substreamie
kosztuje tyle samo, co kamera bez niego.

---

## 📋 7. Gotowa wiadomość do hurtowni

> Dzień dobry,
>
> szukam kamer IP do wdrożeń w domach i mieszkaniach, integrowanych z własnym systemem
> (Home Assistant / Frigate). Proszę o modele spełniające:
>
> 1. **RTSP** niezablokowany (port 554), bez konta w chmurze producenta
> 2. **ONVIF Profile S**, najlepiej też **T**
> 3. **Kodek ustawialny osobno per strumień** — H.265 na głównym, **H.264 na pomocniczym**
> 4. Minimum 2 strumienie, pomocniczy **min. 1280×720**, regulowany bitrate i FPS
> 5. Audio **G.711** lub AAC
> 6. Slot **microSD**
> 7. Konfiguracja przez **interfejs WWW**, bez aplikacji mobilnej
> 8. Warianty: jeden **Wi-Fi**, jeden **PoE**
>
> Proszę też o informację o progach rabatowych i możliwości dropshippingu.

---

## 8. Szybki test po zakupie — zanim wejdzie do oferty

```bash
# 1. Czy RTSP w ogóle stoi
nmap -p 554,2020,80,443 <IP_KAMERY>

# 2. Jaki kodek naprawdę leci (nie co pisze w karcie)
ffprobe -v error -show_streams rtsp://user:pass@<IP>:554/<ścieżka> | grep codec_name

# 3. To samo dla substreamu — TU chcesz zobaczyć h264
```

🎯 **Kluczowy moment: `ffprobe` na strumieniu pomocniczym.** Jeśli pokaże `hevc`,
a nie `h264` — każdy podgląd mobilny będzie kosztować transkodowanie.

Potem wpięcie do `go2rtc` i sprawdzenie, czy WebRTC działa **bez** wpisu `#video=h264`
w konfiguracji (ten wpis wymusza transkodowanie — jeśli jest potrzebny, masz odpowiedź).
