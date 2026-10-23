# 11 · Moduły dopuszkowe Shelly — który wybrać i czy pasuje do mieszkania

Spisane 2026-09-15. Dane techniczne: **Shelly Knowledge Base** (`kb.shelly.cloud`).
Ceny: Ceneo, Allegro, Eltrox, Energomag — **orientacyjne**. Legenda: [../KONWENCJA-NOTATEK.md](../KONWENCJA-NOTATEK.md).
Powiązane: [10-asortyment.md](10-asortyment.md) · [02-api-rpc-gen2.md](02-api-rpc-gen2.md) ·
[09-offline-i-chmura.md](09-offline-i-chmura.md) · [../tanie-wtyczki-z-pomiarem.md](../tanie-wtyczki-z-pomiarem.md)

---

## 1. Najpierw: to nie są wtyczki do gniazdek

To **moduły chowane w puszce w ścianie** — za włącznikiem światła, za gniazdkiem albo
w puszce przy lampie. Czy pasują, zależy od **puszki**, nie od gniazdka.

---

## 2. Jak czytać nazwy

| Fragment | Znaczy |
|---|---|
| **1** / **2** | jeden / dwa obwody |
| **PM** | pomiar mocy |
| **Mini** | mniejsza obudowa — łatwiej wchodzi za włącznik |
| **Gen3** / **Gen4** | generacja; Gen4 = Wi-Fi 6 + Bluetooth 5 + Zigbee + Matter |

---

## 3. Porównanie [DOK]

| Model | Obwody | Pomiar | Styki | Maks. obciążenie | Wymiary (mm) | Łączność | Cena PL |
|---|---|---|---|---|---|---|---|
| **Shelly 1 Gen4** | 1 | ❌ | bezpotencjałowe | **16 A** | 37×42×16 | Wi-Fi 6, BT, Zigbee, Matter | ~76 zł ¹ |
| **Shelly 1 Mini Gen4** | 1 | ❌ | bezpotencjałowe | 8 A / 2000 W | **29×34×16** | Wi-Fi 6, BT, Zigbee, Matter | od ~66 zł |
| **Shelly 1 Mini Gen3** | 1 | ❌ | bezpotencjałowe | 8 A / 2000 W | **29×34×16** | Wi-Fi, BT — Matter niewymieniony | ~57 zł ¹ |
| **Shelly 1PM Gen4** | 1 | ✅ | zwykłe | **16 A** | 37×42×16 | Wi-Fi 6, BT, Zigbee, Matter | od ~77 zł |
| **Shelly 2PM Gen3** | 2 lub roleta | ✅ | zwykłe | 10 A na kanał, 16 A razem | 37×42×16 | Wi-Fi, BT — Matter niewymieniony | ~76 zł ¹ |
| **Shelly 2PM Gen4** | 2 lub roleta | ✅ | zwykłe | 10 A na kanał, 16 A razem | 37×42×16 | Wi-Fi 6, BT, Zigbee, Matter | ~79–120 zł |
| ➕ **Shelly 1PM Mini Gen4** | 1 | ✅ | zwykłe | 8 A | **29×34×16** | Wi-Fi 6, BT, Zigbee, Matter | ~84 zł |

¹ cena z jednego źródła. ➕ spoza pierwotnej listy — jedyny, który łączy **pomiar i mały rozmiar**.

„Matter niewymieniony" = producent nie podaje go w specyfikacji Gen3.

---

## 4. Styki bezpotencjałowe — co to i po co

**Shelly 1 i 1 Mini** mają przekaźnik jak zwykły przełącznik, **bez własnego napięcia na wyjściu**.
Dzięki temu sterują nie tylko lampą, ale też **bramą, wejściem sterującym pieca albo urządzeniem
niskonapięciowym**. Nie mierzą prądu.

**[DEDUKCJA]** Przy zwykłej lampie 230 V trzeba doprowadzić fazę do styku modułu — to kwestia
schematu, który elektryk zrobi przy montażu.

**Modele PM** przełączają zasilanie wprost i **mierzą**: napięcie, prąd, moc.

---

## 5. 🔴 Czy będą pasowały — dwie rzeczy do sprawdzenia w puszce

### A. Przewód neutralny N — wymagany przez WSZYSTKIE powyższe modele [DOK]

- w starszych budynkach często go brak w puszce pod włącznikiem; w nowszych zwykle jest,
  ale **bez gwarancji** **[DEDUKCJA]**
- blok z ok. 2011 roku → szanse dobre, **i tak trzeba sprawdzić**
- **brak N** → moduł montuje się przy lampie albo bierze wersję bez N — modele z literą **L**,
  np. Shelly 1L [DOK — poradnik wiNET]

### B. Głębokość puszki

