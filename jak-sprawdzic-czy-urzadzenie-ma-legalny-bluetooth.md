# Jak sprawdzić, czy urządzenie ma legalnie Bluetooth — procedura + przykład LOQED

Spisane 2026-09-20. Sprawdzone na żywo w bazach Bluetooth SIG.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [oplaty-za-standardy-bluetooth-matter-zigbee.md](oplaty-za-standardy-bluetooth-matter-zigbee.md) ·
[mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md](mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md) ·
[jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md)

---

## 1. Dwie bazy, dwa różne pytania

| Baza | Odpowiada na pytanie | Link |
|---|---|---|
| **Member directory** | czy firma jest **członkiem** SIG | [bluetooth.com — member directory](https://www.bluetooth.com/develop-with-bluetooth/join/member-directory/) |
| 🔴 **Qualified Product database** | czy **konkretny produkt** przeszedł kwalifikację | [qualification.bluetooth.com/Listings/Search](https://qualification.bluetooth.com/Listings/Search) |

**Rozstrzyga ta druga.** Członkostwo to tylko warunek wstępny — samo w sobie nie dowodzi,
że dany produkt jest zgłoszony. Baza produktów mówi wprost [DOK]:

> „Only Products that have completed the Bluetooth Qualification Process will appear in the
> Qualified Product database search results on the specified Product Publication Date"

Szuka się po **nazwie firmy, nazwie produktu albo numerze modelu**. Logowanie niepotrzebne.
(Launch Studio pod `launchstudio.bluetooth.com` **wymaga logowania** — to panel dla członków,
nie publiczna wyszukiwarka.)

## 2. 🔴 Najpierw ustal, CZYJA jest marka na urządzeniu

To najczęstszy błąd. Sprzedawca ≠ marka produktu.

Przykład: **Shelly sprzedaje „LOQED Touch Inteligentny Zamek" za 1 130 zł.** Marką na produkcie
jest **LOQED**, nie Shelly. Więc:

- sprawdzamy **LOQED**, nie Shelly
- **Shelly jest tu odsprzedawcą** i nie potrzebuje niczego [DOK — SIG]:
  *„If you are a retailer or supplier simply selling or distributing another company's Bluetooth
  product and not branding or representing the product as your own, you do not need to qualify
  or declare the product."*
- to, że **Shelly Europe Ltd. jest Adopterem**, wynika z ich **własnej** linii Bluetooth
  (Shelly BLU) — z tym zamkiem nie ma związku

## 3. Wynik sprawdzenia [DOK — odczyt 20.09.2026]

| Baza | Wynik |
|---|---|
| Member directory: `Loqed` | **Loqed B.V. — Adopter** ✅ |
| Member directory: `shelly` | **Shelly Europe Ltd. — Adopter** ✅ (nieistotne dla tego produktu) |
| Qualified Product database: `Loqed` | **LOQED Touch Smart Lock**, model **2.0**, firma **Loqed B.V.** ✅ |

Karta produktu: [qualification.bluetooth.com/ListingDetails/195261](https://qualification.bluetooth.com/ListingDetails/195261)

➡️ **Zamek ma legalnie Bluetooth.** Firma jest członkiem, produkt jest w bazie kwalifikowanych.

## 4. Procedura do wielokrotnego użytku

1. Popatrz, **czyja marka jest na obudowie** — nie kto sprzedaje
2. [Member directory](https://www.bluetooth.com/develop-with-bluetooth/join/member-directory/) →
   wpisz nazwę tej firmy. Nie ma jej → 🔴 nie mogą legalnie brandować Bluetootha
3. [Qualified Product database](https://qualification.bluetooth.com/Listings/Search) → wpisz
   nazwę firmy, produktu albo **numer modelu**. Nie ma → 🔴 produkt nie przeszedł kwalifikacji
4. Sprawdź, czy **numer modelu w bazie zgadza się z tym na tabliczce** urządzenia
5. Przy chińskim dostawcy: jeśli nie ma go w żadnej z baz, a chce sprzedawać pod swoją marką —
   **to jest pytanie do niego**, a nie rzecz do przemilczenia

## 5. Po co nam to [DEDUKCJA]

- przy **cudzej marce** — dowód, że kupujemy legalny towar, i że jako odsprzedawca nic nie płacimy
- przy rozważaniu **własnej marki** — sprawdzamy, czy fabryka w ogóle jest członkiem SIG;
  jeśli nie jest, biała etykieta oznacza pełne **12 000 $** po naszej stronie —
  [klamki-30-sztuk-rachunek-wlasna-marka.md](klamki-30-sztuk-rachunek-wlasna-marka.md)
- przy reklamacji i nadzorze rynku — produkt spoza bazy „may be impounded by customs' authorities" [DOK — SIG]

## 6. Jedno zdanie

> **Członkostwo firmy sprawdzasz w Member directory, a sam produkt w Qualified Product database —
> i zawsze pod marką, która jest na obudowie, a nie pod nazwą sklepu, który go sprzedaje.**
