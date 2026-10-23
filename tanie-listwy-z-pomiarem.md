# Tańsze listwy od Shelly Power Strip 4 Gen4 — co realnie istnieje

Spisane 2026-09-14. Ceny z polskich sklepów (Eltrox, Allegro, Ceneo, Lidl, noussmart.pl)
w dniu sprawdzenia — **orientacyjne**. Źródła: dokumentacja Zigbee2MQTT, szablony Tasmoty,
strony producentów i sklepów. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [tanie-wtyczki-z-pomiarem.md](tanie-wtyczki-z-pomiarem.md) ·
[matter/07-pomiar-energii.md](matter/07-pomiar-energii.md) ·
[gdzie-kupowac-aliexpress-vs-alibaba.md](gdzie-kupowac-aliexpress-vs-alibaba.md)

---

## 1. Wniosek od razu

🔴 **Nie znalazłem listwy wyraźnie tańszej od Shelly, która mierzy każde gniazdo osobno.**

Tańsze listwy odcinają dokładnie pomiar: jedna mierzy tylko całość, druga nie mierzy wcale.

---

## 2. Porównanie

| | **Shelly Power Strip 4 Gen4** | **Nous A5T** | **Lidl Silvercrest HG06338** |
|---|---|---|---|
| Cena w PL | **~190–220 zł** | **~130–150 zł** | **~110–169 zł** (169 zł lidl.pl, promocja 109,85 zł) |
| Gniazda sterowane osobno | 4 | 3 (+ USB osobno) | 3 |
| **Pomiar** | ✅ **każde gniazdo osobno** | ⚠️ **jeden wspólny dla całej listwy** | ❌ **brak** |
| Łączność | Wi-Fi, Bluetooth, Zigbee, Matter | Wi-Fi | Zigbee |
| Lokalnie | API i MQTT; pomiar przez Matter potwierdzony | Tasmota — MQTT i HTTP, bez chmury | przez Zigbee2MQTT |
| Konfiguracja | prosta | trzeba umieć Tasmotę | parowanie w Zigbee2MQTT |

---

## 3. Nous A5T — najbliżej „taniej Shelly", ale z haczykiem

- **pomiar jednym układem (CSE7766) na całą listwę** — nie zobaczysz, które urządzenie
  ile bierze [DOK]
- pomiar wymaga kalibracji, żeby był dokładny [DOK]
- 3 gniazda na osobnych przekaźnikach + USB na czwartym [DOK]
- 15 A / 3450 W [DOK]
- **nie działa z aplikacją Nous, Tuya ani Smart Life — tylko Tasmota.** Sklep pisze wprost:
  „Użytkownik musi znać procedurę konfiguracji TASMOTA" [DOK]
- ⚠️ szablon Tasmoty ostrzega o problemach z przyciskami na samej listwie [DOK]
- **[DO SPRAWDZENIA]** typ wtyczki — sprzedawana w polskich sklepach, ale strony nie podają

🟢 **Dla Twojego huba to akurat zaleta:** Tasmota wysyła dane po MQTT prosto do Mosquitto.
Local-first z konstrukcji — nie ma czego odłączać od chmury.

---

## 4. Lidl HG06338 — najtańsza w Zigbee, ale bez pomiaru

- działa w Zigbee2MQTT natywnie, bez bramki Lidla [DOK]
- 3 gniazda sterowane osobno + ustawienie zachowania po zaniku prądu [DOK]
- ❌ **brak pomiaru mocy, energii, napięcia i prądu** [DOK]
- ⚠️ znana usterka: wadliwy kondensator potrafi sam włączać i wyłączać gniazda —
  da się naprawić [DOK — strona Zigbee2MQTT]

Pomiar u Lidla ma tylko **wtyczka** Silvercrest HG08673, nie listwa. Zgłaszano, że raportuje
zużycie co ok. 3 minuty i bywa niedokładna.

**[DEDUKCJA]** Pozostałych listew Zigbee z Lidla (zwykłej i zewnętrznej) nie zweryfikowałem.
Opisy wspominające „licznik zużycia" okazały się dotyczyć wtyczki.

---

## 5. Sprawdzone i odrzucone

| Listwa | Dlaczego odpada |
|---|---|
| Meross Matter 30 W GaN (~249 zł) | pomiar tylko w aplikacji Meross — [matter/07-pomiar-energii.md](matter/07-pomiar-energii.md) |
| BORUIDAPLS WP34A (~176 zł) | brak jakichkolwiek informacji |
| Tapo P304M | wersja UK/IE, pomiar nie przez Matter |
| tanie listwy Tuya Zigbee z AliExpress | loteria wersji; w Zigbee2MQTT nie znalazłem 4-gniazdowej listwy z pomiarem |
| Tuya TS011F_2_gang_power | tylko 2 gniazda, pomiar wspólny |

---

## 6. Co wybrać

| Chcesz | Bierz |
|---|---|
| **pomiar każdego gniazda osobno** | **Shelly Power Strip 4 Gen4** — tańszej z tą funkcją nie znalazłem |
| taniej, wystarczy suma dla całej listwy, Tasmota Cię nie przeraża | **Nous A5T** |
| najtaniej w Zigbee, pomiar niepotrzebny | **Lidl HG06338** |

Obejście „zwykła listwa + wtyczki z pomiarem" nie wychodzi taniej: cztery IKEA INSPELNING
i zwykła listwa to mniej więcej cena Shelly — [tanie-wtyczki-z-pomiarem.md](tanie-wtyczki-z-pomiarem.md).

---

## Źródła

- [Eltrox — Shelly Power Strip 4 Gen4 White](https://www.eltrox.pl/p/63975-shelly-power-strip-4-gen4-white) · [Ceneo — Shelly Power Strip 4 Gen4 Czarna](https://www.ceneo.pl/192058034)
- [Szablon Tasmoty — Nous A5T](https://templates.blakadder.com/nous_A5T.html) · [noussmart.pl — Nous A5T](https://noussmart.pl/products/przedluzacz-wifi-nous-a5t-tasmota) · [Ceneo — Nous A5T](https://www.ceneo.pl/147835890)
- [Zigbee2MQTT — Lidl HG06338](https://www.zigbee2mqtt.io/devices/HG06338.html) · [Lidl — listwa USB Zigbee](https://www.lidl.pl/p/silvercrest-listwa-zasilajaca-usb-zigbee-smart-home/p100358333) · [Pepper — promocja listwy Lidl](https://www.pepper.pl/promocje/listwa-zasilajaca-usb-zigbee-smart-home-lidl-silvercrest-1104541)
- [Zigbee2MQTT — Lidl HG08673 (wtyczka)](https://www.zigbee2mqtt.io/devices/HG08673.html) · [Homey Community — błędne zużycie wtyczki Silvercrest](https://community.homey.app/t/lidl-silvercrest-zwischenstecker-zeigt-falschen-energieverbrauch-an/148438)
- [Zigbee2MQTT — Tuya TS011F_2_gang_power](https://www.zigbee2mqtt.io/devices/TS011F_2_gang_power.html)
