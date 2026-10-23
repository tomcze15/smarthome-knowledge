# Dahua VTO1301R-W — panel IP po Wi-Fi: czy nadaje się do naszej apki

Spisane 2026-09-18. Źródła: karta katalogowa producenta `DHI-VTO1301R-W` (pobrana i przeczytana),
strona produktu Dahua, sklep Delta Poznań, integracje Home Assistant.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [dahua-vto1000jm-analogowy-4-zylowy.md](dahua-vto1000jm-analogowy-4-zylowy.md) (wersja analogowa) ·
[kamery-wideodomofony.md](kamery-wideodomofony.md) · [kamery-dobor.md](kamery-dobor.md) (stanowisko wobec marki)

---

## 1. Odpowiedź na pytanie: tak, działa bez monitora

Sklep Delta pisze wprost: **„Może działać bez monitora VTH, tylko z aplikacją mobilną"** [DOK].
Karta katalogowa to potwierdza pośrednio: **`Web Configuration: Yes`**, dostęp przez P2P
i aplikację DMSS — monitor VTH jest opcją, nie warunkiem [DOK].

---

## 2. Co mówi karta katalogowa [DOK]

| Parametr | Wartość |
|---|---|
| Kamera | 1/2.8" CMOS **2 Mpx**, WDR 120 dB, 3D/2D NR, auto IR, ICR |
| Pole widzenia | **H 110° · V 62° · D 133°** |
| Kodeki | H.264, H.265; 768 kbps–8 Mbps; PAL 25 kl./s |
| Strumienie | **główny: 720p**, WVGA, D1, CIF · **pomocniczy: 1080p**, WVGA, D1, QVGA, CIF |
| 🟢 **Interoperacyjność** | **ONVIF; CGI** |
| 🟢 **Protokoły** | **RTSP**; DNS; P2P; UPnP; RTP; RTMP; **SIP**; SSH; HTTP/HTTPS; ONVIF |
| Łączność | **tylko Wi-Fi 2,4 GHz** (802.11 b/g/n) — w karcie **nie ma portu LAN ani PoE** |
| Zapis | karta microSD do 256 GB (zdjęcia i wiadomości wymagają karty w VTO albo VTH) |
| Audio | dwukierunkowe, G.711a/u, PCM, redukcja echa i szumu |
| Alarm | **tylko sabotaż (tamper)** |
| Zasilanie | 12–33 V DC, 1 A; zasilacz opcjonalny; ≤ 3,5 W czuwanie, ≤ 5 W praca |
| Obudowa | PC+ABS, **IP65**, 140,7 × 50,7 × 27 mm, −40 °C do +55 °C, CE/FCC |
| W zestawie | taśma zasilająco-alarmowa, daszek przeciwdeszczowy |

**Cena PL:** 351 zł netto / **431,73 zł brutto** w Delcie — ale tam oznaczony jako
**„produkt wycofany z oferty"**; w innych sklepach bywa dostępny od podobnej kwoty [DOK].

---

## 3. 🟢 Co z tego wynika dla nas — to jest urządzenie sieciowe

W przeciwieństwie do analogowego [VTO1000JM](dahua-vto1000jm-analogowy-4-zylowy.md) ten panel:

- ma **RTSP** → hub pobiera obraz i podaje dalej przez go2rtc do apki
- ma **ONVIF** → wykrycie w sieci, adres strumienia, zdarzenia
- ma **CGI** (firmowe API HTTP Dahua) i **panel WWW** → konfiguracja bez chmury
- ma **SIP** → można go wpiąć w centralę VoIP i odbierać „dzwonek" jak połączenie

Integracje w Home Assistancie: `rroller/dahua` (zdarzenia i stany) oraz osobne integracje
dla serii VTO; strumień przez RTSP/go2rtc [DOK].

---

## 4. 🔴 Trzy rzeczy, które psują obraz

