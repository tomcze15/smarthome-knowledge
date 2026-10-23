# Konwencja notatek w tym vaulcie

Żeby po pół roku dało się to czytać. Spisane 2026-09-10.

---

## 1. Struktura folderów

```
smarthome/
├── KONWENCJA-NOTATEK.md      ← ten plik
├── <temat>.md                ← notatki przekrojowe, nie mieszczące się w żadnym folderze
├── matter/                   ← temat rozbudowany = własny folder
│   ├── README.md             ← spis i router
│   └── NN-nazwa.md
├── sonoff/
├── shelly/
└── zalaczniki/               ← obrazki wklejane do notatek
```

**Kiedy zakładać folder:** gdy temat ma **co najmniej trzy** notatki albo wiadomo,
że będzie miał. Jeden plik nie potrzebuje folderu.

## 2. README jako router, nie jako treść

Każdy folder ma `README.md`, który zawiera **wyłącznie**:

- spis plików z jednozdaniowym opisem każdego
- stan (`✅ gotowe` / `⏳ zaplanowane`)
- linki do powiązanych notatek spoza folderu
- ewentualnie źródło prawdy dla tematu

🔴 **Do README nie wpisuje się wiedzy.** Wiedza żyje w plikach tematycznych.
README ma mówić **gdzie szukać**, nie **co wiedzieć**.

## 3. Numeracja = kolejność czytania

`01-`, `02-`, `03-` układają notatki w **ścieżkę nauki** — od podstaw do szczegółów.
Nie w kategorie, nie alfabetycznie.

Ktoś, kto czyta od `01` do końca, powinien rozumieć temat. Ktoś, kto szuka konkretu,
idzie prosto do właściwego numeru przez README.

## 4. Jeden plik = jedno pojęcie

**Limit orientacyjny: ~300 linii.** Powyżej — dzielisz.

Notatka ma dać się przeczytać w jednym posiedzeniu. Jeśli w środku pojawiają się dwa
niezależne tematy, to są dwie notatki.

## 5. Legenda pewności — obowiązkowa

| Znacznik | Znaczenie |
|---|---|
| **[DOK]** | wprost z dokumentacji, przepisane ze źródła |
| **[DEDUKCJA]** | wniosek logiczny, ale niepotwierdzony |
| **[DO SPRAWDZENIA]** | wymaga weryfikacji na sprzęcie albo u dostawcy |

🎯 **To jest najważniejsza reguła w całym vaulcie.** Notatka bez rozróżnienia „wiem"
od „tak mi się wydaje" jest gorsza niż jej brak, bo kłamie z pełnym przekonaniem.

Do tego emoji dla wagi: 🔴 krytyczne · ⚠️ uwaga · 🟢 dobra wiadomość · 🎯 sedno

## 6. Znaczniki (tagi) dla rzeczy przekrojowych

Obsidian indeksuje `#tagi`. Warto oznaczać to, co przecina foldery:

```
#do-sprawdzenia   ← otwarte pytania, jedno kliknięcie i masz listę wszystkich
#decyzja          ← miejsce, gdzie coś zostało postanowione
#pulapka          ← rzecz, na której łatwo się naciąć
```

**[DEDUKCJA]** Docelowo `#do-sprawdzenia` powinien zastąpić szukanie tekstu
„[DO SPRAWDZENIA]" — ale nie ma sensu przerabiać wstecz wszystkich notatek.
Stosujemy w nowych, stare zostają jak są.

## 7. Linkuj zamiast powtarzać

Jeśli coś jest już wyjaśnione w innej notatce — **linkuj**, nie przepisuj.
Skopiowana treść rozjedzie się przy pierwszej poprawce.

Wyjątek: jedno zdanie podsumowania plus link. To pomaga czytającemu, nie musi klikać.

## 8. Korekty zostają widoczne

Gdy okazuje się, że coś w notatce było błędne — **nie kasujemy po cichu**.
Zostaje sekcja `## 🔴 KOREKTA` z datą i tym, co było źle.

Powód: inaczej za miesiąc padnie to samo pytanie i nie będzie wiadomo, że już to
przerabialiśmy i dlaczego wniosek się zmienił.

## 9. Data i źródło

Każda notatka zaczyna się od linii z datą spisania i skąd pochodzą dane.
Bez tego po pół roku nie wiadomo, czy „aktualne ceny" to sprzed tygodnia czy roku.
