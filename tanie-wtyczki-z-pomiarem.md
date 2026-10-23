# Tanie wtyczki z pomiarem energii — tańsza droga niż listwa Shelly

Spisane 2026-09-14. Ceny z polskich sklepów (Allegro, Ceneo, Media Expert, Eltrox, IKEA)
w dniu sprawdzenia — **orientacyjne, zmieniają się**. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md) ·
[gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md) ·
[zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md)

➡️ **Szukasz listwy, nie wtyczki?** [tanie-listwy-z-pomiarem.md](tanie-listwy-z-pomiarem.md)

---

## 1. Najpierw rachunek: listwa Shelly wcale nie jest droga

**Shelly Power Strip 4 Gen4** — w polskich sklepach **ok. 190–220 zł** (Eltrox, Allegro,
Ceneo — 2026-09-14) za **cztery** gniazda, każde z osobnym sterowaniem i pomiarem.
Czyli **ok. 50 zł za gniazdo**.

> 🔴 **KOREKTA 2026-09-14:** wcześniej stało tu „rząd 250 zł, ok. 60 zł za gniazdo" —
> szacunek z przeliczenia sugerowanej ceny 59,90 €. Realne ceny w Polsce są niższe.

Tanie wtyczki poniżej kosztują **40–72 zł za sztukę**.

🎯 **„Dużo taniej" wychodzi tylko wtedy, gdy nie potrzebujesz czterech gniazd — albo gdy
bierzesz najtańszą IKEA.** Cztery wtyczki Sonoffa (~256 zł) kosztują już **więcej** niż listwa.

---

## 2. Droga Zigbee — przez Zigbee2MQTT, który już masz

Najlepiej pasuje do obecnego zestawu:
- pomiar czytasz **lokalnie**, bez aplikacji producenta i bez Mattera
- **każda wtyczka to router Zigbee** — wzmacnia sieć, np. dla czujników na baterii

| Wtyczka | Cena | Pomiar | Zigbee2MQTT | Uwagi |
|---|---|---|---|---|
| **IKEA INSPELNING** | **~40 zł** (39,99 zł w IKEA) | ✅ | ✅ obsługiwana | wtyczka **typ E**, 16 A / 3680 W · 🔴 **zgłaszano błędne odczyty energii** (zawyżone kWh, zera) zależnie od firmware'u |
| **SONOFF S60ZBTPF** | **~64 zł** | ✅ moc, napięcie, prąd, energia | ✅ **pełna** — te same dane co w eWeLink | 16 A · pasuje do gniazd C, E i F |
| **Nous A1Z** | **~64 zł** | ✅ W, V, A | ✅ lokalnie w Z2M i ZHA | ⚠️ w sprzedaży wersje **10 A i 16 A** — sprawdź przed zakupem |

ℹ️ IKEA pisze, że do śledzenia zużycia potrzebna jest bramka DIRIGERA — to dotyczy
**ich aplikacji**. W Zigbee2MQTT pomiar jest wystawiany bezpośrednio.

---

## 3. Droga Matter over Wi-Fi — do nauki Mattera

| Wtyczka | Cena | Pomiar | **Pomiar przez Matter** | Uwagi |
|---|---|---|---|---|
| **Tapo P110M** | **~64 zł** | ✅ | ✅ od firmware 1.3.2 [DOK] | 3680 W · pierwsza konfiguracja w aplikacji Tapo |
| **Shelly Plug S Gen3** | **od ~72 zł** | ✅ | ❓ niepotwierdzone | 2500 W · ✅ **lokalne API Shelly** — w hubie pomiar i tak dostępny |
| **Shelly Plug M Gen3** | ❓ **[DO SPRAWDZENIA]** | ✅ | ✅ widoczny w matter-survey (`0x0090`) | 3000 W · wg wątku na forum Shelly tańsza od Plug S Gen3 |

