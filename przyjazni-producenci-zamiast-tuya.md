# Przyjaźni producenci zamiast Tuya — kto nie blokuje pomiarów i trzyma się standardu

Spisane 2026-09-15. Źródła: matter-survey.org, matter-smarthome.de, Zigbee2MQTT, Automated Home,
IKEA, Espressif, ESPHome, fora Hubitat i GitHub. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md) ·
[tanie-wtyczki-z-pomiarem.md](tanie-wtyczki-z-pomiarem.md) ·
[tanie-listwy-z-pomiarem.md](tanie-listwy-z-pomiarem.md) ·
[gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md) ·
[zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md)

---

## 1. Dlaczego Tuya „wykańcza" — to model biznesowy, nie przypadek

- Tuya sprzedaje markom **platformę chmurową** — urządzenia mają rozmawiać z jej chmurą
- dokumentacja Zigbee2MQTT mówi wprost: **Tuya wyłączyła automatyczne raportowanie mocy,
  napięcia i prądu** — trzeba je odpytywać [DOK]
- do tego własny klaster `0xEF00` zamiast standardu i loteria wersji pod jedną aukcją —
  [zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md),
  [gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md)

---

## 2. Twarde dane: kto naprawdę wystawia pomiar przez Matter [DOK]

Baza `matter-survey.org/cluster/0x0090` — **68 urządzeń** z klastrem pomiaru mocy (2026-09-14):

**IKEA · Eve · Tapo · Shelly · Aqara · Kasa · Meross (wtyczka) · Inovelli · Sunricher**

🔴 **Tuya: zero.**

⚠️ Baza jest społecznościowa — brak na liście nie dowodzi braku. Ale zero urządzeń Tuya
przy jej skali jest wymowne **[DEDUKCJA]**.

---

## 3. Trzy rodzaje „łaskawych"

### A. Wielcy i tani, trzymają się standardu

**IKEA — nowa linia na Matter over Thread**

| Produkt | Cena w PL | Uwagi |
|---|---|---|
| **GRILLPLATS** wtyczka | **~39 zł** | pomiar energii **przez Matter potwierdzony** (matter-survey, matter-smarthome od firmware 1.4.6) |
| GRILLPLATS z pilotem | ~45–62 zł | |
| **DIRIGERA** hub | **~205–279 zł** | kontroler Matter i **Thread Border Router** |

- 🔴 **Thread wymaga Border Routera** — wtyczka bez niego się nie sparuje po Matterze
- 🟢 **ukryty tryb Zigbee** [DOK — Automated Home]:
  - żarówki **KAJPLATS**: 6 cykli zasilania → Matter, **12 cykli → Zigbee**
  - piloty **BILRESA**: 4 naciśnięcia resetu → Touchlink, **8 → standardowe Zigbee**
  - po sparowaniu działają w Zigbee2MQTT i ZHA, **bez Border Routera**
- ⚠️ **GRILLPLATS też ma tryb Zigbee** (reset przez przytrzymanie ok. 10 s, potem 8 szybkich
  naciśnięć), ale wg zgłoszeń w Zigbee2MQTT daje **tylko włącz/wyłącz — bez pomiaru** [DOK].
  Zigbee2MQTT ma już stronę modelu E2491 — **[DO SPRAWDZENIA]** aktualny stan obsługi pomiaru
- **[DO SPRAWDZENIA]** czy własny kontroler Matter na RPi5 może korzystać z sieci Thread DIRIGERY

**Tapo P110M** (~64 zł) — Wi-Fi, pomiar przez Matter od firmware 1.3.2 [DOK].
Duży producent, nie wymaga Threada.

### B. Otwarci z konstrukcji — Shelly

- lokalne API, MQTT i skrypty na urządzeniu — [shelly/02-api-rpc-gen2.md](shelly/02-api-rpc-gen2.md)
- pomiar przez Matter widoczny w bazie dla kilku modeli: Plug M Gen3, Power Strip 4 Gen4,
  1PM, 2PM Gen4, 1PM Mini Gen4 [DOK]

### C. Otwarta platforma — „Tuya bez zamka"

**Espressif** — producent układów ESP32 i oficjalnego zestawu do Mattera `esp-matter`
na GitHubie, zbudowanego na otwartym SDK Mattera. Wi-Fi: ESP32, seria C i S.
Thread: seria H, C5, C6 [DOK]. Na tym budują producenci gotowego sprzętu.

**Program „Made for ESPHome"** — odznaka otwartego urządzenia [DOK]:
- tylko ESP32 i jego warianty (C3, C6, S2, S3) — ESP8266 się nie kwalifikuje
- **fabrycznie wgrany ESPHome**
- **konfiguracja publiczna**, np. na GitHubie
- użytkownik sam robi aktualizacje i może „przejąć kontrolę" nad konfiguracją
- lista: `devices.esphome.io/made-for-esphome/`

