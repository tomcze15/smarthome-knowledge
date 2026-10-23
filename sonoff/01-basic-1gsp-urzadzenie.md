# BASIC-1GSP — urządzenie

Źródło: `https://help.sonoff.tech/docs/BASIC-1GSP` (instrukcja użytkownika, **nie** dokumentacja API).

## Co to jest

Jednokanałowy przekaźnik z pomiarem energii, **na szynę DIN**, montowany za wyłącznikiem
nadprądowym w rozdzielnicy. Nazwa handlowa: „Basic DIN Rail (Matter over Wi-Fi Version)".

Zastosowania wskazane przez producenta: ładowarki EV, ogrzewanie podłogowe, pompy ciepła,
duże AGD.

## Specyfikacja [DOK]

| Parametr | Wartość |
|---|---|
| Model | BASIC-1GSP |
| MCU | **BK7238** ← patrz uwaga w README, to koliduje z przykładem z dokumentacji API |
| Zasilanie / obciążenie | 100–240 V~ 50/60 Hz, **32 A max**, 7680 W |
| Wi-Fi | IEEE 802.11 b/g/n **2.4 GHz** (5 GHz nie) |
| Wymiary / masa | 84 × 66,5 × 18 mm, 92,1 g |
| Obudowa | PC, UL94 V-0 |
| Temperatura pracy | −15 °C ~ 50 °C |
| Wilgotność | 10–90 % RH, bez kondensacji |
| Certyfikaty | CE / RoHS / FCC / CP65 / **Matter** |

⚠️ Instalacja przez elektryka, przy odłączonym zasilaniu. Wymagany wyłącznik nadprądowy
(MCB) lub RCBO przed urządzeniem.

## Funkcje istotne dla integracji [DOK]

- **Pomiar energii** — statystyki dzienne/tygodniowe/miesięczne i **180 dni historii**,
  wraz z kosztem; eksport danych z ostatnich 180 dni
- **Progi ochronne** — własne progi prądu / napięcia / mocy; przekroczenie = odcięcie zasilania,
  zdalne przywrócenie możliwe
- **Ochrona ładowania** — odcięcie gdy moc utrzymuje się poniżej progu przez zadany czas
  (anty-przeładowanie akumulatorów)
- **Zachowanie po zaniku zasilania** — ON / OFF / ostatni stan / odwrotność ostatniego stanu
- **Timery** — harmonogram, odliczanie, cykle, timer losowy, inching
- **Logi** — historia zmian stanu, 3 miesiące wstecz
- **LAN Control** — ⚠️ to jest tryb **aplikacji eWeLink** (apka gada z urządzeniem przez router
  z pominięciem chmury), a **nie** API dla programisty. Nie mylić z lokalnym RPC z pliku 02.

## Matter — co jest wspierane [DOK]

| Platforma | Zdalne ster. | Głos | Sceny | **Statystyki zużycia** |
|---|---|---|---|---|
| Apple Home | ✓ | ✓ | ✓ | ✗ |
| Google Home | ✓ | ✓ | ✓ | ✗ |
| Alexa | ✓ | ✓ | ✓ | ✗ |
| SmartThings | ✓ | ✓ | ✓ | **✓** |
| Home Assistant | ✓ | ✓ | ✓ | **✓** |

To jest realny plan B: **przez Matter Home Assistant widzi też pomiar energii.**

## Przycisk i diody [DOK]

**Przycisk:**

| Akcja | Efekt |
|---|---|
| Przytrzymanie 5 s | Wejście w tryb parowania |
| Pojedyncze naciśnięcie | Włącz / wyłącz |
| Krótkie naciśnięcie w trybie parowania | Wyjście z trybu parowania |

**Dioda Wi-Fi (niebieska)** — tryb eWeLink:

| Zachowanie | Znaczenie |
|---|---|
| Dwa krótkie + jedno długie | Tryb parowania |
| Nie świeci | Online |
| Mignięcie pojedyncze | Offline |
| Mignięcie podwójne | Połączony z routerem, brak chmury |

W trybie Matter tak samo, z tą różnicą, że „połączony z routerem, brak chmury" = **potrójne**
mignięcie.

**Dioda zasilania (czerwona):** świeci = przekaźnik załączony.

## Czego na tej stronie NIE ma

Zero endpointów, zero pól JSON, zero informacji o lokalnym API. To wyłącznie instrukcja
obsługi. API żyje w osobnej gałęzi help center — plik [02](02-api-rpc-lokalne.md).
