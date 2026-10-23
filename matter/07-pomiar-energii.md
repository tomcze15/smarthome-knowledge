# 07 · Pomiar energii przez Matter — dlaczego „Matter" nie znaczy „widzę waty"

Spisane 2026-09-14. Źródła: matter-smarthome.de, matter-survey.org, matterdevices.io,
blog Neila Turnera (test wtyczek Meross), Shelly, wątek TP-Link o Tapo P304M.
Legenda pewności: [../KONWENCJA-NOTATEK.md](../KONWENCJA-NOTATEK.md).
Powiązane: [01-podstawy.md](01-podstawy.md) · [03-commissioning.md](03-commissioning.md) ·
[../wyjasnienie-polnoc-poludnie.md](../wyjasnienie-polnoc-poludnie.md)

---

## 1. Skąd rozczarowanie

Listwa **Meross Matter 30 W GaN** ma pomiar mocy, a recenzja mówi:
„funkcja pomiaru mocy nie jest dostępna; jest dostępna tylko w aplikacji Meross".

Oczekiwanie brzmiało: skoro Matter, to pomiar powinien być dostępny normalnie.
**Rzeczywistość: pomiar energii to późny dodatek do standardu i wymaga spełnienia
trzech warunków naraz.**

To ten sam problem, który wyszedł przy Sonoff BASIC-1GSP —
[../sonoff/07-offline-i-chmura.md](../sonoff/07-offline-i-chmura.md).

---

## 2. Pomiar energii doszedł dopiero w Matter 1.3 [DOK]

| Wersja | Pomiar energii |
|---|---|
| **Matter 1.0 – 1.2** | ❌ brak standardowego sposobu raportowania mocy i energii |
| **Matter 1.3** | ✅ nowe klastry + typ urządzenia **Electrical Sensor** |
| Matter 1.4 | typy urządzeń do zarządzania energią (EVSE, pompa ciepła, PV, magazyn) — [../strategia-protokoly.md](../strategia-protokoly.md) |

Dwa klastry z Matter 1.3:

| Klaster | Kod | Co podaje |
|---|---|---|
| **Electrical Power Measurement** | `0x0090` | moc chwilowa, napięcie, prąd — „ile bierze teraz" |
| **Electrical Energy Measurement** | `0x0091` | energia narastająco w kWh — „ile zużyło" |

🎯 **Urządzenie certyfikowane na starszej wersji Mattera fizycznie nie ma jak podać watów
przez Matter** — nawet jeśli ma miernik w środku.

---

## 3. Trzy warunki — wszystkie naraz [DOK]

1. **Urządzenie** — ma miernik i firmware z Matter 1.3+, które **wystawia** klastry pomiarowe
2. **Kontroler** — ekosystem, w którym urządzenie jest sparowane, też obsługuje 1.3+
3. **Aplikacja** — pokazuje te dane w interfejsie

Brak dowolnego z trzech = przez Matter masz tylko włącz/wyłącz.

⚠️ Często potrzebna jest **aktualizacja firmware'u w aplikacji producenta** — Matter tego
za niego nie zrobi. Przykład Meross MSS315 (wtyczka): przed aktualizacją przez Matter
tylko włączanie i wyłączanie; po aktualizacji w Home Assistancie pojawiły się moc,
napięcie i prąd. Do tego trzeba było ponownie „przesłuchać" urządzenie w Home Assistancie.

---

## 4. Które aplikacje pokazują waty z Mattera [DOK]

| Pokazują | Brak na liście |
|---|---|
| Home Assistant · Homey Pro · SmartThings · Aqara Home · Ledvance Smart+ · aplikacje Tuya | **Apple Home · Google Home · Alexa** |

Przykład z innej marki: wtyczka **Kasa KP125M** dodana przez Matter do HomeKit albo Alexy
daje włączanie i harmonogramy — waty widać tylko w aplikacji Kasa.

🟢 **Dla Twojego huba warunek 3 zależy wyłącznie od Ciebie** — sam czytasz klastry
i sam decydujesz, co pokazać. Sprawdzać trzeba tylko warunek 1: czy urządzenie je wystawia.

---

## 5. Jak sprawdzić urządzenie PRZED zakupem

**1 · matter-survey.org** — społecznościowa baza możliwości urządzeń Matter, dane od
użytkowników aplikacji Matter Binding Helper.

```
https://matter-survey.org/cluster/0x0090
```

W dniu sprawdzenia: **68 urządzeń** z klastrem pomiaru mocy.
⚠️ **Obecność na liście = ktoś to realnie widział. Brak na liście nie dowodzi braku** —
może po prostu nikt tego modelu nie zgłosił.

**2 · matterdevices.io** — przy urządzeniu oznaczenie „Energy Monitor".

**3 · W opisie szukaj „przez Matter", nie samego „pomiar energii".** „Energy monitoring"
w tytule aukcji zwykle znaczy: w aplikacji producenta.