⚠️ **To nie jest Matter.** ESPHome obsługuje Matter tylko przez zewnętrzny komponent
`esphome-matter` — Wi-Fi i Thread, testowany na ESP32-C3, C5, C6, S3 i H2 — jeszcze
nie natywnie [DOK].

**Athom** — gotowe urządzenia z Tasmotą lub ESPHome. Ich gniazdo ścienne na ESP32-C3
z Tasmotą producent opisuje jako obsługujące Matter [DOK — strona producenta].

### Półka premium — Eve

Pomiar przez Matter od firmware 3.5.0, Thread [DOK]. Najczystsze podejście do standardu,
ale **[DEDUKCJA]** wyraźnie droższe.

---

## 4. Co ma sens w Twoim zestawie teraz

| Sytuacja | Wybór |
|---|---|
| RPi5 + Zigbee2MQTT, **brak Border Routera** | standard Zigbee: Sonoff SNZB, IKEA INSPELNING · Wi-Fi z API: Shelly |
| nauka Mattera z pomiarem **bez Threada** | Tapo P110M albo Shelly (Wi-Fi) |
| **dołożysz Border Router** | IKEA GRILLPLATS ~39 zł — najtańszy potwierdzony pomiar przez Matter |

---

## 5. Reguła zakupowa

Zamiast szukać „łaskawej marki", **sprawdzaj konkretne urządzenie**:

1. `matter-survey.org/cluster/0x0090` — czy ktoś widział pomiar przez Matter
2. `zigbee2mqtt.io` — model i czy w „exposes" jest `power` / `energy`
3. odznaka „Made for ESPHome" albo udokumentowane lokalne API
4. 🚩 w opisie „Tuya" albo „Smart Life" → zakładaj kłopoty z lokalnym pomiarem

---

## Źródła

- [matter-survey.org — klaster 0x0090](https://matter-survey.org/cluster/0x0090)
- [matter-smarthome.de — wtyczki raportujące energię przez Matter](https://matter-smarthome.de/en/practice/which-matter-smart-plugs-report-energy-data/)
- [Zigbee2MQTT — Tuya TS011F_plug_3 (wyłączone raportowanie)](https://www.zigbee2mqtt.io/devices/TS011F_plug_3.html)
- [Automated Home — ukryty tryb Zigbee w urządzeniach IKEA](https://automatedhome.com/the-hidden-trick-inside-ikeas-new-matter-over-thread-smart-devices/)
- [Zigbee2MQTT — dyskusja: GRILLPLATS w trybie Zigbee](https://github.com/Koenkk/zigbee2mqtt/discussions/31464) · [Zigbee2MQTT — IKEA E2435/E2491](https://www.zigbee2mqtt.io/devices/E2491.html) · [Hubitat — GRILLPLATS w trybie Zigbee](https://community.hubitat.com/t/info-ikea-grillplats-plug-works-in-a-zigbee-mode/163463)
- [IKEA — GRILLPLATS wtyczka](https://www.ikea.com/pl/pl/p/grillplats-wtyczka-smart-90624744/) · [IKEA — GRILLPLATS z pilotem](https://www.ikea.com/pl/pl/p/grillplats-wtyczka-z-pilotem-smart-30620051/) · [Pepper — GRILLPLATS 44,99 zł](https://www.pepper.pl/promocje/ikea-grillplats-wtyczka-matter-przycisk-1244273)
- [IKEA — DIRIGERA](https://www.ikea.com/pl/pl/p/dirigera-inteligentna-bramka-bialy-smart-10503406/) · [Matter Alpha — DIRIGERA jako hub Matter](https://www.matteralpha.com/explainer/five-updates-that-turned-the-ikea-dirigera-into-a-serious-matter-hub)
- [SmartThings Polska — GRILLPLATS z pomiarem energii](https://smartthingspolska.com/2026/08/16/ikea-grillplats-smart-plug-z-pomiar-energii-dla-smartthings-energy/)
- [Espressif — esp-matter na GitHubie](https://github.com/espressif/esp-matter) · [Espressif — ESP Matter Solution](https://www.espressif.com/en/solutions/device-connectivity/esp-matter-solution)
- [ESPHome — Made for ESPHome](https://esphome.io/guides/made_for_esphome/) · [lista urządzeń Made for ESPHome](https://devices.esphome.io/made-for-esphome/)
- [esphome-matter — zewnętrzny komponent](https://github.com/DavidvtWout/esphome-matter)
- [Athom — gniazdo ścienne EU z Tasmotą](https://www.athom.tech/blank-1/tasmota-eu-wall-socket)
