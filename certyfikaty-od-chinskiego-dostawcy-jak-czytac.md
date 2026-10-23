# Ściana certyfikatów na stronie chińskiego dostawcy — co z tego jest coś warte

Spisane 2026-09-19 na podstawie zrzutu ze strony Liliwise (rząd logotypów + galeria
certyfikatów) przysłanego przez usera. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md) (sekcja 5 — czego żądać) ·
[kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md) ·
[liliwise-s31-analiza-specyfikacji.md](liliwise-s31-analiza-specyfikacji.md)

---

## 1. Krótka odpowiedź

**Nie „nic nie znaczą" — ale w tej formie nie znaczą NIC DLA CIEBIE.** Ściana logotypów
i zdjęcia dokumentów z daleka to materiał marketingowy. Wartość prawną ma **plik PDF,
w którym zgadza się model, normy i laboratorium** — i dopiero to trzeba obejrzeć.

## 2. Co jest czym na tym pasku logotypów

| Logo | Co to naprawdę jest | Wartość dla sprzedaży w Polsce |
|---|---|---|
| **CE** | **deklaracja producenta**, nie certyfikat. Nikt tego nie „wydaje" | 🟡 liczy się dokument pod spodem, nie znaczek |
| **FCC** | USA | 🔴 zero |
| **UL** | USA / Kanada | 🔴 zero (ale świadczy o poziomie fabryki) |
| **UKCA** | Wielka Brytania po brexicie | 🔴 zero |
| **RoHS** | ograniczenie substancji — realny wymóg UE | 🟢 potrzebne, ale to dopiero jeden z kilku |
| **ISO 9001 / 14001 / 45001** | systemy zarządzania **fabryką** | 🟡 nic nie mówią o produkcie |
| **TÜV Rheinland** | prawdziwa jednostka badawcza | 🟡 logo nie mówi, **co** badano |
| **IP66** | 🔴 **to nie certyfikat, tylko deklarowany parametr** | trzeba raportu z badania |
| **FIREPROOF** | 🔴 **nie istnieje jako certyfikat** — hasło marketingowe | zero |
| czerwono-niebieski znak | nie rozpoznaję **[DO SPRAWDZENIA]** — zapytać, co to | — |

🔴 **Czego w tym rzędzie NIE MA, a jest najważniejsze: RED (2014/53/UE) i EN 18031.**
To obowiązek dla każdego urządzenia radiowego w UE od 1.08.2025. Brak wzmianki zgadza się
z tym, co widać w [specyfikacji S31](liliwise-s31-analiza-specyfikacji.md).

## 3. 🔴 Co widać na samych certyfikatach

Na czytelnym dokumencie („Certificate of Conformity", wystawca **CTB**) wnioskodawcą
i producentem jest firma o nazwie w rodzaju **Guangdong / Guangzhou Lightsource Electronics
Limited** — **nie „Liliwise"**.

**[DO SPRAWDZENIA]** i to jest teraz pytanie numer jeden:
- czy Lightsource to ta sama firma co Liliwise (inna nazwa rejestrowa), czy fabryka, która
  dla nich produkuje, czy **cudze dokumenty użyte jako ilustracja**
- czy **na liście modeli w certyfikacie jest S31** — certyfikat na inny model jest dla nas bezwartościowy

Wystawca **CTB to chińskie laboratorium badawcze**, nie jednostka notyfikowana UE. Przy drodze
samodzielnej deklaracji to dopuszczalne — ale jeśli EN 18031 zostanie zastosowana z ograniczeniami,
potrzebna jest **jednostka notyfikowana**, a tej tu nie widać.

## 4. Procedura sprawdzenia — pięć kroków

1. **Poproś o pliki, nie o zdjęcia.** PDF-y: deklaracja zgodności (DoC) + raporty z badań
2. **Sprawdź model.** Czy na dokumencie jest dokładnie **S31**. Jeśli jest lista modeli —
   czy S31 jest na liście
3. **Sprawdź, jakie dyrektywy i normy** wymienia deklaracja. Dla klamki z BLE/Wi-Fi powinny być:
   **2014/53/UE (RED)**, EN 300 328, EN 301 489-1/-17, EN 62368-1, EN 62311/62479,
   **EN 18031-1 i -2**, RoHS (EN IEC 63000)
4. **Zweryfikuj wystawcę w publicznej bazie** — to darmowe i rozstrzygające:
   - TÜV Rheinland → **Certipedia**
   - UL → **UL Product iQ**
   - laboratorium chińskie → numer akredytacji **CNAS / ILAC**
5. **Sprawdź zgodność nazw.** Firma na certyfikacie musi być tą, od której kupujesz — albo
   musi być udokumentowane, że to jej fabryka

## 5. 🔴 I tak nie zdejmuje to z Ciebie odpowiedzialności

Nawet komplet poprawnych dokumentów nie zmienia tego, że **jako importer odpowiadasz wobec
UOKiK-u i klienta** — [kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md).
Dokumenty są warunkiem, żeby w ogóle wolno było sprzedawać, a nie tarczą.

## 6. Jedno zdanie

> **Ściana logotypów to grafika: połowa z nich dotyczy innych rynków, dwa („IP66", „FIREPROOF")
> w ogóle nie są certyfikatami, brakuje jedynego, który dziś jest w UE kluczowy (RED + EN 18031),
> a na widocznych dokumentach figuruje inna nazwa firmy niż Liliwise — więc pytasz o pliki PDF
> z numerem modelu S31 i dopiero wtedy jest o czym rozmawiać.**
