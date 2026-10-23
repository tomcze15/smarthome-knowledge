# Dahua VTO1000JM — analogowy domofon 4-żyłowy: jak działa i co da się z nim zrobić

Spisane 2026-09-18 z dwóch dokumentów producenta: karty katalogowej `DHI-VTO1000JM`
(rev 001.001, 2021) i instrukcji `Analog 4-Wire Intercom User Manual V2.0.0` (styczeń 2026).
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [domofon-w-bloku-2-zylowy-vs-4-zylowy.md](domofon-w-bloku-2-zylowy-vs-4-zylowy.md) ·
[kamery-wideodomofony.md](kamery-wideodomofony.md) ·
[shelly/11-moduly-dopuszkowe-wybor.md](shelly/11-moduly-dopuszkowe-wybor.md) (styki bezpotencjałowe)

---

## 1. Co to za urządzenie [DOK]

**VTO1000JM to panel zewnętrzny, w pełni analogowy.** W karcie katalogowej:

- „Communication Method: **Analog**"
- „Power Supply: **Powered by indoor monitor**"
- kamera 1/4" **1,3 Mpx**, pole widzenia 70° w poziomie i 40° w pionie, auto IR, DWDR
- porty: **Unlock Button 1**, **Door Status Detection 1**
- IK07/IP66, ≤ 1 W w czuwaniu, ≤ 3 W w pracy, CE/FCC

System składa się z dwóch części [DOK — instrukcja, 1.1]:

> „The analog 4-wire video intercom consists of a door station (VTO) and an indoor monitor (VTH).
> […] The VTO is installed outdoors and VTH is installed indoors."

Pasujące monitory: **VTH1020J**, **VTH1020J-T**, **VTH1010J** (7" i 4,3"); pasujące panele:
**VTO1000J**, **VTO1000JM** [DOK].

---

## 2. Jak to działa w praktyce

```
        NA ZEWNĄTRZ                         W MIESZKANIU / DOMU
   ┌────────────────────┐   4 żyły    ┌──────────────────────────┐
   │ VTO1000JM          │────────────►│ VTH (monitor 7" lub 4,3")│
   │ kamera + mikrofon  │ czerwony: + │ ekran, głośnik, przyciski│
   │ głośnik + przycisk │ żółty: wideo│ własny zasilacz 🔌        │
   └────────┬───────────┘ biały: audio└──────────┬───────────────┘
            │             czarny: masa           │
     zamek elektryczny                     do 2 kamer CVBS
     (NO/COM/NC)                           i drugiego panelu
     przycisk wyjścia
```

**Kolory przewodów panelu** [DOK]: czerwony — zasilanie · żółty — **wideo** · biały — audio ·
czarny — masa · zielony — **przycisk wyjścia** · pomarańczowy — **sprzężenie zwrotne (stan drzwi)** ·
fioletowy — NO · niebieski — COM · brązowy — NC.

**Przebieg połączenia** [DOK]:
1. gość naciska przycisk na panelu → monitor dzwoni i pokazuje obraz
2. na monitorze naciskasz przycisk rozmowy, żeby pogadać
3. przycisk z kluczykiem otwiera zamek — **przekaźnik jest w panelu**, nie w monitorze
4. gdy nikt nie dzwoni, przyciskiem podglądu przełączasz widok: VTO1 → VTO2 →
   kamera analogowa 1 → kamera analogowa 2

**Rozbudowa** [DOK]: w jednym systemie do **2 paneli (VTO) i 3 monitorów (VTH)**; monitor ma
porty: kamera analogowa 1 i 2, VTO 1 i 2, dwa porty kaskadowe do kolejnych monitorów, zasilanie.

**Kable** [DOK]: RVV4 — 0,3 mm² do 10 m, 0,5 mm² do 30 m, 0,75 mm² do 50 m.
Zalecane kamery analogowe: **seria HAC 1230** (CVBS).

---

## 3. 🔴 Czego tu NIE ma