**4 · Po zakupie — chip-tool.** Odczyt listy klastrów, które urządzenie wystawia
(składnia wg schematu z przewodnika chip-tool, [03-commissioning.md](03-commissioning.md)):

```bash
chip-tool descriptor read server-list <node_id> <endpoint>
```

Szukasz **144** (`0x0090`) i **145** (`0x0091`) — chip-tool podaje identyfikatory dziesiętnie.
**[DEDUKCJA]** Listwa zwykle ma osobny endpoint na każde gniazdo — sprawdzaj każdy.

---

## 6. Konkretne listwy — stan 2026-09-14

| Listwa | Pomiar w aplikacji producenta | **Pomiar przez Matter** | Uwagi |
|---|---|---|---|
| **Meross Matter 30 W GaN** | ✅ | ❌ brak w matter-survey; matterdevices.io bez „Energy Monitor"; recenzja to potwierdza | **[DEDUKCJA]** może dojść aktualizacją, jak w ich wtyczce MSS315 — **nie kupuj na obietnicę** |
| **BORUIDAPLS WP34A** (wersja Matter) | ❓ | ❓ **brak jakichkolwiek informacji w sieci** | aukcja ma trzy wersje: Wi-Fi, Zigbee (Tuya), Matter — „Energy Monitoring" widnieje tylko przy Wi-Fi |
| **Tapo P304M** | ✅ na każde gniazdo | ❌ wg wątku na forum TP-Link: brak pomiaru przez huby Matter | wersja UK/IE |
| **Shelly Power Strip 4 Gen4** | ✅ na każde gniazdo | ✅ **widoczny w matter-survey (`0x0090`)** | gniazda EU z uziemieniem · sugerowana cena **59,90 €** |

⚠️ **„WP34A" to nazwa modelu, nie 34 ampery.** Domowe gniazda 230 V są na 16 A.

### Dlaczego Shelly wygrywa w tym zestawieniu

- pomiar **na każdym gnieździe osobno**
- pomiar dostępny **przez Matter** — potwierdzony w bazie
- **i niezależnie od Mattera**: wbudowany panel WWW, **REST API i MQTT**, skrypty
  na urządzeniu — [../shelly/02-api-rpc-gen2.md](../shelly/02-api-rpc-gen2.md)
- Wi-Fi + Bluetooth + **Zigbee** + Matter; działa też jako wzmacniacz Wi-Fi i Zigbee
  **[DO SPRAWDZENIA]** czy Zigbee2MQTT obsługuje ten konkretny model

➡️ **Tańsze alternatywy** — pojedyncze wtyczki Zigbee i Matter od ~40 zł: [../tanie-wtyczki-z-pomiarem.md](../tanie-wtyczki-z-pomiarem.md) · tańsze listwy: [../tanie-listwy-z-pomiarem.md](../tanie-listwy-z-pomiarem.md)

---

## 7. Wniosek dla projektu

1. **„Matter" na pudełku = sterowanie. Pomiar trzeba sprawdzić osobno.**
2. **Dla huba natywne API jest lepsze niż Matter** — nawet gdy Matter pomiar ma, lokalne API
   zwykle daje więcej. Model „północ/południe": [../wyjasnienie-polnoc-poludnie.md](../wyjasnienie-polnoc-poludnie.md)
3. **Dopisać do testu akceptacyjnego** ([../strategia-doboru-sprzetu.md](../strategia-doboru-sprzetu.md)):
   > Czy pomiar mocy działa **w moim hubie, lokalnie, bez aplikacji producenta**?

---

## Źródła

- [matter-smarthome.de — które wtyczki Matter raportują energię](https://matter-smarthome.de/en/practice/which-matter-smart-plugs-report-energy-data/)
- [matter-smarthome.de — Matter 1.3, nowe funkcje](https://matter-smarthome.de/en/development/matter-1-3-a-deep-dive-into-the-new-features/)
- [matter-survey.org — klaster Electrical Power Measurement 0x0090](https://matter-survey.org/cluster/0x0090)
- [matterdevices.io — listwy zasilające Matter](https://matterdevices.io/device-type/power-strip/)
- [Neil Turner — pomiar energii przez Matter (Meross)](https://neilturner.me.uk/2025/09/17/energy-monitoring-over-matter/)
- [Fixory — wtyczki Matter z pomiarem energii](https://fixoryhq.com/en/best-matter-smart-plugs-energy-monitoring/)
- [TP-Link Community — Tapo P304M i pomiar przez Matter](https://community.tp-link.com/en/smart-home/forum/topic/698174)
- [Shelly — Power Strip 4 Gen4](https://www.shelly.com/products/shelly-power-strip-4-gen4)
- [Basic Tutorials — test Shelly Power Strip 4 Gen4](https://basic-tutorials.com/reviews/gadget-reviews/shelly-power-strip-4-gen-4/)
