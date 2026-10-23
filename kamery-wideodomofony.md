# Wideodomofony (doorbell) — czy da się wpiąć do własnej apki

Spisane 2026-09-18. Źródła: karta katalogowa Dahua VDKP01-BG (PDF producenta), sklep Delta Poznań,
strona produktu Dahua, IP Cam Talk, Home Assistant (integracja Reolink, integracje Dahua/VTO),
SmartHomeScene, sklepy PL. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [kamery-dobor.md](kamery-dobor.md) (marki, go2rtc, Frigate, RODO) ·
[kamery-specyfikacja-zakupowa.md](kamery-specyfikacja-zakupowa.md) (kodeki, strumienie, ONVIF) ·
[kamery-gdzie-kupowac.md](kamery-gdzie-kupowac.md) · [local-first.md](local-first.md) ·
[domofon-w-bloku-2-zylowy-vs-4-zylowy.md](domofon-w-bloku-2-zylowy-vs-4-zylowy.md)
(instalacja domofonowa w bloku: 2 żyły, 4 żyły, moduły typu Nuki Opener)

---

## 1. 🔴 Najpierw sprostowanie: kamera w salonie NIE musi iść przez internet

User założył, że domofon może być lokalny, ale kamera w salonie „musi iść przez neta".
**To nieprawda.**

Kamera IP z RTSP to serwer wideo w Twojej sieci. Telefon w kuchni łączy się z hubem, hub
z kamerą — wszystko po LAN. Internet jest potrzebny **tylko** do oglądania spoza domu.

```
BEZ INTERNETU (w domu)                          Z INTERNETEM (spoza domu)
  telefon ──WiFi──► hub ──RTSP──► kamera          telefon ──Headscale──► hub ──► kamera
            ◄── WebRTC (go2rtc) ──                 (tylko transport, obraz nadal lokalny)
```

🟢 Warstwa transportu już istnieje: **go2rtc** bierze RTSP i wydaje WebRTC, MJPEG, HLS —
[kamery-dobor.md](kamery-dobor.md), sekcja 4.

🔴 **Warunek jest jeden: kamera musi mieć niezablokowane RTSP.** Cała różnica między
„działa bez internetu" a „nie działa" leży w tym jednym punkcie, nie w rodzaju urządzenia.

---

## 2. Dahua VDKP01-BG — co mówi karta katalogowa [DOK]

Zestaw: dzwonek **DB41AB** + bezprzewodowy gong **DS1AP**. Cena w Delta Poznań:
**275 zł netto / 338,25 zł brutto**.

| Parametr | Wartość |
|---|---|
| Rozdzielczość | 2560 × 1440 (4 Mpx) |
| **Strumień główny** | 2560 × 1440 @ **1–15 kl./s** |
| **Strumień pomocniczy** | **640 × 360** |
| Kodek | H.265 / H.264 |
| Obiektyw | 2,3 mm, F2.2; pole widzenia H 120°, D 143° |
| Sieć | **Wi-Fi 2,4 GHz (802.11 b/g/n/ax)**, parowanie przez Bluetooth |
| Zapis | karta microSD do 256 GB |
| Oprogramowanie | **DMSS** (Android, iOS) |
| Zasilanie | 5 V USB-C albo 10–24 V DC / 14–24 V AC |
| Odporność | IP65, PIR + detekcja AI ludzi i pojazdów, audio dwukierunkowe |

### 🔴 Czego w karcie NIE MA

**Ani słowa o ONVIF, RTSP, SDK, P2P, SIP czy API.** Sekcja „Network" wymienia wyłącznie
Wi-Fi, parowanie Bluetooth, kartę microSD i aplikację DMSS [DOK — przeszukana cała karta].

**[DEDUKCJA]** To profil urządzenia konsumenckiego, zbudowanego wokół aplikacji producenta.
Bez RTSP nie wepniesz go do własnej apki inaczej niż przez chmurę Dahua.

⚠️ **[DO SPRAWDZENIA]** Na forum IP Cam Talk pada twierdzenie, że **DB41AB ma ONVIF**.
Sprzeczne z kartą katalogową — rozstrzygnie dopiero test na sprzęcie:
`nmap -p 554,2020,80 <IP>` i próba `ffprobe` na RTSP.

### Co jeszcze zgrzyta przy tym modelu [DEDUKCJA]

- **15 kl./s** na strumieniu głównym — mało jak na ruch przy drzwiach
- **640 × 360** na strumieniu pomocniczym — tyle, co w BCS; wystarcza do detekcji, za mało
  do pokazania klientowi ([kamery-specyfikacja-zakupowa.md](kamery-specyfikacja-zakupowa.md), sekcja 2)