**Nie ma sieci.** Ani w panelu, ani w monitorze: żadnego Ethernetu, Wi-Fi, aplikacji,
RTSP, ONVIF ani chmury. W całej instrukcji (28 stron: przegląd, montaż, okablowanie,
menu, ustawienia czasu, reset) **nie pada ani razu** słowo RTSP, ONVIF czy „network port"
— jedyne wzmianki o sieci to szablonowe ostrzeżenia bezpieczeństwa [DOK].

➡️ **Założenie „połączę się z monitorem po Wi-Fi i wezmę RTSP" nie zadziała.** Monitor
jest analogowy: to ekran z głośnikiem, a nie urządzenie sieciowe. Obraz jedzie **żółtą żyłą
jako analogowy sygnał CVBS**, nie pakietami po sieci.

---

## 4. Co da się z tym zrobić

| Sposób | Co daje | Ocena |
|---|---|---|
| **Otwieranie z apki: przekaźnik ze stykiem bezpotencjałowym równolegle do przycisku wyjścia** (zielony przewód) | hub otwiera drzwi | 🟢 **najprostsze i udokumentowane** — panel ma wejście przycisku wyjścia [DOK]. Moduł typu Shelly 1 Mini ma styk bezpotencjałowy |
| **Enkoder CVBS → IP (RTSP)** wpięty w żyłę wideo | obraz z panelu w hubie | 🟡 technicznie możliwe, ale ⚠️ **[DO SPRAWDZENIA]**: panel jest zasilany z monitora, więc prawdopodobnie nadaje obraz tylko w czasie dzwonienia i podglądu — a nie cały czas |
| **Wykrycie dzwonienia** | powiadomienie w apce | ⚠️ brak udokumentowanego wyjścia „naciśnięto przycisk" **[DO SPRAWDZENIA]** |
| **Osobny dzwonek IP przy drzwiach** (Reolink, Aqara) | obraz, zdarzenie, rozmowa w apce | 🟢 najczystsze: stary system zostaje do otwierania, nowy robi resztę — [kamery-wideodomofony.md](kamery-wideodomofony.md) |
| **Wymiana na panel IP** (np. Dahua VTO2202F-P) | wszystko po sieci | 🔴 wtedy monitor VTH1020J przestaje pasować — to inna rodzina |

**[DEDUKCJA] Rekomendacja:** VTO1000JM zostaw jako to, czym jest — solidny domofon do
rozmowy i otwierania. Do apki dołóż **przekaźnik na przycisk wyjścia** (otwieranie) i osobno
**dzwonek IP** (obraz i powiadomienie). Próba wyciągnięcia wideo z analogowej żyły to dużo
pracy przy kiepskim efekcie: 1,3 Mpx, obraz prawdopodobnie tylko w trakcie połączenia.

---

## 5. Otwarte pytania #do-sprawdzenia

- czy panel nadaje sygnał wideo cały czas, czy tylko podczas dzwonienia i podglądu
- dokładne połączenie przycisku wyjścia: zielony do masy czy zielony do pomarańczowego
- czy da się wykryć naciśnięcie przycisku na panelu bez ingerencji w elektronikę
- czy moduł Shelly z serii Mini może pracować na zasilaniu 12 V DC obok monitora

---

## 6. Jedno zdanie

> **VTO1000JM to czysto analogowy domofon: panel na zewnątrz, monitor w środku, cztery żyły
> między nimi i zero sieci — do apki wchodzisz przez przekaźnik na przycisku wyjścia,
> a obraz bierzesz z osobnego dzwonka IP, nie z tego systemu.**

---

## Źródła

- karta katalogowa `DHI-VTO1000JM`, Rev 001.001, 2021 — plik lokalny `~/Downloads/VTO1000JM_datasheet_20210603.pdf`
- `Analog 4-Wire Intercom User Manual V2.0.0`, styczeń 2026 — plik lokalny `~/Downloads/Analog_4-Wire_Intercom_User_Manual_V2.0.0.pdf`
- [Dahua — strona produktu VTO1000JM](https://www.dahuasecurity.com/products/All-Products/Video-Intercoms/4-Wire-Series/Door-Station/VTO1000JM)
