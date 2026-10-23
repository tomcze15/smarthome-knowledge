# Gdzie kupować zamki: dystrybutor w UE czy marka europejska

Spisane 2026-09-20. Sprawdzone: rejestry firm na stronach sprzedawców, dokumentacja Tedee,
bazy Bluetooth SIG. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md](jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md) ·
[kto-odpowiada-za-wadliwa-klamke-prawo.md](kto-odpowiada-za-wadliwa-klamke-prawo.md) ·
[zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md)

---

## 1. Uczciwie: nie mogę nikogo „zaręczyć"

Nie ma rejestru „zaufanych dystrybutorów". Mogę podać **firmy, które istnieją i sprzedają
te towary w Polsce**, oraz **kryteria, po których sam sprawdzisz, czy są warte zaufania**.
Poniżej jedno i drugie.

## 2. Polscy sprzedawcy TTLock [DOK — odczyt 20.09.2026]

| Firma | Dane rejestrowe | Uwagi |
|---|---|---|
| **Spreest Sp. z o.o.** | ✅ pełne: NIP 8222391241, KRS 0000948155, REGON 521050049, Długa Kościelna | sprzedaje w całej UE (wybór walut), część towaru pod **własnym oznaczeniem „Spreest"** — czyli ktoś tam wziął rolę wprowadzającego |
| **Altum Polska** (Maciej Klukowski) | 🟡 jednoosobowa działalność, Warszawa | sklep z linią TTLock SmartLock |
| **HAT Pol** | ⚠️ do sprawdzenia | zaprasza hurtownie i instalatorów, rabaty partnerskie — **najbardziej B2B-owy przekaz** |
| **Keylock** | ⚠️ brak danych firmy na stronie kontaktu | sprzedaż + montaż w całej Polsce |
| **Hotels Media** | ⚠️ do sprawdzenia | profil hotelowy |

**[DEDUKCJA]** To są **sklepy**, nie dystrybutorzy w rozumieniu prawa. Zanim je potraktujesz
jak tarczę, sprawdź sekcję 3 — bo sam fakt, że firma ma polski NIP, **nie znaczy, że ktoś
wykonał obowiązki importera**.

## 3. 🔴 Pięć pytań, które rozstrzygają wszystko

Wyślij je do każdego kandydata. Odpowiedzi są weryfikowalne:

1. **Kto jest importerem tego towaru do UE?** Proszę o nazwę i adres — takie, jakie są
   na produkcie lub opakowaniu
2. **Czy dostanę deklarację zgodności UE** dla konkretnego modelu, z RED (2014/53/UE) i RoHS?
3. **Czy w raportach jest EN 18031?** (cyberbezpieczeństwo, obowiązkowe od 1.08.2025)
4. **Jaki macie numer BDO** i czy prowadzicie sprzedaż B2B z rabatem?
5. **Czy towar jest na magazynie w Polsce**, czy zamawiany z Chin na moje zamówienie?

**Jak czytać odpowiedzi:**

| Odpowiedź | Co znaczy |
|---|---|
| podają swoją firmę jako importera + dają deklarację | 🟢 **jesteś dystrybutorem**, obowiązki lekkie |
| „towar jedzie z Chin na Twoje zamówienie" | 🔴 to **dropshipping — importerem jesteś Ty**, nic nie zyskałeś |
| „mamy CE" i nic więcej | 🔴 nie mają dokumentów, tylko znaczek |
| brak EN 18031 | 🔴 ten sam problem co u Liliwise, tylko przesunięty o jedno ogniwo |

➡️ **Cała wartość kupowania w UE polega na tym, że ktoś PRZED Tobą wziął rolę importera.**
Jeśli nie wziął, płacisz drożej i masz dokładnie to samo ryzyko.

## 4. 🟢 Druga droga, moim zdaniem lepsza: marka europejska

Zamiast szukać pośrednika do chińskiego towaru — kupować markę, która **sama** jest
producentem w UE i ma dokumentację, wsparcie i otwarte API.

| Marka | Kraj | Dlaczego warta uwagi |
|---|---|---|
| 🟢 **Tedee** | **Polska** (Warszawa) | **udokumentowane lokalne API mostka** ([docs.tedee.com/bridge-api](https://docs.tedee.com/bridge-api)), **oficjalna integracja w Home Assistant komunikująca się lokalnie**, Matter w modelach GO i GO 2. Polski producent = polskie wsparcie i realny regres |
| 🟢 **LOQED** | Holandia | sprawdzone w bazie SIG: `LOQED Touch Smart Lock`, model 2.0 — [karta](https://qualification.bluetooth.com/ListingDetails/195261). Dostępny przez Shelly za 1 130 zł |
| 🟡 **Nuki** | Austria | lokalne HTTP API, Matter, szeroko wspierany |
| 🟡 **ABUS, Winkhaus, Assa Abloy** | DE / DE / SE | klasyczni producenci zamków, są w rejestrze CSA. Segment projektowy, drożej |

### 🔴 Ale uczciwie o różnicy formatu

**Tedee, LOQED i Nuki to nakładki na wkładkę**, a nie klamki z czytnikiem linii papilarnych.
Montują się od wewnątrz na istniejącej wkładce — nie wymagają wymiany zamka ani frezowania
([klamki-zamek-wpuszczany-jaki-rozmiar-do-polski.md](klamki-zamek-wpuszczany-jaki-rozmiar-do-polski.md)).

**Klamek smart w formacie europejskim od marek z UE praktycznie nie ma w przystępnej cenie.**
To jest realna luka rynkowa — i jednocześnie powód, dla którego wszyscy jadą do Chin.

## 5. [DEDUKCJA] Co bym zrobił na start

1. **Pierwsze wdrożenia: Tedee.** Polski producent, lokalne API, zero roli importera po naszej
   stronie, wsparcie po polsku. Droższe od chińskiego, ale **cena obejmuje ryzyko, którego
   nie musimy wyceniać**
2. **Równolegle** wysłać pięć pytań z sekcji 3 do HAT Pol i Spreest — jeśli któryś okaże się
   prawdziwym importerem z dokumentami, mamy tańszą ścieżkę na klamki
3. **Liliwise zostawić jako opcję na później** — dopiero gdy skala uzasadni rolę importera

## 6. Jedno zdanie

> **Dystrybutor w UE ma sens tylko wtedy, gdy KTOŚ przed Tobą naprawdę wziął rolę importera —
> a jeśli szukasz pewności bez sprawdzania, prostszą odpowiedzią jest polski Tedee
> z udokumentowanym lokalnym API niż chińska klamka przez pośrednika.**

---

## Źródła

- [Spreest — dane firmy](https://spreest.pl/pl/pages/kontakt) · [Altum Polska — TTLock SmartLock](https://altumpolska.pl/c/ttlock-smartlock) · [HAT Pol — TTLock](https://hatpol.pl/ttlock-zamki-smart-lock-c-32_85.html) · [Keylock](https://keylock.pl/ttlock/)
- [Tedee Bridge API](https://docs.tedee.com/bridge-api) · [Home Assistant — integracja Tedee](https://www.home-assistant.io/integrations/tedee/) · [Tedee — integracje smart home](https://tedee.com/smart-home-integrations/)
- [Bluetooth Qualified Product Database — LOQED Touch Smart Lock](https://qualification.bluetooth.com/ListingDetails/195261)
