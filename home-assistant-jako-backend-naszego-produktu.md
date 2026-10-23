# Home Assistant jako backend naszego produktu — czy wolno i czy warto

Spisane 2026-09-20. Źródła: licencje z repozytoriów GitHub (sprawdzone przez API), blog
Home Assistant o certyfikacji Matter (10.03.2025), strona członkostwa CSA.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [oplaty-za-standardy-bluetooth-matter-zigbee.md](oplaty-za-standardy-bluetooth-matter-zigbee.md) ·
[ocena-szczera-modelu.md](ocena-szczera-modelu.md) · [zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md)

---

## 1. Czy wolno — tak [DOK]

| Komponent | Licencja | Co z tego wynika |
|---|---|---|
| **Home Assistant Core** | **Apache 2.0** | 🟢 użycie komercyjne dozwolone, bez copyleft, bez opłat |
| **Home Assistant Frontend** | **Apache 2.0** | 🟢 jw. |
| **Supervisor**, **Operating System** | **Apache 2.0** | 🟢 jw. |
| **Matter SDK (connectedhomeip)** | **Apache 2.0** | 🟢 jw. |
| 🟡 **Zigbee2MQTT** | **GPL-3.0** | osobna sprawa — patrz niżej |

Apache 2.0 wymaga tylko zachowania noty o prawach autorskich i tekstu licencji.
**Nie wymaga otwierania naszego kodu.**

### 🟡 Zigbee2MQTT i GPL-3.0

Z2M jako **osobny proces gadający przez MQTT** nie „zaraża" naszego kodu — to komunikacja
międzyprocesowa, nie linkowanie. Ale jeśli **dystrybuujemy go na hubie**, trzeba dołączyć
tekst licencji i ofertę udostępnienia źródeł (łatwe — repo jest publiczne).
**[DO SPRAWDZENIA]** u prawnika przy pierwszej sprzedaży sprzętu.

## 2. 🔴 Czego nie wolno: nazwy i logo

Znak „Home Assistant" należy do **Open Home Foundation**. Produkt ma mieć **własną nazwę**.
**[DO SPRAWDZENIA]** czy wolno napisać „zbudowane na Home Assistant" — to pytanie o politykę
znaku towarowego, do sprawdzenia w ich trademark policy.

## 3. 🔴 Ich certyfikat Matter NIE przechodzi na nas [DOK]

To jest najważniejszy haczyk. Blog Home Assistant, wpis o certyfikacji:

> „We certified the server as a standalone component to allow any project to use it.
> **Each project that uses it, including Home Assistant, will have to go through a
> certification process** to acknowledge that it uses a certified component as a
> ‚User Interface Component'."

➡️ Użycie **Open Home Foundation Matter Server** to **skrót, nie darmowa przepustka**.
Nadal potrzebne: członkostwo CSA i **własna certyfikacja jako User Interface Component** —
tylko tańsza i szybsza, bo ciężka część (serwer) jest już zrobiona.

Ich wpisy w bazie CSA: `CSA25010SWC60193-M1` (Home Assistant) i `CSA24059SWC60176-M2`
(OHF Matter Server) — [Certified Products, Open Home Foundation](https://csa-iot.org/csa-iot_products/?p_company%5B%5D=1474).

## 4. Własny front na ich backendzie — realne, ale z kosztami

HA ma **WebSocket API i REST API** — da się na tym zbudować własny interfejs.

| Co dostajesz | Co Cię boli |
|---|---|
| setki gotowych integracji | **wydanie co miesiąc, z breaking changes** — musisz zarządzać wersjami na hubach klientów |
| działający Zigbee, Matter, Thread, Z-Wave | model encji jest **generyczny**, nie produktowy — mapowanie na ładny UX to praca |
| automatyzacje, historia, użytkownicy | **gdy HA zepsuje integrację, klient obwini Ciebie** |
| społeczność i dokumentacja | przejmujesz cudzy cykl wydawniczy |

**[DEDUKCJA] O „odpicowaniu ich frontu" — nie ma sensu.** Ich frontend to duża aplikacja
w TypeScript, która zmienia się co miesiąc; fork trzeba by wiecznie scalać. A my i tak
piszemy **apkę we Flutterze** — to ona jest naszym frontem. Ich panel WWW zostaje najwyżej
jako narzędzie serwisowe dla nas, nie dla klienta.

## 5. 🔴 Strategia „teraz ich, potem własne" — gdzie jest pułapka [DEDUKCJA]

Plan jest rozsądny, ale **tylko jeśli od pierwszego dnia trzymasz granicę**:

> **Własny model domenowy w naszej warstwie. HA jako wymienialny adapter.**
> Apka i chmura **nigdy nie widzą** `entity_id` ani encji Home Assistanta.

Jeśli oprzesz produkt bezpośrednio na encjach HA, migracja za trzy lata to nie refaktor,
tylko **przepisanie backendu i ponowne parowanie wszystkich urządzeń u klientów** — czyli
wizyta u każdego.

### Co realnie decyduje o możliwości podmiany

**Poświadczenia sieci nie siedzą w HA, tylko w usługach obok:**

| Usługa | Gdzie żyją klucze | Skutek |
|---|---|---|
| **Zigbee2MQTT** (osobny proces, MQTT) | w koordynatorze i Z2M | 🟢 podmiana HA **nie wymaga** ponownego parowania |
| **python-matter-server** (osobny proces, WebSocket) | w serwerze Matter | 🟢 jw. |
| gdyby to były wbudowane integracje HA (ZHA, Matter w Core) | w bazie HA | 🔴 podmiana = parowanie od zera |

➡️ **Rekomendacja architektoniczna:** trzymać **Z2M i matter-server jako samodzielne usługi**,
a nie integracje wewnątrz HA. Wtedy HA jest wymienialny, a sieć urządzeń zostaje.

## 6. Jedno zdanie

> **Licencyjnie wolno (Apache 2.0), nazwy używać nie wolno, ich certyfikat Matter i tak nie
> przechodzi na nas — a cała gra o „potem zbudujemy swoje" rozstrzyga się dziś: własny model
> domenowy w naszej warstwie i Zigbee2MQTT oraz matter-server jako osobne usługi, nie
> integracje w HA.**
