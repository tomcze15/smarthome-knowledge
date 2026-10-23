# SONOFF ODM Partnership — co się pod tym kryje

Źródło: `https://odm.sonoff.tech/` (cała strona przeczytana 2026-08-17) + strony CoolKit
i eWeLink. Legenda pewności jak w [README.md](README.md).

---

## 🔴 Najważniejsze: to nie jest program z warunkami, tylko formularz kontaktowy

Cała witryna to **jedna strona z kotwicami** (`#capabilities`, `#models`, `#process`,
`#faq`, `#odm-inquiry`). Zero podstron, zero linków wychodzących, zero dokumentów.

**Czego NIE ma na stronie:**

- ❌ MOQ (minimalna wielkość zamówienia)
- ❌ jakichkolwiek cen i widełek
- ❌ terminów i czasów realizacji
- ❌ kosztów certyfikacji
- ❌ warunków umownych, wzorów umów
- ❌ informacji o własności firmware'u i IP
- ❌ warunków wyłączności

Strona sama to przyznaje, w sekcji formularza:

> „Submit the essentials first. The ODM partnership team can follow up for **MOQ,
> certification, annual volume, target price, and technical details after the first review**."

Czyli: **najpierw Cię kwalifikują, potem rozmawiają o liczbach.** To standard w chińskim
ODM, ale trzeba wiedzieć, że wchodzi się w proces sprzedażowy, a nie wybiera z cennika.

---

## Kto jest po drugiej stronie — dwie firmy, nie jedna

| Firma | Rola | Marka |
|---|---|---|
| **ITEAD** | sprzęt: projekt, produkcja, testy | **SONOFF** |
| **CoolKit Technology** | software: firmware, apka, chmura | **eWeLink** |