🔴 **Shelly Plug PM Gen3 to NIE jest wtyczka do sterowania.** Tylko mierzy — **nie wyłącza
prądu**. Przeznaczona do lodówki, zamrażarki czy serwera, które mają działać bez przerwy.
Łatwo pomylić z Plug S i Plug M.

---

## 4. Najtańszy wariant, jeśli wystarczy suma

Zwykła listwa + **jedna** wtyczka z pomiarem przed nią = zużycie całej listwy za ~40–64 zł.

❌ Ale bez sterowania każdym gniazdem osobno i bez pomiaru poszczególnych urządzeń.

---

## 5. Czego unikać

- **tanie listwy „Zigbee energy monitoring" z AliExpress** (Tuya) — loteria wersji:
  ta sama aukcja, inny kod producenta w środku, a pomiar na każdym gnieździe
  niepotwierdzony — [gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md)
- **„energy monitoring" w tytule bez „przez Matter"** — zwykle znaczy: tylko w aplikacji
  producenta — [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md)

---

## 6. Co wybrać

| Potrzebujesz | Bierz |
|---|---|
| najtaniej, do nauki i testów Zigbee2MQTT | **IKEA INSPELNING** — licząc się z możliwymi błędami odczytu energii |
| pewny, pełny pomiar w Zigbee2MQTT | **SONOFF S60ZBTPF** |
| nauki Mattera z pomiarem | **Tapo P110M** |
| „jak Shelly", z lokalnym API | **Shelly Plug S Gen3** |
| cztery gniazda, każde z pomiarem | **Shelly Power Strip 4 Gen4** — w przeliczeniu na gniazdo nie jest droga |

---

## Źródła

- [Allegro — SONOFF S60ZBTPF](https://allegro.pl/listing?string=sonoff+s60zbtpf) · [Eltrox — S60ZBTPF](https://www.eltrox.pl/p/61842-inteligentne-gniazdko-sonoff-s60zbtpf-zigbee)
- [Zigbee2MQTT — SONOFF S60ZBTPF](https://www.zigbee2mqtt.io/devices/S60ZBTPF.html) · [SmartHomeScene — test S60ZB](https://smarthomescene.com/reviews/sonoff-zigbee-smart-plug-s60zb-review/)
- [Ceneo — Nous A1Z](https://www.ceneo.pl/127586020) · [Takken.io — Nous A1Z](https://takken.io/notes/home-assistant/devices/nous-a1z-16a-power-monitoring-plug)
- [SmartThings Polska — IKEA INSPELNING](https://smartthingspolska.com/2025/07/15/ikea-inspelning-nowe-smart-gniazdko-z-monitorem-zuzycia-energii/) · [MarkLabs — gniazdka IKEA w Home Assistant](https://marklabs.pl/ikea-gniazdka-tretakt-inspelning-energy-dashboard/)
- [Zigbee2MQTT — IKEA E22xx](https://www.zigbee2mqtt.io/devices/E22xx.html) · [zgłoszenie: zawyżone kWh](https://github.com/Koenkk/zigbee2mqtt/issues/27524) · [zgłoszenie: zerowe odczyty](https://github.com/Koenkk/zigbee2mqtt/issues/27855)
- [Ceneo — Tapo P110M](https://www.ceneo.pl/160467991) · [matter-smarthome.de — wtyczki raportujące energię przez Matter](https://matter-smarthome.de/en/practice/which-matter-smart-plugs-report-energy-data/)
- [Ceneo — Shelly Plug S Gen3](https://www.ceneo.pl/177690362) · [Bonmario — Shelly Plug PM Gen3](https://bonmario.com/inteligentne-gniazdko-13a-shelly-plug-pm-gen3-matter-bialy.html)
- [matter-survey.org — klaster 0x0090](https://matter-survey.org/cluster/0x0090)
- [Eltrox — Shelly Power Strip 4 Gen4 White](https://www.eltrox.pl/p/63975-shelly-power-strip-4-gen4-white) · [Ceneo — Shelly Power Strip 4 Gen4 Czarna](https://www.ceneo.pl/192058034)
