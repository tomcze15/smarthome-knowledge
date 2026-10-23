# EURA-TECH — analiza dwóch deklaracji zgodności

Spisane 2026-09-20 z dwóch dokumentów przysłanych przez usera. Dane firmowe zweryfikowane
w KRS (API Ministerstwa Sprawiedliwości), obecność w Bluetooth SIG sprawdzona w Member
directory i Qualified Product Database. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [certyfikaty-sciaga-co-jest-potrzebne.md](certyfikaty-sciaga-co-jest-potrzebne.md) ·
[jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md](jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md) ·
[kto-w-ue-brandu-chinskie-klamki-sprawdzone.md](kto-w-ue-brandu-chinskie-klamki-sprawdzone.md)

---

## 1. Firma — dane potwierdzone [DOK — KRS, odczyt 20.09.2026]

| Na deklaracji | W KRS |
|---|---|
| EURA-TECH sp. z o.o. | ✅ zgodne |
| NIP 5881009008 | ✅ zgodny |
| ul. Przemysłowa 35A, 84-200 Wejherowo | ✅ zgodny |
| Kapitał 1 500 000 zł | ✅ zgodny |
| KRS 0000190941 | ✅ wpis **28.01.2004** |

Reprezentacja: *„do składania oświadczeń i podpisywania w imieniu spółki upoważniony jest
**każdy członek zarządu samodzielnie**"* — w zarządzie jest wiceprezes o nazwisku na D,
więc podpis „Andrzej Dudziński, V-CE PREZES" jest skuteczny [DOK].

**[DO SPRAWDZENIA]** numer BDO 000015700 — brak publicznego API, zweryfikować w wyszukiwarce BDO.

## 2. Dwie deklaracje

| | ELH-01H4 | ELH-25H4 |
|---|---|---|
| Nr / data | 81/2022 · **08.09.2022** | 5/2026 · **22.01.2026** |
| Rok naniesienia CE | 22 | 26 |
| Numery seryjne | **22xxxxxxxx, 23xxxxxxxx, 24xxxxxxxx** | nie podano |
| Dyrektywy | RoHS 2011/65/UE · RED 2014/53/UE | RoHS 2011/65/UE **+ 2015/863** · RED 2014/53/UE |
| Normy | EN IEC 62368-1:2020 · EN 62479:2010 · EN 301 489-1 V2.2.3 · EN 301 489-17 V3.2.4 · EN 300 328 V2.2.2 | **identyczne** |
| 🔴 EN 18031 | brak | **brak** |

## 3. 🔴 Problem z EN 18031 — i dlaczego dotyczy głównie nowszej deklaracji

Normy cyberbezpieczeństwa **EN 18031** są obowiązkowe dla urządzeń radiowych wprowadzanych
do obrotu w UE **od 1.08.2025**.

| Dokument | Ocena [DEDUKCJA] |
|---|---|
| **ELH-01H4 (2022)** | 🟡 brak EN 18031 jest **chronologicznie wytłumaczalny**. Ale numery seryjne obejmują roczniki **22, 23, 24** — a produkt bywa w sprzedaży w 2026. **Egzemplarze wprowadzane po 1.08.2025 potrzebują nowej deklaracji.** Pytanie: czy istnieje jej aktualna wersja |
| **ELH-25H4 (styczeń 2026)** | 🔴 **tu wymówki nie ma.** Deklaracja wystawiona pół roku po wejściu obowiązku, bez ani jednej normy cyberbezpieczeństwa |

**Drobiazg, ale znamienny:** w obu dokumentach EN 62479:2010 opisano jako „(lvd)". To nie jest
norma niskonapięciowa, tylko ocena narażenia na pole elektromagnetyczne. Norma właściwa,
opis błędny — w obu, czyli szablon nie był weryfikowany od 2022.

## 4. Bluetooth SIG — pytanie bez odpowiedzi, nie werdykt

**Sprawdzone [DOK — 20.09.2026]:** „EURA-TECH", „EURATECH", „Wejherowo", „Eura", „ELH-25H4",
„ELH" — **brak w Member directory i brak w Qualified Product Database**.

EURA-TECH deklaruje się producentem urządzenia radiowego 2,4 GHz pod własną marką, a SIG
pisze wprost: *„Your supplier or other member companies cannot qualify your products on your
behalf"* [DOK].

🟡 **Ale brak w bazie to nie dowód naruszenia.** Uczciwe zastrzeżenia:

- **deklaracja nie zawiera słowa „Bluetooth"** — EN 300 328 obejmuje całe pasmo 2,4 GHz:
  Wi-Fi, Zigbee, rozwiązania firmowe. Z samego dokumentu nie wynika, że to BLE
  (to, że klamka działa na TTLock, wiemy z oferty sprzedażowej, nie z deklaracji)
- **data publikacji w bazie produktów jest ustalana przez firmę** — produkt może być
  zakwalifikowany, a jeszcze niewidoczny
- kwalifikacja mogła iść **pod nazwą fabryki albo konstrukcji**, nie marki

## 5. 🟢 Dlaczego to i tak nie jest NASZ problem [DEDUKCJA]

| | |
|---|---|
| **Prawnie** | Bluetooth SIG to **prywatna umowa licencyjna**, nie przepis państwowy. Brak kwalifikacji nie jest wadą w rozumieniu CE ani nadzoru rynku — nie czyni wyrobu nielegalnym w UE |
| **Nasza rola** | odsprzedając pod marką EURA jesteśmy **odsprzedawcą** — SIG wprost zwalnia nas z czegokolwiek |
| **Ryzyko realne** | to **ryzyko dostaw**, nie prawne: gdyby SIG wszczął postępowanie, dostawa mogłaby stanąć |

➡️ **Traktować jako żółtą flagę co do staranności dostawcy, nie jako czerwoną flagę dla nas.**
Ta sama staranność, która zgubiła EN 18031 i opis „(lvd)" od 2022, tłumaczy też brak w bazie SIG.

## 6. Co z tym zrobić

1. **Poprosić o aktualną deklarację** dla modelu, który faktycznie kupujemy — z datą po 1.08.2025
2. **Zapytać wprost o EN 18031**: jest raport, czy uznano, że art. 3.3 RED nie dotyczy — i dlaczego
3. **Zapytać o Bluetooth SIG**: czy produkt jest zakwalifikowany i pod jakim numerem konstrukcji
4. Odpowiedzi **na piśmie** — przy sporze to one pokazują, że dochowaliśmy staranności dystrybutora

## 7. Jedno zdanie

> **Firma jest prawdziwa, dokument jest prawdziwy i to dokładnie ten dokument, o który trzeba
> prosić — ale brak EN 18031 w deklaracji ze stycznia 2026 i brak firmy w bazach Bluetooth SIG
> to dwa pytania, które trzeba im zadać, zanim zamówimy cokolwiek.**
