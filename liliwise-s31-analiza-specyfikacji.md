# Liliwise S31 — czy to naprawdę klamka „na Europę"

Spisane 2026-09-19 ze specyfikacji producenta przysłanej przez usera (zrzut strony produktu S31).
Weryfikacja rozmiarów: oferty polskich sklepów z okuciami (CentrumOkuc, okucia24/KFV, MIBAR).
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [klamki-zamek-wpuszczany-jaki-rozmiar-do-polski.md](klamki-zamek-wpuszczany-jaki-rozmiar-do-polski.md) ·
[zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md) ·
[mail-liliwise-probki.md](mail-liliwise-probki.md)

---

## 1. Krótka odpowiedź

**Częściowo tak — i to lepiej niż poprzedni zestaw.** Z sześciu wymienionych zamków
**trzy są europejskie**, w tym dwa w rozmiarze, który w Polsce jest absolutnym standardem
w drzwiach PCV i aluminiowych. Ale to **klamka wąskoprofilowa** — do drzwi z PCV, aluminium
i przesuwnych, a nie do zwykłych drewnianych drzwi wewnętrznych.

## 2. „Suitable for Mortise" — rozkodowane [DEDUKCJA + DOK na rozmiary PL]

| Oznaczenie | Dorn | Rozstaw | Ocena |
|---|---|---|---|
| **3592** | 35 | **92** | 🟢 **europejski — standard PL dla drzwi PCV/alu** |
| **4592** (Double Hook) | 45 | **92** | 🟢 europejski, z hakami — drzwi przesuwne i alu |
| **6072** | 60 | **72** | 🟢 europejski (DIN), ale dorn 60 — nie 50 |
| 6085 | 60 | 85 | 🔴 azjatycki |
| 3585 | 35 | 85 | 🔴 azjatycki |
| 2885 (Double Hook) | 28 | 85 | 🔴 azjatycki |

**92 mm to w Polsce standard drzwi PCV i aluminiowych** [DOK] — KFV, Karo/Romb i inni sprzedają
zamki `92/35`, `92/45` z trzpieniem **8 mm** i czołem 16 lub 24 mm. Czyli 3592 i 4592 trafiają
dokładnie w to, co u nas wisi w drzwiach balkonowych i wejściowych z profili.

🔴 **Czego nadal nie ma: dorn 50 przy rozstawie 72** — czyli `5072`, najpopularniejszy rozmiar
polskich **drewnianych** drzwi wewnętrznych. Jest tylko 6072 (dorn 60).

## 3. Co w tej specyfikacji wygląda dobrze

| Pozycja | Dlaczego dobrze |
|---|---|
| **TTlock BLE / TTlock WIFI/BLE** na liście protokołów | są obie wersje, o które pytamy — [zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md) |
| **Password: 150** | dokładnie tyle kodów własnych mieści pamięć zamka TTLock — spec jest spójny, nie zmyślony |
| **Klucz awaryjny** („Key" wśród sposobów otwarcia) + USB awaryjne zasilanie | jest wyjście, gdy padną baterie |
| **−25 °C … 60 °C** | polska zima nie jest problemem |
| **Grubość drzwi 35–65 mm** | łapie drzwi wewnętrzne (ok. 40) i większość wejściowych |
| **304 stal nierdzewna**, 250 tys. cykli, 200 h mgły solnej | deklaracje na poziomie porządnego produktu **[DO SPRAWDZENIA]** czy są raporty |

## 4. 🔴 Czego brakuje i co zgrzyta

| Problem | Dlaczego to ważne |
|---|---|
| **„Certification: CE, FFC, ROSH"** | literówki zamiast FCC i RoHS. **FCC to certyfikat amerykański — w UE nic nie znaczy.** CE to nie certyfikat, tylko deklaracja producenta |
| **Zero słowa o RED i EN 18031** | urządzenie radiowe wprowadzane do UE po 1.08.2025 musi to spełniać — [mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md](mini-pc-z-alibaby-sprzedaz-pod-wlasna-marka.md) |
| **„Waterproof / Fireproof: Yes"** bez klasy IP | „tak" bez IP44/IP55 jest niemierzalne i nieweryfikowalne |
| **Brak modelu układu TTLock** | to jest **pytanie nr 1** — decyduje, czy hub może gadać z zamkiem po BLE bez bramki |
| **4 × AAA, „12 miesięcy"** | AAA to mało jak na czytnik linii papilarnych; 12 miesięcy dotyczy prawie na pewno wersji **samo BLE**, nie BLE+Wi-Fi **[DO SPRAWDZENIA]** |
| **Brak wagi** | potrzebna do policzenia frachtu — [klamki-30-sztuk-rachunek-wlasna-marka.md](klamki-30-sztuk-rachunek-wlasna-marka.md) |
| **Brak: wersja Bluetooth, trzpień, odwracalność lewy/prawy, czy zamek jest w cenie** | bez tego nie da się zamówić konkretu |
| **Tuya BLE / Wi-Fi / Zigbee na tej samej liście** | ten sam korpus, różne firmware. **Trzeba wskazać wariant przy zamówieniu**, inaczej przyjdzie Tuya |

## 5. 🟡 Co oznacza „narrow aluminium or sliding doors"

Korpus klamki **280 × 38 × 25 mm** — wąski. To projekt pod **wąskie profile**: PCV, aluminium,
drzwi przesuwne. Deklarowane „Wooden Door / Security Door / Composite Door" tego nie wyklucza
(wąska klamka wejdzie wszędzie), ale sens tego produktu to profile — i stąd rozmiary 92 mm.

**[DEDUKCJA]** Dla naszego rynku to akurat niezły strzał: drzwi balkonowe i wejściowe z PCV
są w Polsce wszędzie, a 92/35 to ich standard.

## 6. Co dopisać do zapytania do Liliwise

1. Który **układ TTLock** (SN534-15-1, SN534-16-1, SN503-18-1, SN8534-3-1, SN8534-1-1, SN8503-2-1)
2. Czy jest mortise **5072** (dorn 50, rozstaw 72) — polskie drzwi drewniane
3. **Klasa IP** zamiast „Waterproof: Yes"
4. **Raporty RED + EN 18031, EMC, LVD** — nie „CE, FFC, ROSH"
5. **Waga** sztuki z opakowaniem
6. **Trzpień** — czy 8 mm kwadratowy
7. Czy zapadka/klamka jest **odwracalna lewy-prawy**
8. Czy **zamek wpuszczany jest w cenie**, czy dokupywany osobno
9. Żywotność baterii **osobno dla BLE i dla BLE+Wi-Fi**

## 7. Jedno zdanie

> **S31 to wąskoprofilowa klamka z trzema europejskimi rozmiarami zamka — w tym 92 mm, czyli
> polskim standardem drzwi PCV i aluminiowych — ale jej „certyfikaty" to literówki i amerykański
> FCC, brakuje IP, RED/EN 18031 i modelu układu TTLock, więc do zamówienia daleko.**

---

## Źródła

- specyfikacja produktu Liliwise S31 — zrzut strony przysłany przez usera 2026-09-19
- [CentrumOkuc — zamki wpuszczane do aluminium i PCV](https://online.centrumokuc.pl/26-zamki-wpuszczane-do-aluminium-pcv-stal) · [okucia24 — KFV 49PZ 24/45/92/8](https://okucia24.com/zamek-kfv-49pz-24-45-92-8-glowny-zapadkowo-ryglowy-do-drzwi-z-pcv,id2523.html) · [MIBAR — zamek wąski 92/35](https://mibar.pl/zamek-do-drzwi-pvc-35-92-romb-041-049)