| Problem | Szczegóły |
|---|---|
| **Brak PoE i brak gniazda LAN** | w karcie katalogowej jest wyłącznie Wi-Fi 2,4 GHz. Przy furtce albo drzwiach wejściowych to najsłabsze ogniwo — trzeba doprowadzić 12 V i liczyć na zasięg |
| **Brak sterowanego wyjścia (przekaźnika)** | karta wymienia w sekcji Alarm **tylko sabotaż**; sklep pisze wprost „bramofon nie posiada sterowalnych wyjść". **[DO SPRAWDZENIA]** czy taśma zasilająco-alarmowa nie ma jednak wyjścia zamka — instrukcja 49-stronicowa do sprawdzenia |
| **Dziwne strumienie** | karta podaje **główny 720p**, a **pomocniczy 1080p**. Tak jest w dokumencie producenta; **[DO SPRAWDZENIA]** na urządzeniu, bo wygląda to na zamienione etykiety |

Do tego zostaje stanowisko wobec marki z [kamery-dobor.md](kamery-dobor.md): technicznie w porządku,
ale do oferty budowanej na prywatności — nie.

---

## 5. Jak by to wyglądało w praktyce [DEDUKCJA]

```
panel VTO1301R-W ──Wi-Fi──► router ──► hub (go2rtc: RTSP → WebRTC) ──► apka
        │
        └── otwieranie: OSOBNY moduł przekaźnikowy sterowany z huba
            (panel nie ma własnego wyjścia)
```

To akurat pasuje do naszej architektury: panel daje obraz i rozmowę, a **otwieranie robi
nasz przekaźnik** — czyli kontrola nad zamkiem zostaje po naszej stronie, nie u producenta.

---

## 6. Werdykt [DEDUKCJA]

| Zastosowanie | Ocena |
|---|---|
| **Do testów i nauki** (RTSP, ONVIF, SIP, CGI w jednym urządzeniu za ~430 zł) | 🟢 dobry materiał doświadczalny |
| **Do własnego domu** | 🟡 pod warunkiem dobrego Wi-Fi przy drzwiach i osobnego przekaźnika |
| **Do oferty dla klientów** | 🔴 nie: brak PoE, brak wyjścia zamka, marka odpadła na etapie pozycjonowania. Lepszy **Reolink D340P** — [kamery-wideodomofony.md](kamery-wideodomofony.md) |

Jeśli koniecznie Dahua i IP — patrzeć na modele z **PoE i przekaźnikiem**, np. `VTO2202F-P-S2`
(2-wire + RJ45, ONVIF) **[DO SPRAWDZENIA]** cena i dostępność w PL.

---

## 7. Otwarte pytania #do-sprawdzenia

- czy taśma zasilająco-alarmowa daje jednak wyjście do zamka (instrukcja 49 stron)
- czy RTSP działa bez konta w chmurze i po odcięciu internetu (test odcięcia WAN)
- który strumień jest naprawdę 1080p
- czy model nie został wycofany z produkcji — w Delcie oznaczony jako wycofany z oferty
- czy ONVIF obejmuje zdarzenie „naciśnięto przycisk"

---

## Źródła

- [Dahua — VTO1301R-W](https://www.dahuasecurity.com/products/All-Products/Video-Intercoms/IP-Series/Villa-Door-Station/Lite-Series/VTO1301R-W) · [karta katalogowa DHI-VTO1301R-W (PDF)](https://www.sourcesecurity.com/datasheets/dahua-technology-vto1301r-w-audio-video-or-keypad-entry/co-4261-ga/vto1301r-w.pdf)
- [Delta Poznań — VTO1301R-W, 431,73 zł brutto](https://sklep.delta.poznan.pl/wideodomofon-vto1301r-w-wi-fi-dahua_l1_p19000.html) · [Morele](https://www.morele.net/dahua-technology-wideodomofon-vto1301r-w-wi-fi-dahua-11245518/)
- [Instrukcja VTO1301R-W (49 stron)](https://www.manua.ls/dahua-technology/vto1301r-w/manual)
- [GitHub — rroller/dahua (integracja HA)](https://github.com/rroller/dahua) · [Dahua VTO w Home Assistancie](https://github.com/felipecrs/dahua-vto-on-home-assistant)