- Wi-Fi zamiast PoE — przy drzwiach wejściowych w bloku często słaby zasięg

---

## 3. Integracje Dahua w praktyce [DOK]

| Droga | Co daje |
|---|---|
| `rroller/dahua` (HACS) | zdarzenia i stany (m.in. naciśnięcie dzwonka) — „not really good for video streams" |
| Integracje Dahua **VTO** (intercom IP) | pełna praca lokalna: RTSP, zdarzenia, audio dwukierunkowe przez go2rtc |
| DMSS | aplikacja producenta, wymaga chmury do pracy zdalnej |

🎯 **Podział, o którym trzeba wiedzieć:** linia **profesjonalna** Dahua (kamery IP, domofony
**VTO**) ma RTSP, ONVIF i SIP — i jest lokalna. Linia **konsumencka** Wi-Fi (dzwonki DMSS,
marka Imou) jest zbudowana wokół chmury. Ta sama marka, dwa różne światy — dokładnie jak
u Sonoffa ([sonoff/08-asortyment.md](sonoff/08-asortyment.md)).

---

## 4. Marka Dahua — stanowisko bez zmian

Pełna analiza: [kamery-dobor.md](kamery-dobor.md), sekcja 1. W skrócie:

- technicznie dobry sprzęt, w linii pro porządne RTSP i ONVIF
- 🔴 ale **FCC rozszerzyła zakaz w czerwcu 2026**, Parlament Europejski usunął Hikvision
  ze swojego budynku, Dania odradza, trend idzie w jedną stronę
- **[DEDUKCJA] Do własnych testów — proszę bardzo. Do oferty sprzedawanej na obietnicy
  prywatności — nie.** To ryzyko pozycjonowania i regulacyjne, nie techniczne

---

## 5. Przegląd marek — wideodomofony, które da się wpiąć lokalnie

Research 2026-09-18. Ceny orientacyjne, z polskich sklepów w dniu sprawdzenia.

### Półka 1 — najlepszy stosunek ceny do lokalności

| Model | Cena PL | Lokalnie | Uwagi |
|---|---|---|---|
| **Reolink D340P** (PoE) | **ok. 400–550 zł** | RTSP + ONVIF, **oficjalna integracja Home Assistant**, działa z Frigate [DOK] | jeden kabel; ⚠️ audio dwukierunkowe bez pełnego wsparcia natywnego [DOK — recenzja] |
| **Reolink D340W** (Wi-Fi) | **ok. 474–560 zł** | jw. [DOK] | Wi-Fi 2,4/5 GHz albo LAN; zasilanie 12–24 V AC/DC |
| **Aqara G410** | **ok. 629 zł** | **RTSP, ale tylko przy zasilaniu przewodowym** [DOK] | 2K, rozpoznawanie twarzy lokalnie, radar mmWave; jest też hubem Matter, Thread Border Routerem i bramką Zigbee — u Ciebie ta część dubluje hub |

### Półka 2 — domofony IP z SIP (kanał instalatorski)

| Marka | Kraj | Protokoły | Cena PL |
|---|---|---|---|
| **Akuvox** (R20B, R28) | 🇨🇳 | SIP + ONVIF + RTSP [DOK] | **[DO SPRAWDZENIA]**; cennik z 2021: R20B X5 — 1170 zł. Dystrybutor: ELFON |
| **Fanvil** (i63) | 🇨🇳 | SIP, RTSP [DOK] | **[DO SPRAWDZENIA]** — fanvil.pl |
| **2N** (IP Solo, IP Verso) | 🇨🇿 grupa Axis | **ONVIF Profile S i T**, RTSP, SIP, **HTTP API** [DOK] | **[DO SPRAWDZENIA]** — SeeCom Polska; półka premium |

### Półka 3 — premium z oficjalnym lokalnym API

| Model | Cena PL | Dlaczego warto znać |
|---|---|---|
| **DoorBird D1101V** 🇩🇪 | **ok. 1690 zł** | **publiczne API LAN-2-LAN** (PDF producenta), RTSP i ONVIF, integracja HA, zero abonamentu [DOK] |

### Ekosystem zamknięty, ale w pełni lokalny

| Model | Cena PL | Uwagi |
|---|---|---|
| **UniFi G4 Doorbell / Pro** | **ok. 1400–1600 zł** + konsola | wymaga konsoli UniFi Protect (Cloud Key G2+, UNVR albo UDM); AI liczy się lokalnie, dobra integracja HA [DOK]; drogo i wiąże z ekosystemem |