- standard to puszka **Ø60 mm**: płytka **ok. 40 mm** albo głęboka **ok. 60 mm** [DOK]
- za włącznikiem w płytkiej puszce jest ciasno — do modułów elektronicznych poleca się głębszą [DOK]
- **[DEDUKCJA z wymiarów]** Mini (29×34×16) wchodzi łatwiej niż zwykły (37×42×16)
- zwykły 1PM i 2PM → głęboka puszka albo puszka przy lampie

---

## 6. Gen3 czy Gen4

- **Gen4:** Wi-Fi 6, Bluetooth 5, Zigbee, Matter [DOK]
- **Gen3** (1 Mini Gen3, 2PM Gen3): w specyfikacji tylko Wi-Fi i Bluetooth [DOK]
- oba mają **lokalne API Shelly** — [02-api-rpc-gen2.md](02-api-rpc-gen2.md)
- **[DEDUKCJA]** różnica w cenie jest niewielka — jeśli myślisz o Matterze, bierz Gen4

### Zigbee w Gen4 — ostrożnie

- tryb Zigbee włącza się przyciskiem reset — wg relacji użytkowników pięć zdecydowanych naciśnięć
- zgłoszenie: **2PM Gen4 w Zigbee2MQTT działał tylko w trybie rolety**, bez przełączenia na dwa obwody
- **[DO SPRAWDZENIA]** czy w trybie Zigbee dalej działa Wi-Fi i lokalne API
- 🟢 **dla własnego huba bezpieczniej: Wi-Fi + lokalne API**

---

## 7. Który do czego

| Chcesz | Bierz |
|---|---|
| zwykłe światło za włącznikiem, bez pomiaru | **Shelly 1 Mini Gen4** |
| światło lub małe urządzenie **z pomiarem**, mała puszka | **Shelly 1PM Mini Gen4** (8 A) |
| obwód do 16 A **z pomiarem** | **Shelly 1PM Gen4** — potrzebna głęboka puszka |
| włącznik dwuklawiszowy albo **roleta** | **Shelly 2PM Gen4** |
| brama, piec, niskie napięcie | **Shelly 1 Gen4** (16 A) albo **1 Mini Gen4** (8 A) |
| najtaniej, Matter niepotrzebny | **Shelly 1 Mini Gen3** |

---

## 8. ⚠️ Bezpieczeństwo

- praca przy 230 V: **zawsze przy wyłączonym bezpieczniku**, najlepiej przez elektryka
- nie przekraczać obciążenia — Mini ma **8 A**

---

## Źródła

- Shelly KB: [1 Gen4](https://kb.shelly.cloud/knowledge-base/shelly-1-gen4) · [1 Mini Gen4](https://kb.shelly.cloud/knowledge-base/shelly-1-mini-gen4) · [1 Mini Gen3](https://kb.shelly.cloud/knowledge-base/shelly-1-mini-gen3) · [1PM Gen4](https://kb.shelly.cloud/knowledge-base/shelly-1pm-gen4) · [2PM Gen3](https://kb.shelly.cloud/knowledge-base/shelly-2pm-gen3) · [2PM Gen4](https://kb.shelly.cloud/knowledge-base/shelly-2pm-gen4) · [1PM Mini Gen4](https://kb.shelly.cloud/knowledge-base/shelly-1pm-mini-gen4)
- Ceny: [Ceneo — 1 Mini Gen4](https://www.ceneo.pl/190080028) · [Ceneo — 1PM Gen4](https://www.ceneo.pl/190080214) · [Ceneo — 2PM Gen4](https://www.ceneo.pl/190030721) · [Eltrox — 2PM Gen4](https://www.eltrox.pl/p/62059-shelly-2pm-gen4) · [Eltrox — 1PM Mini Gen4](https://www.eltrox.pl/p/61992-shelly-1pm-mini-gen4) · [msalamon — 1 Mini Gen3](https://sklep.msalamon.pl/produkt/sterownik-shelly-1-mini-gen3/) · [Allegro — Shelly 1 Gen4](https://allegro.pl/listing?string=shelly+1+gen4)
- Neutral: [wiNET — schemat Shelly 1](https://winet.com.pl/shelly-1-schemat-jak-poprawnie-podlaczyc-modul-w-instalacji-domowej/) · [ArturHome — instalacja bez neutrala](https://forum.arturhome.pl/t/instalacja-puszkowa-bez-neutrala-wlaczniki-schodowe-i-shelly-1l-lub-inny-switch/5945)
- Puszki: [AVT — puszka 60 mm, głębokość 40 mm](https://sklep.avt.pl/pl/products/puszka-podtynkowa-60mm-glebokosc-40mm-192175.html) · [Simet — puszka 60 mm głęboka](https://www.simet.com.pl/en/products/flush-mounted-junction-box-60-mm-deep-2/)
- Zigbee w Gen4: [HA Community — tryb Zigbee](https://community.home-assistant.io/t/shelly-gen4-zigbee-mode/888242) · [HA Community — 2PM Gen4 w Zigbee2MQTT](https://community.home-assistant.io/t/zigbee2mqtt-unable-to-set-shelly-gen4-2mp-to-switch-mode/937114)
