# Gdzie kupować sprzęt — AliExpress, Alibaba czy polski sklep

Spisane 2026-09-14. Źródła: Komisja Europejska (Taxation and Customs Union), Avalara,
dokumentacja Zigbee2MQTT i zgłoszenia na GitHubie. Legenda pewności:
[KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).

---

## 1. Najkrócej

- **AliExpress** — sklep dla ludzi. Kupujesz jedną sztukę jak w każdym sklepie
- **Alibaba** — giełda hurtowa dla firm. Kupujesz partię, negocjujesz z fabryką
- **Do testów: AliExpress albo polski sklep.** Alibaba dopiero wtedy, gdy wiesz, jaki model
  bierzesz w ilościach

Obie należą do tej samej grupy, ale to **dwa różne światy**.

---

## 2. Różnice

| | **AliExpress** | **Alibaba** |
|---|---|---|
| Dla kogo | konsument (B2C) | firmy (B2B) |
| Ile sztuk | od jednej | zwykle **minimalne zamówienie (MOQ)**; próbki na zapytanie |
| Cena | stała, klikasz „kup" | negocjowana, zapytanie ofertowe |
| Kto sprzedaje | sklepy, często pośrednicy | fabryki i firmy handlowe |
| Ochrona kupującego | spór i zwrot pieniędzy | **Trade Assurance** — tylko gdy płacisz przez platformę |
| Wysyłka | w cenie albo tania, do domu lub paczkomatu | do ustalenia: kurier ekspresowy albo fracht |
| VAT i cło | VAT pobierany przy zakupie; od 07.2026 dochodzi cło €3 (sekcja 3) | odprawa po Twojej stronie: cło wg taryfy + VAT importowy, czasem agencja celna |
| Zwrot towaru | do Chin zwykle nieopłacalny | praktycznie nie istnieje |
| Do czego | **testy, pojedyncze sztuki** | partie, private label, ODM |

---

## 3. Cła od lipca 2026 [DOK]

- **od 1 lipca 2026** znika zwolnienie z cła dla paczek do **€150** spoza UE
- przejściowo: **€3 cła za każdą kategorię towaru** (pozycję taryfy) w paczce —
  **nie za paczkę**
- VAT jak dotąd, osobno
- stawka przejściowa obowiązuje **do 1 lipca 2028** — potem zwykłe cła wg taryfy

**[DEDUKCJA] Co to znaczy w praktyce:**
- trzy takie same czujniki w jednej paczce = jedna kategoria = raz €3
- czujnik, żarówka i gniazdko w jednej paczce = najpewniej trzy kategorie = trzy razy €3
- **drobne, różnorodne zamówienia podrożały** — tanie urządzenie za 30 zł niesie teraz
  zauważalny narzut

---

## 4. 🔴 Pułapka Zigbee z AliExpress: ta sama aukcja, inne urządzenie

Dotyczy głównie sprzętu opartego na **Tuya**. [DOK]

- wiele urządzeń ma **ten sam model** (np. `TS011F`), ale **inny „manufacturer name"**
  (np. `_TZ3000_xxxxxxxx`) — i działa inaczej
- Zigbee2MQTT rozpoznaje urządzenie po **parze: model + manufacturer name**, a nie po zdjęciu
- sprzedawca może przysłać **inną wersję** niż ta, którą ktoś opisał jako działającą.
  Zdjęcie w aukcji to samo

**Przed zakupem:**
- sprawdź model na `zigbee2mqtt.io` (lista obsługiwanych urządzeń)
- znajdziesz konkretny kod `_TZ...` — wyszukaj go w zgłoszeniach Zigbee2MQTT na GitHubie

**Po dostawie:**
- w panelu Zigbee2MQTT sprawdź manufacturer name
- „unsupported"? **Otwórz spór od razu**, albo bądź gotów dopisać własny konwerter

🟢 **Najprościej omijać problem:** urządzenia grające **standardem Zigbee 3.0** zamiast
Tuya — np. Sonoff SNZB. Dlaczego: [zigbee-standard-vs-lamanie.md](zigbee-standard-vs-lamanie.md).
I kupuj z **oficjalnych sklepów marek** na AliExpress, nie od przypadkowych sprzedawców.

➡️ **Kogo wybierać zamiast Tuya:** [przyjazni-producenci-zamiast-tuya.md](przyjazni-producenci-zamiast-tuya.md)

---

## 5. 🔴 Pułapki Matter

**„Matter" w tytule aukcji to nie certyfikat.** Sprawdź VID i PID w publicznym rejestrze
CSA: `https://webui.dcl.csa-iot.org/` — jak: [matter/03-commissioning.md](matter/03-commissioning.md).

**Matter over Thread wymaga Thread Border Routera.**
**[DEDUKCJA] Twój zestaw:** na RPi5 masz ZBDongle-E pracujący jako koordynator Zigbee —
Border Routera nie masz. **Urządzenie Matter over Thread się nie sparuje**, dopóki go
nie dołożysz.

➡️ **Do nauki Mattera kupuj urządzenia Matter over Wi-Fi.** Nie wymagają nic dodatkowego.

⚠️ Część ofert „Matter" to **hub**, który wystawia zwykłe urządzenia Zigbee jako Matter
(most). Samo urządzenie Mattera wtedy nie zna — czytaj opis, co dokładnie mówi po Matterze.

---

## 6. Trzecia opcja: polski sklep

- szybka dostawa, gwarancja w kraju
- **14 dni na zwrot bez podania przyczyny** przy zakupie online jako osoba prywatna
  (prawo konsumenckie UE)
- przy zakupie na firmę zasady zwrotów bywają inne

🎯 **Przy testach to często najlepszy wybór.** Ryzyko „nie działa z Zigbee2MQTT" jest
realne, a łatwy zwrot jest wart więcej niż różnica w cenie.

---

## 7. Plan zakupowy

| Etap | Gdzie | Na co uważać |
|---|---|---|
| **Testy teraz** | AliExpress (oficjalne sklepy marek) albo polski sklep | pojedyncze sztuki; łącz ten sam typ w jednym zamówieniu |
| Zigbee | — | przed zakupem `zigbee2mqtt.io`, po dostawie manufacturer name |
| Matter | — | certyfikat w DCL; **Wi-Fi, nie Thread** |
| **Wdrożenia, partie** | Alibaba | najpierw próbki; płatność przez Trade Assurance; odprawa i obowiązki CE po Twojej stronie — [strategia-modbus-rs485.md](strategia-modbus-rs485.md) |

---

## Źródła

- [Komisja Europejska — koniec zwolnienia z cła do €150 od 2026](https://taxation-customs.ec.europa.eu/news/e-commerce-150-eur-customs-duty-exemption-threshold-be-removed-2026-2025-11-13_en)
- [Komisja Europejska — stała opłata przejściowa do 1 lipca 2028](https://taxation-customs.ec.europa.eu/news/guidance-and-legal-text-temporary-flat-fee-low-value-imports-which-will-apply-until-1-july-2028-2026-06-08_en)
- [Avalara — koniec zwolnienia z cła, lipiec 2026](https://www.avalara.com/blog/en/europe/2025/11/eu-end-150-customs-duty-exemption-2026.html)
- [Zigbee2MQTT — obsługa nowych urządzeń Tuya (fingerprinty)](https://www.zigbee2mqtt.io/advanced/support-new-devices/02_support_new_tuya_devices.html)
- [Zigbee2MQTT — przykład nieobsługiwanego wariantu TS011F](https://github.com/Koenkk/zigbee2mqtt/discussions/25856)
