# Mail do Liliwise — zapytanie o próbki (wersja robocza PL)

Przygotowane 2026-09-18. Odbiorca: Daniel, `daniel@liliwise.com` (adres wywnioskowany ze
wzoru mailowego — **[DO SPRAWDZENIA]** przed wysyłką).
Podstawa merytoryczna: [zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md) ·
[jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md) (sekcja 5 — czego żądać od dostawcy)

⚠️ Wersja polska do akceptacji. Po zatwierdzeniu przepisujemy na angielski.

---

## Temat

> Zapytanie o próbki — inteligentne klamki TTLock, rynek polski

## Treść

> Dzień dobry, Danielu,
>
> nazywam się [imię i nazwisko] i reprezentuję firmę [nazwa] z Polski. Budujemy system
> inteligentnego domu, który działa lokalnie, w sieci klienta — własny hub i własna
> aplikacja mobilna. Szukamy dostawcy zamków i klamek na dłuższą współpracę, a zaczynamy
> od testów na próbkach.
>
> **Chciałbym zamówić 1–2 próbki klamki, którą polecacie do mieszkań.** Proszę o informację:
>
> 1. Jaki model polecacie i jaka jest cena próbki?
> 2. Jak wygląda zamówienie próbki — płatność, czas realizacji, wysyłka do Polski?
> 3. Czy w cenie jest wysyłka z cłem opłaconym (DDP), czy odprawę robimy po swojej stronie?
>
> Do tego kilka pytań technicznych, bo od nich zależy, czy zamek wpiszemy do naszego systemu:
>
> 4. **Jaki układ (moduł) TTLock jest w klamce?** Interesuje nas, czy jest to jeden z układów
>    obsługujących „third-party Bluetooth device integration": SN534-15-1, SN534-16-1,
>    SN503-18-1, SN8534-3-1, SN8534-1-1, SN8503-2-1. Jeśli nie — czy da się zamówić wersję
>    z takim układem albo z aktualizacją firmware'u?
> 5. Które modele macie w wersji **tylko Bluetooth**, a które **Bluetooth + Wi-Fi**?
>    Co polecacie do mieszkania w bloku?
> 6. Czy Wasze zamki współpracują z bramką **TTLock G6 (Matter)**? Czy dostarczacie tę bramkę?
> 7. Jakie są baterie (typ, deklarowana żywotność) i czy klamka ma klucz awaryjny?
> 8. Czy klamka pasuje do **europejskich drzwi**: wkładka euro-profil (PZ), rozstaw 72 lub 85 mm,
>    grubość drzwi 35–60 mm? Jakie rozstawy i grubości obsługujecie?
>
> Ponieważ sprzedajemy w Unii Europejskiej, potrzebujemy też potwierdzenia dokumentacji:
>
> 9. Czy do zamówienia dołączacie **deklarację zgodności CE i RoHS oraz raporty z badań**
>    (LVD, EMC, dyrektywa radiowa RED wraz z normami EN 18031)?
>
> Na koniec, na przyszłość: jakie są minimalne wielkości zamówienia i ceny przy zamówieniach
> hurtowych, i jak wygląda u Was gwarancja oraz wsparcie techniczne?
>
> Z góry dziękuję za odpowiedź.
>
> Pozdrawiam
> [imię i nazwisko]
> [firma] · [strona WWW] · [telefon]

---

## Dlaczego akurat te pytania

| Pytanie | Po co |
|---|---|
| 4 — układ TTLock | przesądza, czy hub albo ESP32 może być mostkiem Bluetooth **bez bramki** |
| 5 — BLE czy BLE+Wi-Fi | Wi-Fi w tych zamkach idzie do chmury TTLock, nie do naszej sieci |
| 6 — G6 Matter | jedyna oficjalna droga do sterowania lokalnego z huba przez Matter |
| 8 — dopasowanie do drzwi | klamki azjatyckie bywają pod inny standard wkładki i rozstawu |
| 9 — dokumenty | przy imporcie do UE to my odpowiadamy za zgodność — [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md) |

**[DEDUKCJA] Czego świadomie NIE pytamy na tym etapie:** o OEM i własne logo. Przy pierwszym
kontakcie to niepotrzebnie przesuwa rozmowę na sprzedaż projektową, a decyzja i tak brzmi:
klamek nie brandujemy.