### 🚫 Odradzam

| Co | Dlaczego |
|---|---|
| Ring, Nest, Arlo, Blink, Eufy | chmura i abonament — [kamery-dobor.md](kamery-dobor.md), sekcja 2 |
| **Tapo D230S1** | wg społeczności HA brak strumienia wideo w integracji; hub H200 TP-Link traktuje wyłącznie jako mostek Matter [DOK] |
| Dahua VDKP01-BG, Imou, dzwonki Hikvision | brak RTSP w karcie albo bagaż marki — sekcje 2 i 4 |
| **VIDOS** (🇵🇱, system VIDOS ONE IP) | polski producent, ale **[DO SPRAWDZENIA]**, czy wystawia ONVIF/RTSP — w materiałach tego nie znalazłem |

### 🔭 Na horyzoncie: Matter 1.5 — kamery w standardzie

- **Matter 1.5** zamknięty **20.11.2025**: kamery, wideodomofony i interkomy w standardzie —
  wideo na żywo, zdarzenia, detekcja osoby i paczki, audio dwukierunkowe [DOK]
- **Matter 1.5.1** z **31.03.2026** dopracowuje właśnie kamery i dzwonki [DOK]
- na czerwiec 2026 wdrożył to **tylko SmartThings**; Aqara zapowiadała pierwszą kamerę Matter
  na pierwszą połowę 2026 [DOK]

**[DEDUKCJA]** Kierunek ważny dla produktu, ale **dziś nie kupuj pod Matter** — kupuj pod RTSP,
a Matter traktuj jako bonus na przyszłość.

### 🎯 Rekomendacja [DEDUKCJA]

1. **Reolink D340P**, jeśli da się doprowadzić kabel — najtaniej i najpewniej lokalnie
2. **Reolink D340W**, jeśli kabla nie ma
3. **Aqara G410**, jeśli chcesz 2K i rozpoznawanie twarzy lokalnie — pod warunkiem zasilania przewodowego
4. **DoorBird** do pakietu VIP — jedyny z publicznym API i marką z UE
5. **Akuvox / Fanvil / 2N**, gdy klient chce prawdziwy domofon: wiele przycisków, RFID, SIP

---

## 6. Lista kontrolna przed zakupem domofonu

1. **RTSP** niezablokowany (port 554), bez konta w chmurze
2. **ONVIF Profile S** (minimum) — [kamery-specyfikacja-zakupowa.md](kamery-specyfikacja-zakupowa.md)
3. **zdarzenie naciśnięcia dzwonka** dostępne lokalnie (ONVIF events, webhook albo integracja)
4. **audio dwukierunkowe** obsługiwane przez go2rtc dla tego modelu
5. strumień pomocniczy **min. 1280 × 720**, kodek **H.264** ustawiany osobno
6. **PoE** zamiast Wi-Fi, jeśli to możliwe
7. microSD na zapis lokalny
8. test odcięcia WAN po zakupie — [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md)

---

## 7. Otwarte pytania #do-sprawdzenia

- czy DB41AB faktycznie wystawia ONVIF/RTSP (test na sprzęcie)
- czy DMSS pozwala dodać urządzenie po IP w LAN, bez chmury
- ceny i dostępność Dahua VTO oraz DoorBird w Polsce
- czy go2rtc obsługuje audio dwukierunkowe dla Reolink D340P
- wydajność Frigate na docelowym hubie przy domofonie + kamerach ([kamery-dobor.md](kamery-dobor.md), sekcja 4)

---

## 8. Jedno zdanie

> **Nie „domofon lokalnie, kamera przez internet" — obie rzeczy działają lokalnie, jeśli mają
> RTSP. Dzwonek Dahua VDKP01-BG go w karcie nie ma, więc do własnej apki nadaje się Reolink
> PoE albo domofon IP z linii Dahua VTO.**

---

## Źródła

