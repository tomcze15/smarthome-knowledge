# Licencja premium jako podpisany token — działa offline

Spisane 2026-10-23. Źródło: koncepcja rozpisana wcześniej przez AI, przekazana przez usera.
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [local-first.md](local-first.md) · [ocena-szczera-modelu.md](ocena-szczera-modelu.md)

---

## 1. Idea

Informację o subskrypcji premium niesie **podpisana cyfrowo licencja**, a nie flaga ani
zapytanie od telefonu.

- **backend** ma **klucz prywatny** — nigdy go nikomu nie udostępnia
- **każdy box** ma wbudowany **klucz publiczny**
- kluczem prywatnym się **podpisuje**, publicznym można tylko **sprawdzić podpis**

➡️ Nawet pełny dostęp do boxa nie pozwala wygenerować fałszywej licencji.

## 2. Co jest w licencji

Token podpisany kluczem prywatnym backendu:

| Pole | Po co |
|---|---|
| identyfikator użytkownika | czyja licencja |
| **identyfikator boxa** | licencja działa tylko na tym jednym urządzeniu |
| poziom subskrypcji | BASIC / COMFORT / PRO / VIP |
| lista odblokowanych funkcji | co wolno uruchomić |
| **data wygaśnięcia** | patrz sekcja 4 |

## 3. Weryfikacja na boxie — całkowicie offline

Zanim box uruchomi funkcję premium, sprawdza:

1. czy **podpis** jest prawidłowy (kluczem publicznym)
2. czy licencja jest wystawiona **dla tego boxa**
3. czy **nie wygasła**

Box **nie pyta serwera o zgodę**.

## 4. Krótka ważność = mechanizm anulowania

- licencja ważna krótko, np. **7 dni**, odnawiana, gdy box albo telefon ma internet
- klient przestaje płacić → backend **przestaje wystawiać nowe licencje** → stara sama wygasa
- **telefon jest tylko kurierem** — przekazuje boxowi nową licencję, ale box i tak sam sprawdza
  podpis, więc zmodyfikowana aplikacja nie podrobi uprawnień

## 5. Ochrona przed oszukiwaniem zegarem

- box zapamiętuje **najpóźniejszy czas, jaki kiedykolwiek widział**, i odmawia działania,
  gdy zegar nagle cofnie się do tyłu
- box pamięta **ostatni kontakt z serwerem** — po przekroczeniu ustalonego limitu dni bez
  kontaktu wyłącza funkcje premium, nawet jeśli licencja formalnie jest jeszcze ważna

---

## 6. Uwagi [DEDUKCJA]

- 🔴 **Licencja może gasić tylko funkcje premium, nigdy podstawowe.** Otwieranie drzwi,
  światło, automatyzacje lokalne mają działać bez licencji. To jest dokładnie przewaga nad
  Homey Self-Hosted Server, który po wygaśnięciu abonamentu **cały przechodzi w offline**
- **Limit dni bez kontaktu** (sekcja 5) wymaga ostrożności przy klientach, którzy celowo trzymają
  box bez internetu — lepiej długi (np. 30 dni) i z wyraźnym ostrzeżeniem w apce przed wyłączeniem
- reakcja na cofnięty zegar: zamiast „odmawia działania" bezpieczniej **wyłączyć premium
  i pokazać komunikat**, niż blokować cały box (np. po wymianie baterii RTC)
- klucz publiczny w boxie: przewidzieć **rotację kluczy** (kilka zaufanych kluczy, nowy
  wgrywany aktualizacją), inaczej wyciek klucza prywatnego = wymiana firmware we wszystkich boxach
- format: standardowy podpisany token (np. JWT/PASETO z Ed25519) zamiast własnego