Obie z Shenzhen, [dzieliły biuro i przeprowadziły się do wspólnej siedziby](https://ewelinkcommunity.net/2021/10/itead-sonoff-and-coolkit-ewelink-moved-to-new-office/).
Strona ODM sprzedaje je razem jako „one partnership entry".

⚠️ **Praktyczna konsekwencja: umowa może dotyczyć dwóch podmiotów** — sprzęt od jednego,
oprogramowanie i chmura od drugiego. Warto to wyjaśnić na starcie, bo rozjeżdża
odpowiedzialność przy reklamacjach i przy wsparciu.

To nie jest startup — **ponad 500 firm produkcyjnych korzysta już z platformy eWeLink**,
a pod nią sprzedają się marki takie jak QIACHIP, QIHANG czy PINXUAN. Biznes działa
od lat, tylko dopiero teraz dostał ładny landing page.

---

## Trzy modele współpracy [DOK]

### 01 · Software/Firmware-Only — *„Led by eWeLink"*

Dostarczenie warstwy software'owej **do Twojego istniejącego sprzętu**. Definicja produktu,
projekt sprzętu i specyfikacja zostają po stronie Twojego dostawcy hardware'u.

→ Najniższy próg wejścia. Sensowne, jeśli masz już sprzęt albo innego producenta.

### 02 · Redesign and Customization — *„Industrial design + firmware"*

Odświeżenie **istniejącej platformy sprzętowej** Sonoffa: wzornictwo pod Twoją markę
plus adaptacja firmware'u.

> „This path shortens time to market and reduces the risk and cycle of full new product
> development."

→ **Najbardziej realistyczna opcja dla mniejszego gracza.** Bierzesz działający produkt,
zmieniasz obudowę i branding.

### 03 · New Product Development — *„Dedicated joint team"*

Nowy produkt od definicji przez sprzęt, software, testy po plan produkcji masowej.

> „Best for **strategic clients**"

→ Tłumaczenie z korporacyjnego: **duże wolumeny.** Jeśli pytasz o MOQ, to nie ten model.

---

## Zakres techniczny

**Protokoły:** Wi-Fi · Zigbee · Matter · **Thread**

⚠️ Ciekawostka: **Thread pojawia się w ofercie ODM, choć nie widziałem go w asortymencie
detalicznym** ([08-asortyment.md](08-asortyment.md)). Albo mają gotowe rozwiązanie
niewydane pod własną marką, albo to deklaracja na wyrost. [DO SPRAWDZENIA]

**Kategorie:** Switches & Relays · Plugs & Energy · Security & Sensors ·
Controllers & Gateways

**Software:** firmware wbudowany, aplikacja mobilna, **chmura publiczna ALBO prywatna**

🟢 **Opcja chmury prywatnej to najważniejszy punkt całej oferty** — patrz sekcja
o pytaniach niżej.

---

## Proces — sześć kroków [DOK]

| Krok | Co się dzieje |
|---|---|
| 01 Submit Inquiry | Firma, rynek, kategoria produktu, kierunek projektu |
| 02 Initial Review | **Kwalifikacja** — sprawdzają dopasowanie, skalę, region |
| 03 Model Alignment | Wybór jednego z trzech modeli |
| 04 Solution Proposal | Zakres, software, chmura/apka, harmonogram |
| 05 Sample & Testing | Walidacja funkcji i jakości |
| 06 Mass Production | Produkcja, pakowanie, dostawa |

**Do pierwszego kontaktu wystarczy:** profil firmy, strona WWW, region docelowy, kategoria
produktu, krótki opis. Próg wejścia do *rozmowy* jest niski — próg do *kontraktu* nieznany.

Formularz wymaga: nazwa firmy, strona/sklep online, osoba kontaktowa, e-mail służbowy,
kraj, kategoria, opis projektu, zgoda na kontakt.

---

## Jedyne opublikowane warunki, jakie znalazłem

Nie dotyczą ODM, tylko **API platformy eWeLink** — ale pokazują styl firmy:
[eWeLink-API/Pricing.md](https://github.com/CoolKit-Technologies/eWeLink-API/blob/main/en/Pricing.md)

| | Personal (darmowy) | Enterprise (płatny) |
|---|---|---|
| Poświadczenie API | 1 szt., ważne rok | 1 szt., ważne rok |
| Wsparcie techniczne | ❌ brak | do **90 dni** (e-mail, telefon, WeChat) |
| Dokumentacja | ograniczona | pełna |
| Lista API | ograniczona | szersza, „higher stability" |
| Liczba wywołań | ograniczona | wyższa |
| Gwarancja stabilności | ❌ | ✅ |
| **Cena** | 0 | **niepodana — kontakt `bd@coolkit.cn`** |

Wzorzec się powtarza: **kwot nie publikują nigdzie.**

Zwróć uwagę na „up to 90 days of technical support" — wsparcie jest **czasowe**, nie
bezterminowe. Przy ODM warto sprawdzić, jak długo trwa wsparcie po wdrożeniu.

---

## 🔴 Pytania, które trzeba zadać ZANIM się zaangażujesz

Uszeregowane od najważniejszych dla kogoś, kto ceni local-first.

### Chmura i zależność

1. **Czy produkt będzie działał bez Waszej chmury?** Model „private cloud" jest w ofercie —
   ile kosztuje i czy da się w ogóle bez chmury
2. **Co się dzieje z moimi urządzeniami u klientów, jeśli przestanę płacić za chmurę?**
   To pytanie o „bricking risk" — realne w tym modelu biznesowym
3. Kto jest administratorem danych użytkowników końcowych — Ty czy CoolKit? **RODO,
   jeśli sprzedajesz w UE**
4. Gdzie fizycznie stoją serwery

### Software i własność

5. **Czy dostaję źródła firmware'u, czy tylko binaria?**
6. Kto jest właścicielem IP powstałego w projekcie
7. **Czy mogę dostać lokalne API (JSON-RPC) w moim produkcie?** ← patrz niżej, kluczowe
8. Czy mogę sam wydawać aktualizacje OTA, czy idą przez ich infrastrukturę
9. Co po zakończeniu wsparcia — czy dostanę możliwość samodzielnego utrzymania

### Handel

10. **MOQ** — pierwszy egzemplarz i kolejne partie
11. Kto płaci za certyfikację (CE, RoHS, a przy Matter także **certyfikacja CSA**)
12. Na kogo wystawione certyfikaty — na Ciebie czy na nich
13. Wyłączność: czy ten sam projekt trafi do konkurencji
14. Odpowiedzialność za wady — który podmiot, ITEAD czy CoolKit
15. Warunki płatności, waluta, Incoterms

### 🎯 Pytanie numer 7 jest dla Ciebie najważniejsze

Z całego naszego rozpoznania wynika, że **lokalne JSON-RPC ma u Sonoffa tylko garstka
modeli** ([08-asortyment.md](08-asortyment.md)) — to wyjątek, nie standard.

Jeśli robisz ODM i zależy Ci na local-first, **musisz to wpisać do specyfikacji jawnie**.
Domyślnie dostaniesz produkt zorientowany na chmurę eWeLink, bo to jest ich model
biznesowy. Oni zarabiają na chmurze, nie na sprzęcie.

Konkretnie żądaj: lokalny web console, JSON-RPC 2.0 po HTTP/WS, konfigurowalny broker MQTT,
autodiscovery HA — czyli **dokładnie to, co ma MINI-1GSP**. To jest dowód, że potrafią.

---

## Kontekst branżowy — czego się spodziewać

⚠️ **To NIE są warunki eWeLink**, tylko typowe wartości w branży, jako punkt odniesienia:

- ODM z gotowym projektem: MOQ zwykle **100–1000 sztuk** (niższe niż OEM, bo oprzyrządowanie
  już istnieje)
- White label z customizacją: **500–1000 sztuk**, czas realizacji **8–12 tygodni**
- Sama zmiana logo i opakowania: **2–4 tygodnie**

Model 02 (Redesign) powinien mieścić się w tych widełkach. Model 03 (New Product) będzie
o rząd wielkości wyżej — nowe formy wtryskowe to koszt rzędu dziesiątek tysięcy dolarów,
który ktoś musi zamortyzować.

---

## Ocena

**Plusy:**
- Prawdziwy, działający biznes — 500+ firm na platformie, lata doświadczenia
- Trzy modele, w tym jeden o niskim progu (software-only)
- Opcja chmury prywatnej — rzadkość w tym segmencie
- Szeroka baza sprzętowa do przerobienia zamiast projektowania od zera
- Próg do pierwszej rozmowy praktycznie zerowy

**Minusy:**
- **Zero przejrzystości cenowej** na każdym poziomie, konsekwentnie
- Dwa podmioty prawne w jednej ofercie
- Model biznesowy oparty na chmurze — local-first trzeba wywalczyć
- Wsparcie techniczne czasowe („up to 90 days")
- Landing page jest nowy i ogólnikowy; brak studiów przypadku i referencji

**[DEDUKCJA] Werdykt:** warto wysłać zapytanie, bo to nic nie kosztuje i dopiero wtedy
poznasz liczby. Ale idź tam **z gotową listą pytań** i świadomością, że rozmawiasz
z firmą, której model biznesowy stoi na chmurze — czyli dokładnie na tym, od czego
chcesz się uniezależnić.