- [Delta Poznań — Dahua VDKP01-BG](https://sklep.delta.poznan.pl/dzwonek-bezprzewodowy-z-kamera-gong-bezprzewodowy-vdkp01-bg-wi-fi-6-4-mpx-dahua_l1_p27474.html) · [Dahua — strona produktu](https://www.dahuasecurity.com/uk/products/All-Products/Video-Intercoms/Video-Doorbell/KIT/VDKP01-BG) · [karta katalogowa PDF](https://www.megateh.eu/files/datasheet/dahua/vdkp01-bg_s0_datasheet_20250725.pdf)
- [IP Cam Talk — wątek o DB41AB](https://ipcamtalk.com/threads/anyone-tried-dahuas-new-video-doorbell-db41ab-w.81956/)
- [GitHub — rroller/dahua (integracja HA)](https://github.com/rroller/dahua) · [Dahua VTO w Home Assistancie](https://github.com/felipecrs/dahua-vto-on-home-assistant) · [go2rtc — wsparcie Dahua VTO](https://github.com/AlexxIT/go2rtc/issues/49)
- [Home Assistant — integracja Reolink](https://www.home-assistant.io/integrations/reolink/) · [SmartHomeScene — Reolink PoE Doorbell i Frigate](https://smarthomescene.com/reviews/reolink-poe-video-doorbell-review-and-frigate-setup-guide/)
- [Reolink Polska — D340P](https://reolink-sklep.pl/pl/p/Reolink-Wideo-Dzwonek-D340P-POE/155) · [Proshop — Reolink Video Doorbell PoE](https://www.proshop.pl/Inteligentny-dom/REOLINK-Video-Doorbell-PoE-Black/3295310) · [Ceneo — D340W](https://www.ceneo.pl/166622992) · [Eltrox — D340W](https://www.eltrox.pl/p/43830-reolink-bezprzewodowy-wideo-dzwonek-d340w-wi-fi-5mpx) · [SmartRTSP — adresy RTSP i ONVIF w dzwonkach Reolink](https://www.smartrtsp.com/guides/reolink-video-doorbell-rtsp-onvif)

**Przegląd marek (sekcja 5)**
- [Aqara — Doorbell Camera Hub G410](https://www.aqara.com/en/product/doorbell-camera-hub-g410/) · [9to5Mac — G410 z HomeKit Secure Video, Matter i RTSP](https://9to5mac.com/2025/07/10/aqara-g410-doorbell-camera-hub-launches-with-homekit-secure-video-matter-and-rtsp-support/) · [Komputronik — G410, 629 zł](https://www.komputronik.pl/product/976865/kamera-ip-do-monitoringu-aqara-doorbel-camera-hub-g410-czarna-kamera-ip-.html)
- [DoorBird — API LAN-2-LAN (PDF)](https://www.doorbird.com/downloads/api_lan.pdf) · [DoorBird Polska](https://doorbird-polska.pl/produkty/d1101v-montaz-natynkowy/) · [Amazon.pl — D1101V](https://www.amazon.pl/DoorBird-D1101V-wideodomofon-przyciskiem-pol%C4%85czenia/dp/B08432MTB6)
- [2N — IP Solo](https://www.2n.com/en-US/products/intercoms/2n-ip-solo/) · [2N — IP Verso](https://www.2n.com/pl-PL/produkty/domofony/2n-ip-verso/) · [SeeCom Polska — 2N IP Verso](https://seecompolska.pl/2n-ip-verso/)
- [Akuvox Polska (ELFON)](https://akuvox-polska.com/wideodomofony-ip-sip-akuvox-panele-zewnetrzne/) · [Fanvil Polska — i63](https://fanvil.pl/fanvil-wideodomofon-sip-i63/)
- [Ubiquiti — G4 Doorbell Pro PoE Kit](https://sklep.ui.pl/unifi-access/1108-UVC-G4-Doorbell-Pro-PoE-Kit.html) · [Home Assistant — integracja UniFi Protect](https://www.home-assistant.io/integrations/unifiprotect/) · [Ceneo — G4 Doorbell Pro](https://www.ceneo.pl/154260375)
- [Tapo D230S1 w Home Assistancie — wątek](https://community.home-assistant.io/t/tapo-d230-video-stream-in-ha/858436) · [HomeAssistant-Tapo-Control — zgłoszenie D230S1](https://github.com/JurajNyiri/HomeAssistant-Tapo-Control/issues/397)
- [VIDOS — system VIDOS ONE IP](https://vidos.pl/produkt/vidosone/)
- [Matter Alpha — Matter 1.5 wprowadza kamery](https://www.matteralpha.com/industry-news/matter-1-5-introduces-camera-support-finally) · [HomeKit News — Matter 1.5.1 dla kamer](https://homekitnews.com/2026/03/31/the-csa-releases-matter-1-5-1-update-for-cameras/) · [Samsung — SmartThings pierwszy z kamerami Matter](https://news.samsung.com/global/samsung-smartthings-becomes-the-industrys-first-to-support-matter-cameras)
