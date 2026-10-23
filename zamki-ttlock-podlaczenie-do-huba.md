# Zamki TTLock — jak podłączyć klamkę do huba

Spisane 2026-09-16. Źródła: dokumentacja TTLock Open Platform (User Guide: How to get started, eKey,
Passcode, Gateway, On Premise, Third-party device integration; FAQ; Cloud API), pomoc TTLock,
GitHub (ttlock, roquerodrigo, kind3r, jbergler), PyPI, pub.dev, matter-survey.org, fora Home Assistant
i Homey, sklepy ttlock.eu i Blusafe, pomoc Nuki. Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md) (test odcięcia WAN) ·
[wyjasnienie-polnoc-poludnie.md](wyjasnienie-polnoc-poludnie.md) ·
[local-first.md](local-first.md) ·
[jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md)

---

## 🔴 KOREKTA 2026-09-16 — TTLock ma oficjalne drogi lokalne

Pierwsza wersja tej notatki (spisana tego samego dnia) twierdziła:

- „nie znalazłem udokumentowanego lokalnego API" — oficjalnie hub steruje klamką tylko przez chmurę
- lokalny Bluetooth z huba to wyłącznie projekt kind3r (reset klamki, GPL, porzucony) → „do produktu nie"

**Co było niepełne.** W dokumentacji TTLock (menu *User Guide*, link od usera) są dwie
oficjalne drogi lokalne, których wtedy nie znalazłem:

1. **Third-party device integration** — oficjalny protokół Bluetooth: otwórz, zamknij, stan → sekcja 4.3
2. **On Premise** — płatny pakiet: własny serwer zamiast chmury TTLock → sekcja 4.5

Do tego nowsza biblioteka **ttlock-ble** → sekcja 4.4 (później odrzucona decyzją usera — sekcja 5).

Zdanie o standardowej bramce pozostaje prawdziwe: w zwykłej konfiguracji łączy klamkę z chmurą TTLock.

---

## 1. Kto jest kim

- **TTLock** (Hangzhou Sciener Smart Technology) dostarcza producentom zamków elektronikę
  i oprogramowanie: „We provide PCBA and softwares for lock manufactures and end users" [DOK — GitHub ttlock]
- **[DEDUKCJA]** TTLock jest dla zamków tym, czym Tuya dla gniazdek: jedna platforma pod wieloma markami
- **Liliwise** to nie hurtownia, tylko producent zamków z Guangzhou [DOK — profil firmy].
  Klamki działają na platformie TTLock (informacja od usera)

---

## 2. Jak to jest zbudowane

```
telefon (apka TTLock albo Twoja z SDK) ──Bluetooth──┐
                                                    ├── klamka (PIN, odcisk, karta, klucz kodów)
bramka TTLock ─────────────────────────Bluetooth────┘
     │ Wi-Fi
     └── internet ── chmura TTLock ── Cloud API ── Twój hub
```

- „TTLock bluetooth lock can't connect to the Internet by itself" [DOK — Gateway]
- Cloud API rozmawia z serwerem TTLock, a App SDK z zamkiem po Bluetooth: „All bluetooth
  related protocols are encapsulated in the APP SDK" [DOK — eKey]
- SDK to natywne biblioteki na **Android, iOS i Flutter** [DOK — How to get started]
- konto deweloperskie służy tylko do zgłaszania aplikacji; aplikację **zatwierdzają ręcznie
  w dni robocze**, potem widać `client_id` i `client_secret` [DOK]
- **brak kont testowych i sandboxa** — „We don't provide test accounts or sandbox environments" [DOK]
- dodanie zamka: administrator inicjuje zamek apką, potem wywołuje API chmury [DOK]
- eKey: użytkownik synchronizuje dane klucza z serwera do apki, potem otwiera [DOK]
- klucze klamki: `lockId` i `lockData` zwraca chmura po inicjalizacji; `lockData` to klucz
  z zaszyfrowanymi danymi zamka [DOK — FAQ]
- 🔴 „an uninitialized lock can be initialized by others, which poses a security risk" [DOK — FAQ]
  → klamki nie zostawia się u klienta niezainicjalizowanej

**Bramka** [DOK — Gateway]:
- sama wyszukuje zamki należące do tego samego konta; **brak limitu zamków na bramkę**
- z daleka: otwórz, zamknij, stan, kody, karty, odciski, bateria, zegar
- operacja zdalna trwa kilka sekund, limit 30 s; jedno połączenie Bluetooth naraz;
  dotknięcie klawiatury przerywa operację

---

## 3. Co działa bez internetu

🎯 **Klamka nigdy nie ma internetu — więc wszystko, co robi sama, działa zawsze.**

| Co | Bez internetu | Skąd wiadomo |
|---|---|---|
| PIN, odcisk, karta, klucz mechaniczny | ✅ | **[DEDUKCJA]** zamek sprawdza je sam |
| **kody losowe** już wydane | ✅ | zamek sam sprawdza kod algorytmem [DOK] |
| wygenerowanie nowego kodu losowego | ⚠️ potrzebne API chmury, **ale nie bramka** i nie połączenie z zamkiem | [DOK] |
| kod własny (swoje cyfry) — dopisanie | ⚠️ telefon przy zamku po Bluetooth albo bramka | [DOK] |
| usunięcie lub zmiana kodu | ⚠️ telefon przy zamku albo bramka | [DOK] |
| telefon przy drzwiach po Bluetooth | ✅ z eKey zsynchronizowanym wcześniej | [DOK] |
| otwarcie z daleka, powiadomienia | ❌ bramka + internet | [DOK] |

### Kody losowe i własne [DOK — Passcode]

**Losowe:** przy inicjalizacji zamek i chmura dostają **ten sam algorytm i klucz kodów**.
Kod pobierasz z Cloud API, a zamek sprawdza go sam — „do not rely on gateways".
- 6–9 cyfr, nie da się ich wybrać, ważność z dokładnością do godziny
- **jednorazowy** — raz, w ciągu 6 h od startu
- **stały** i **czasowy** — trzeba użyć co najmniej raz w ciągu 24 h od startu, inaczej zostaje zawieszony
- **cykliczny** i **kasujący** (unieważnia wszystkie użyte kody)

**Własne:** 4–9 cyfr, do **150** w pamięci zamku, ważność z dokładnością do minuty.
Dopisujesz telefonem przy zamku albo przez bramkę.

**Reset wszystkich kodów** — tylko przez SDK przy zamku, nie przez bramkę; powstaje nowe
`lockData`, które trzeba wysłać do chmury.

**[DEDUKCJA] Pomysł na awarię internetu:** hub pobiera z wyprzedzeniem pulę kodów z przyszłym
startem i trzyma ją na zapas. **[DO SPRAWDZENIA]** jak daleko w przód API przyjmuje start.

---

## 4. Drogi do huba i apki

| Droga | Lokalnie | Oficjalnie | Co lokalnie |
|---|---|---|---|
| 4.1 Bramka + chmura | ❌ | ✅ | — |
| 4.2 Bramka G6 Matter | 🟡 do sprawdzenia | ✅ | otwórz, zamknij, stan przez Matter |
| **4.3 Hub jako „urządzenie zewnętrzne Bluetooth"** | ✅ | ✅ | **otwórz, zamknij, stan** |
| 4.4 Biblioteki nieoficjalne | — | ❌ | 🚫 **odrzucone decyzją usera** (sekcja 5) |
| **4.5 On-Premise — własny serwer** | ✅ | ✅ płatne | wszystko, łącznie z generowaniem kodów |
| 4.6 Telefon z oficjalnym SDK | ✅ przy drzwiach | ✅ | prawie wszystko przy drzwiach |
| 4.7 Inny zamek (Nuki 4. gen.) | ✅ | ✅ | Matter over Thread |

### 4.1 Bramka i chmura TTLock

Tak działa integracja Home Assistanta `hass-ttlock` [DOK]: rejestracja aplikacji OAuth
na `open.ttlock.com` (zatwierdzenie „a few days"), stan przez webhooki, otwieranie,
bateria, historia, PIN-y. Zdalne otwieranie trzeba włączyć w apce, inaczej błąd `-4043` [DOK].
Strona WWW nie ma Bluetooth, więc zawsze idzie: API → chmura → bramka → zamek [DOK — FAQ].
❌ Bez internetu hub nie steruje.

### 4.2 Bramka G6 Matter

- **Matter over Wi-Fi**; Apple Home, Google Home, Alexa [DOK — Blusafe]
- 🔴 „One lock per gateway in Matter mode" [DOK — Blusafe]; 49 € (ttlock.eu) – 83,95 € (Blusafe)
- „you MUST use the TTLock app; you CANNOT simply scan the QR code" [DOK — forum HA, 28.02.2026]
- matter-survey.org: Sciener ma jedno urządzenie, **SN8102 WiFi Module**, klastry On/Off
  i Door Lock, widziane 14 razy [DOK]
- opinie: HA (02.2026) „IT IS NOT WORTH IT"; Homey (06–07.2026) otwieranie i zamykanie
  działa, stan z opóźnieniem, wymuszone auto-zamykanie naprawione w `6.0.0.250717` [DOK]
- ⚠️ pakiet On-Premise **nie obsługuje G6** [DOK — On Premise]
- nikt nie opisał testu odcięcia internetu **[DO SPRAWDZENIA]**

#### 🔴 Dwa tryby bramki — to źródło nieporozumień

| | **Tryb TTLock (chmurowy)** | **Tryb Matter (tylko G6)** |
|---|---|---|
| Droga polecenia | hub → **internet → chmura TTLock** → bramka → klamka [DOK] | hub → Wi-Fi w domu → bramka → klamka |
| Bez internetu | ❌ nie działa | 🟡 powinno działać — **[DO SPRAWDZENIA]** |
| Zakres funkcji | **szeroki**: otwórz, zamknij, stan, **kody, karty, odciski**, bateria, odczyt i kalibracja czasu [DOK — przewodnik Gateway] | **wąski**: otwórz, zamknij, stan (klastry Door Lock i On/Off) [DOK — matter-survey] |
| Liczba zamków | bez limitu na bramkę [DOK] | **jedna klamka na bramkę** [DOK] |

**Bramka nie ma lokalnego API.** FAQ mówi wprost o sterowaniu z systemu webowego:
„H5 page → Request API → Cloud server → Gateway (via Bluetooth) → Lock" [DOK].

⚠️ **Bramka nie rozgłasza własnego Wi-Fi** — dołącza do sieci domowej jako klient
**[DEDUKCJA]**; hub rozmawia z nią przez router, a nie przez osobną sieć bramki.

🎯 **Jedyny wariant „szeroki zakres + lokalnie" to On-Premise (4.5)** — wtedy rolę chmury
pełni Twój serwer, a bramka łączy się z nim **[DEDUKCJA]**.

### 4.3 🟢 Hub jako „urządzenie zewnętrzne Bluetooth" — oficjalny protokół

Strona *Third-party Bluetooth Device Integration* [DOK]:

> „If the third-party device has an Android or Apple system, it can be supported by our
> provided SDK. However, many devices do not have an operating system and require us to open
> some protocols for control. This solution is designed to meet this usage scenario, allowing
> the door lock to be controlled by a Bluetooth module (without an operating system) to unlock
> or lock and query the lock status."

**Funkcje:** otwórz, zamknij, stan zamka. Stan widać też **w rozgłoszeniach Bluetooth** zamka,
bez łączenia się [DOK].

🔴 **Warunek — układ w zamku** [DOK]: `SN534-15-1`, `SN534-16-1`, `SN503-18-1`, `SN8534-3-1`,
`SN8534-1-1`, `SN8503-2-1`. Inny układ = zapytać producenta o zamek z właściwym układem albo
o aktualizację firmware'u.

**Jak to przebiega** [DOK]:
1. apka daje zamkowi zgodę na dodanie urządzenia; zamek zaczyna to rozgłaszać
2. urządzenie się dodaje i dostaje stały token oraz własny klucz szyfrowania
3. przy każdej operacji pobiera token tymczasowy i wysyła polecenie
4. dokumentacja opisuje format ramek, sumę kontrolną, kody błędów i ma demo

**[DEDUKCJA]** Hub z Bluetooth albo ESP32 przy drzwiach powinien móc grać rolę takiego
urządzenia. Polecenia idą wprost do zamku, więc po dodaniu chmura nie jest potrzebna.

⚠️ Kody, odciski i karty nadal zarządza się apką albo przez chmurę.

### 4.4 Biblioteki nieoficjalne — 🚫 odrzucone

🚫 **2026-09-16, decyzja usera: tylko oficjalne drogi i czysta współpraca z producentem.**
Opis zostaje, żeby było wiadomo, co i dlaczego odpadło.

**`ttlock-ble`** [DOK — PyPI, GitHub]: Python ≥ 3.14, `bleak`, licencja MIT, wydania od 05.2026
(ostatnie `0.3.2` z 06.09.2026). Klucze z chmury raz, potem Bluetooth bez internetu.
„Unofficial… reverse-engineered. Either side can change and break this SDK without notice".

**`ttlock-sdk-js` (kind3r)** [DOK]: Node.js, tylko zamki V3, wymaga resetu klamki,
licencja GPL-3.0, autor „unable to maintain the project", Bluetooth „very unstable".

### 4.5 On-Premise — własny serwer zamiast chmury TTLock (płatne)

[DOK — On Premise]:
- „All data is stored on the third-party software's own servers"
- „Passwords and electronic keys are also generated on the third-party software's own servers"
- „All operations do not across the our server" — **bramki łączą się z Twoim serwerem**
- składniki: pakiet JAR (generuje kody i klucze; obowiązkowy), SDK Android/iOS,
  **gateway server package**, DLL do koderów kart
- trzy warianty: integracja składników we własny system / gotowa apka, web i serwer / kod źródłowy
- serwer: **Linux, 6 GB+ RAM, 100 GB+ dysku, Tomcat 8.5, MySQL 5.6, Redis, JDK 1.8**
- nie wszystko z apki TTLock — m.in. **bez G6 Matter**
- wsparcie darmowe przez miesiąc, potem abonament roczny; wycena przez WeChat `ttlockdev`

**[DEDUKCJA]** Pełna niezależność od TTLock, łącznie z generowaniem kodów bez internetu.
Ale to rozwiązanie dla platform, z ciężkim i starym stosem — temat na etap skali.
**[DO SPRAWDZENIA]** cena, licencja, czy sam JAR ruszy na ARM w hubie.

### 4.6 Telefon z oficjalnym SDK

- `ttlock_flutter` — repozytorium w organizacji `ttlock` na GitHubie; na pub.dev „unverified
  uploader", wersja 0.3.8 sprzed ok. 13 miesięcy [DOK]
- `TTLock.controlLock(lockData, TTControlAction.unlock)` [DOK]
- `lockData` pochodzi z dodania zamka przez SDK albo z pobrania eKey przez API [DOK]
- ⚠️ działa w telefonie przy drzwiach, nie w hubie

#### Pełna lista operacji po Bluetooth — „Lock Interfaces", 77 pozycji [DOK]

Źródło: `appSdkV3/androidSdkDemoV3/lockApisEn.html`. **Wszystko to jedzie po Bluetooth**,
czyli działa przy drzwiach także bez internetu (potrzebny jest `lockData` pobrany wcześniej).

| Grupa | Operacje |
|---|---|
| **Zamek** | inicjalizacja zamka · reset zamka · **reset eKey** · otwórz/zamknij · stan baterii · **log operacji** (nowe albo wszystkie) · ustaw i odczytaj czas zamka · konfiguracja zamka |
| **Kody** | **utwórz kod własny (6–9 cyfr)** · zmień · usuń · **skasuj wszystkie** · **pobierz wszystkie ważne kody z zamku** · zmień kod administratora |
| **Karty** | dodaj · zmień okres ważności · pobierz listę · usuń · wyczyść wszystkie |
| **Odciski palców** | dodaj · pobierz listę · usuń · wyczyść wszystkie · zmień okres ważności |
| **Twarz** | dodaj · zmień okres · usuń · wyczyść · dodaj dane cech · pobierz listę |
| **Żyły dłoni** | dodaj · zmień okres · usuń · wyczyść · pobierz listę |
| **Piloty** | dodaj · zmień · usuń · wyczyść |
| **Tryb przejścia** (zamek zostaje otwarty) | odczytaj · ustaw · usuń · wyczyść |
| **Ustawienia** | automatyczne zamykanie · wyciszenie i głośność · czułość rozpoznawania twarzy, dłoni i kodu QR · alarm niedomkniętych drzwi · tryb oszczędzania energii · **alarm nieudanych prób** · przełącznik zdalnego otwierania |
| **Akcesoria** | czujnik drzwi (dodaj, usuń) · poziom baterii akcesoriów · MAC bramki |
| **Zamek Wi-Fi** | skan sieci · konfiguracja Wi-Fi · **konfiguracja serwera** · konfiguracja IP · odczyt danych Wi-Fi |
| **Hotel / windy** | dane hotelowe · sektor karty · tryb sterowania windą · przypisane piętra |

🔴 **Uwagi:**
- część operacji zmienia `lockData` i wtedy trzeba **zsynchronizować chmurę** („you need to call
  Update lock data API") [DOK] — bez internetu synchronizacja czeka w kolejce **[DEDUKCJA]**
- „**config server**" przy zamkach Wi-Fi to ciekawostka pod On-Premise (4.5) **[DO SPRAWDZENIA]**,
  czy pozwala wskazać własny serwer

### 4.6a Zamki w wersji „BLE + Wi-Fi" (Liliwise często daje taki wybór)

[DOK — przewodnik *Unlock via network (WiFi lock)*]:

- „WiFi lock can connect directly to WiFi network, **gateway is no longer needed**"
- po konfiguracji zamek łączy się z **serwerem chmurowym TTLock**, a Ty sterujesz nim
  przez Cloud API
- tryb uśpienia: domyślnie wyłączony, zamek trzyma stałe połączenie TCP z serwerem;
  po włączeniu łączy się tylko wtedy, gdy ma coś do zgłoszenia
- przy włączonym uśpieniu **nie da się wysłać polecenia** — błąd `-3035`; polecenie można
  zakolejkować i wykona się, gdy zamek się obudzi
- **brak jakiegokolwiek lokalnego API i dostępu po LAN** — wszystko idzie przez chmurę

🔴 **Wniosek: „Wi-Fi" w tych zamkach nie znaczy „pogada z Twoim hubem po sieci".**
Znaczy „sam dodzwoni się do chmury TTLock, bez bramki". Do local-first to nic nie daje —
tam nadal liczy się droga po Bluetooth (4.3) albo Matter (4.2) **[DEDUKCJA]**.

### 4.7 Inny zamek — Nuki 4. generacji

Matter over Thread, bez mostka Nuki, sterowanie lokalne [DOK — pomoc Nuki]. Starsze Nuki:
oficjalne lokalne HTTP API mostka [DOK]. ⚠️ nakładka na wkładkę, nie klamka; wymaga Thread
Border Routera, którego RPi5 nie ma.

---

## 5. Wymogi usera i architektura #decyzja

**Wymóg 1 (2026-09-16):** chmura TTLock może być potrzebna do konfiguracji; funkcje klamki
mają być w naszej apce i działać, gdy internet padnie.

**Wymóg 2 (2026-09-16):** **żadnych nieoficjalnych dróg — tylko oficjalna współpraca
z producentem.** Biblioteki z 4.4 odpadają.

### Trzy pytania, które wracają

**1. Czy muszę kupić bramkę TTLock (G2, G4, G6)?**
🟢 **Nie — jeśli układ w klamce jest na liście z 4.3.** Wtedy mostkiem Bluetooth jest
**Twój hub albo ESP32 przy drzwiach**, a polecenia idą wprost do zamka. Bramka jest potrzebna
tylko do dwóch rzeczy: sterowania **spoza domu** przez chmurę i zdalnego zarządzania kodami,
kartami i odciskami (4.1). Jeśli układ nie jest na liście — bramka albo inny zamek.

**2. Czy bramka wymusza aplikację TTLock?**
⚠️ **Do konfiguracji — tak.** Bramkę dodaje się w apce TTLock i sama znajduje zamki
**należące do tego samego konta** [DOK]; G6 też najpierw konfiguruje się w apce, dopiero
potem wystawia parowanie Matter (4.2). Nawet dodanie „urządzenia zewnętrznego" z 4.3 wymaga
jednorazowej zgody wydanej przez apkę.
🟢 **Ale klient nie musi jej używać na co dzień:** Twoja apka może stać na oficjalnym SDK,
a konta użytkowników zakładasz przez API (`user/register`) — sekcja 6. Platforma TTLock
zostaje pod spodem, apka producenta nie musi być na telefonie klienta **[DEDUKCJA]**.

**3. Czy wystarczy ESP32 z ESPHome?**
🔴 **ESPHome nie ma obsługi TTLock** — nie znalazłem gotowego komponentu. ESP32 może być
mostkiem, ale **firmware trzeba napisać samemu** według protokołu z 4.3 (ramki, tokeny,
szyfrowanie — producent to publikuje i daje demo). To wciąż droga oficjalna, tylko robota
po naszej stronie **[DEDUKCJA]**.

**[DEDUKCJA] Analogia do Zigbee, o którą pytał user:** bramka TTLock to odpowiednik bramki
Tuya, a protokół dla urządzeń zewnętrznych (4.3) to odpowiednik własnego koordynatora
z Zigbee2MQTT — z tą różnicą, że daje tylko **otwórz, zamknij, stan**, a nie pełne
zarządzanie zamkiem.

### Która warstwa gdzie działa — mapa dla zespołu

🔴 **TTLock nie ma SDK na Linuksa ani na Node.js.** To jednak **nie blokuje huba** — SDK jest
tylko wygodną nakładką na telefon, a nie jedyną drogą.

| Warstwa | Gdzie działa | Co daje | Czy hub może |
|---|---|---|---|
| **Cloud API (REST/HTTPS)** | gdziekolwiek, także **hub w Node.js** | konta, eKey i `lockData`, kody losowe, zdalne otwieranie (przez bramkę albo zamek Wi-Fi), rejestry, callbacki | ✅ **tak, bez żadnego SDK** |
| **App SDK** (Android, iOS, Flutter) | **tylko telefon** | wszystko po Bluetooth przy drzwiach: inicjalizacja zamka, otwieranie, kody, odciski, karty, logi, **konfiguracja bramki** | ❌ nie na Linuksie |
| **Protokół dla urządzeń zewnętrznych** (4.3) | **hub z Bluetooth albo ESP32** | otwórz, zamknij, stan | ✅ tak — implementacja własna wg dokumentacji |
| **Matter przez G6** (4.2) | hub jako kontroler Matter | otwórz, zamknij, stan — lokalnie | ✅ tak |
| **On-Premise** (4.5) | **Twój serwer** (Linux, Java) | wszystko lokalnie, łącznie z generowaniem kodów; bramki łączą się z Tobą | ✅ tak, płatne |

**[DEDUKCJA] Podział ról w naszym produkcie:**
- **telefon (nasza apka + SDK)** — pierwsza konfiguracja zamka, odciski i karty, awaryjne
  otwieranie przy drzwiach bez sieci
- **hub** — codzienne otwieranie z domu (Bluetooth albo Matter), automatyzacje, powiadomienia;
  w tle REST do chmury TTLock po klucze i kody
- **chmura TTLock** — magazyn kluczy, generator kodów losowych, otwieranie spoza domu
- **nasz własny serwer** — zdalny dostęp do huba; z TTLockiem nie ma nic wspólnego

```
KONFIGURACJA — z internetem, raz
  hub ── Cloud API TTLock ── dodanie klamki, eKey, kody
  apka ── zgoda na dodanie urządzenia zewnętrznego ── hub / ESP32 dodany do klamki

CODZIENNIE — także bez internetu
  apka w domu ── hub ──Bluetooth, protokół 4.3── klamka   otwórz · zamknij · stan
  telefon przy drzwiach ──SDK, Bluetooth── klamka          pełne funkcje
  klamka sama                                              PIN · odcisk · karta · kody losowe

TYLKO Z INTERNETEM
  nowe kody z daleka · sterowanie spoza domu · powiadomienia
```

**[DEDUKCJA]** Oba wymogi da się spełnić, jeśli układ w klamce jest na liście z 4.3.
Jeśli nie jest: klamka Liliwise z właściwym układem albo aktualizacja firmware'u;
w ostateczności inny zamek (4.7).

### Wariant B — bramka G6 jako mostek Bluetooth ↔ Wi-Fi, klient bez apki TTLock

Pytanie usera (2026-09-18): kupić G6, żeby gadał z klamką po Bluetooth, a z hubem po Wi-Fi,
tak żeby **klient nie pobierał niczego od TTLocka** i używał tylko naszej apki (na ich SDK).

```
klamka ──Bluetooth── G6 ──Wi-Fi, Matter── nasz hub (kontroler Matter) ──► nasza apka
                                    telefon przy drzwiach ──SDK, Bluetooth──► klamka
```

**Co się zgadza [DOK]:** dokładnie do tego G6 służy — Matter over Wi-Fi, a sterowanie
Matter jest lokalne. Klient na co dzień nie potrzebuje apki TTLock.

**Co trzeba dowieźć albo sprawdzić:**

| Punkt | Stan |
|---|---|
| hub musi umieć być **kontrolerem Matter** | praca po naszej stronie |
| **jedna klamka na bramkę w trybie Matter** | [DOK — Blusafe]; 49–84 € za drzwi |
| konfiguracja bramki | 🟢 **z własnej apki, bez apki TTLock** — patrz niżej [DOK] |
| 🔴 **kod parowania Matter** | wg relacji z forum HA generuje się go **w apce TTLock** („Link to Third-Party App"); **[DO SPRAWDZENIA]**, czy da się to zrobić z własnej apki przez SDK |
| 🔴 **test odcięcia internetu** | nikt tego nie opisał — **[DO SPRAWDZENIA]** na jednej sztuce przed jakąkolwiek obietnicą |
| zakres funkcji przez Matter | klastry **Door Lock** i On/Off [DOK — matter-survey]; kody, karty i odciski nadal przez SDK przy drzwiach albo przez chmurę |

**[DEDUKCJA] Porównanie z wariantem z 4.3 (hub jako urządzenie zewnętrzne):**

| | **G6 + Matter** | **Hub/ESP32 jako urządzenie zewnętrzne** |
|---|---|---|
| Koszt na drzwi | 49–84 € | 0 zł (hub) albo ~30 zł (ESP32) |
| Warunek | żadnych wymagań co do układu w zamku | **układ z listy 4.3** |
| Praca huba | kontroler Matter | własny firmware wg protokołu |
| Funkcje lokalnie | otwórz, zamknij, stan | otwórz, zamknij, stan |
| Dojrzałość | firmware młody, opinie mieszane | protokół opublikowany, ale piszesz sam |

### 🟢 Bramkę konfigurujesz z WŁASNEJ apki — klient nie widzi apki TTLock [DOK]

Znalezione przez usera 2026-09-18 w SDK V3 (przykład dla Androida, strona `gatewayEn.html`).
Przebieg w kodzie:

1. `GatewayClient.getDefault().startScanGateway(new ScanGatewayCallback())` — szukanie bramki po Bluetooth
2. `GatewayClient.getDefault().connectGateway(item, new ConnectCallback())` — połączenie
3. `GatewayClient.getDefault().initGateway(configureGatewayInfo, new InitGatewayCallback())`
   z polami: **`uid`**, `userPwd`, **`ssid`**, `wifiPwd`, `plugName`
4. potem dwa wywołania Cloud API: *Query the init status of the gateway* i *Upload detail info of gateway*

Zgadza się z przewodnikiem: „You may also develop your own APP base on our APP SDK to
initialize the gateway" [DOK].

⚠️ **Doprecyzowanie — to nie jest „własna brama".** To konfiguracja **ich bramki** z Twojej
apki. Bramka po inicjalizacji i tak **rejestruje się w chmurze TTLock** i jest przypisana do
konta (`uid`) [DOK — przewodnik Gateway].

**Jeśli chcesz naprawdę własne urządzenie mostkujące, są dwie oficjalne drogi:**
- **protokół dla urządzeń zewnętrznych** (4.3) — hub albo ESP32 rozmawia wprost z klamką
- **On-Premise** (4.5) — pakiet *gateway server*: **ich bramki łączą się z Twoim serwerem**,
  nie z chmurą TTLock

🔴 **Nadal otwarte:** na stronie z przykładem kodu **nie ma ani słowa o Matterze ani o G6** —
czyli pytanie, czy kod parowania Matter da się wygenerować z własnej apki, zostaje
**[DO SPRAWDZENIA]** u producenta.

---

## 6. Ile kosztuje TTLock po stronie serwerów

| Element | Koszt | Źródło |
|---|---|---|
| Klamka | jednorazowo, u producenta | — |
| SDK (Android, iOS, Flutter) | **za darmo** — „Develop your own platform based on our SDKs, which is free" | [DOK] |
| Open Platform — wersja podstawowa | **za darmo**, do **30 000 wywołań API miesięcznie na aplikację**; po limicie wywołania są odrzucane i przychodzi e-mail | [DOK — FAQ] |
| Wyższa wersja Open Platform | płatna: „Developer Account --- Basic Information --- Developer Version --- pay and upgrade"; **cennika nie ma w dokumentacji** | [DOK] · cena [DO SPRAWDZENIA] |
| Protokół dla urządzeń zewnętrznych (4.3) | dokumentacja nie wspomina o opłacie | [DOK — brak wzmianki] |
| Bramka | tylko sprzęt (G6: 49–84 €) | [DOK] |
| On-Premise | płatne, wycena indywidualna | [DOK] |
| Apka TTLock | podstawowe funkcje za darmo; SMS z systemu wymagają pakietu | [DOK — pomoc TTLock] |

Limity kont [DOK — FAQ]: zamków na konto — bez limitu; administratorów z uprawnieniami — bez
limitu; liczba kodów, kart i odcisków zależy od modelu zamka.

### Co to znaczy dla modelu biznesowego [DEDUKCJA]

- 🟢 **TTLock nie bierze opłaty za to, że klient używa klamki.** Płaci się dopiero za przekroczenie
  limitu wywołań API albo za On-Premise
- limit liczy się **na aplikację** [DOK], a wszyscy klienci idą przez jedną aplikację firmy —
  **30 000 wywołań to limit całej firmy**
- w architekturze z sekcji 5 chmura służy do konfiguracji i nowych kodów, więc wywołań jest mało.
  Przykład rachunkowy: 1000 klientów po 30 wywołań = 30 000
- 🔴 **nie odpytywać chmury w pętli**: hub pytający co minutę to 43 200 wywołań miesięcznie —
  **jeden klient przekracza limit firmy**. Stan klamki hub bierze lokalnie z rozgłoszeń Bluetooth
  (4.3); przy bramce zdarzenia przychodzą na Callback URL [DOK]
- klient nie musi znać apki TTLock: konta można zakładać przez API (`user/register`),
  a klamki dodawać w swojej apce przez SDK i `lock/initialize` [DOK]
- 🔴 **chmura czy On-Premise to decyzja na lata**: „the lockdata generated by the open platform
  SDK cannot be used in the on-premise server JAR package" [DOK — FAQ] → przejście później
  = ponowna inicjalizacja każdej klamki u każdego klienta

---

### 🔴 Własne logo na klamce (OEM/ODM u Liliwise) — co to naprawdę kosztuje

Liliwise reklamuje się jako OEM/ODM, więc pokusa jest naturalna. Rachunek wygląda tak:

| Koszt | Szczegół |
|---|---|
| **Bluetooth SIG** | „Every company selling, branding, manufacturing, or representing a product as its own must pay a Product Qualification Fee"; „a new Declaration ID is required if you are **rebranding** an already qualified design"; „Your supplier or other member companies **cannot qualify your products on your behalf**" [DOK]. **12 000 $** (Adopter) albo **8 000 $** przy członkostwie 3 500 $/rok — **raz za projekt**, nie za model ani sztukę |
| **Rola producenta w UE** | dokumentacja techniczna, badania, deklaracja zgodności na siebie — [jak-kupowac-sprzet-bez-loterii.md](jak-kupowac-sprzet-bez-loterii.md), sekcja 5.3 |
| **RED i cyberbezpieczeństwo** | urządzenie radiowe → dyrektywa radiowa i normy EN 18031 od 1.08.2025 |
| **CRA** | zgłaszanie luk od 11.09.2026; poprawki przez okres wsparcia, co do zasady 5 lat, od 11.12.2027 |
| **BDO** | sprzęt elektroniczny **i baterie** (klamki są bateryjne) oraz opakowania |
| **Klasa ryzyka** | zamek **chroni mienie** → klasa 3 wg [strategia-modbus-rs485.md](strategia-modbus-rs485.md), sekcja 9 |

🟢 **Dopóki sprzedajesz cudzy produkt bez swojego logo, opłata Bluetooth SIG Cię nie dotyczy**
[DOK] — wystarczy sprawdzić, czy model producenta jest w bazie kwalifikowanych produktów.

**[DEDUKCJA] Wniosek: nie brandować klamek.** Sprzedawaj je pod marką producenta, a własną
markę trzymaj tam, gdzie i tak jest Twoja wartość: **hub, apka i usługa**
([ocena-szczera-modelu.md](ocena-szczera-modelu.md), faza 2).

**[DO SPRAWDZENIA]** gdyby jednak kiedyś: czy TTLock ma program własnej marki dla aplikacji
i czy Liliwise poda numer kwalifikacji Bluetooth (QDID) swojego projektu.

---

## 7. Pierwsze kroki

1. **Zapytać Liliwise:** jaki układ TTLock jest w klamce i czy firmware obsługuje
   „third-party Bluetooth device" (lista w 4.3)
2. **Założyć konto deweloperskie** w TTLock Open Platform — zatwierdzenie ręczne w dni robocze;
   w koncie sprawdzić ceny wyższych wersji. Sandboxa nie ma — testy na prawdziwej klamce
3. jedna klamka do testu: dodać hub jako urządzenie zewnętrzne → **odciąć internet** →
   otworzyć i zamknąć z huba
4. ~~równolegle `ttlock-ble` jako plan B~~ — odpada, decyzja z sekcji 5
5. On-Premise — zapytanie o cenę dopiero przy skali, ale **przed** masowym montażem (sekcja 6)

---

## 8. Otwarte pytania #do-sprawdzenia

- jaki układ TTLock mają klamki Liliwise
- 4.3: w którym miejscu apki TTLock daje się zgodę na dodanie urządzenia zewnętrznego; ile urządzeń na zamek
- 4.3: czy hub z Linuksem i Bluetooth dogada się z zamkiem tak samo jak moduł bez systemu
- zasięg Bluetooth z miejsca, gdzie stoi hub — czy potrzebny ESP32 przy drzwiach
- kody losowe: jak daleko w przód API przyjmuje czas startu (pula na awarię)
- cena wyższych wersji Open Platform i ich limity wywołań
- ile wywołań API realnie zużywa jeden klient miesięcznie
- G6: test odcięcia internetu; zarządzanie PIN-ami przez Matter
- On-Premise: cena, licencja, wymagania samego JAR
- `ttlock_flutter`: czy wymaga zatwierdzonej aplikacji w Open Platform

---

## 9. Jedno zdanie

> **Klamka TTLock jest z natury lokalna — nigdy nie ma internetu. Oficjalnie hub może nią
> sterować po Bluetooth protokołem dla urządzeń zewnętrznych, jeśli układ w klamce jest na liście
> TTLock; chmura służy do konfiguracji i nowych kodów, a do 30 000 wywołań API miesięcznie
> jest za darmo.**

---

## Źródła

**TTLock — dokumentacja**
- [How to get started](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/getStartedEn.html) · [FAQ](https://euopen.ttlock.com/documentPages/htmlPages/example/FAQEn.html)
- [Unlock with APP (eKey)](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/ekeyEn.html) · [Unlock with Passcode](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/passcodeEn.html) · [Unlock via network (Gateway)](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/gatewayEn.html)
- [Third-party Bluetooth Device Integration](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/Third-partyEn.html) · [On Premise](https://euopen.ttlock.com/documentPages/htmlPages/userGuide/onPremiseEn.html)
- [Menu dokumentacji (link od usera)](https://euopen.ttlock.com/document/doc?urlName=userGuide%2FekeyEn.html)
- [Pomoc TTLock — usługi płatne w apce](https://ttlockdoc.ttlock.com/en/docs/%E5%BF%AB%E9%80%9F%E5%AD%A6%E4%B9%A0/App/%E5%A2%9E%E5%80%BC%E6%9C%8D%E5%8A%A1)

**TTLock — API i SDK**
- [Cloud API — lock/initialize](https://euopen.ttlock.com/doc/api/v3/lock/initialize) · [key/list](https://euopen.ttlock.com/doc/api/v3/key/list) · [lock/unlock](https://euopen.ttlock.com/doc/api/v3/lock/unlock) · [keyboardPwd/get](https://euopen.ttlock.com/doc/api/v3/keyboardPwd/get) · [user/register](https://euopen.ttlock.com/doc/api/v3/user/register)
- [GitHub — ttlock/Android_SDK_Demo](https://github.com/ttlock/Android_SDK_Demo) · [pub.dev — ttlock_flutter](https://pub.dev/packages/ttlock_flutter) · [GitHub — ttlock/ttlock_flutter](https://github.com/ttlock/ttlock_flutter)

**Bramka G6 Matter**
- [TTLOCK EU — G6 Matter](https://ttlock.eu/shop/ttlock-g6-matter/) · [Blusafe — G6 Matter](https://blusafesolutions.eu/tr/products/ttlock-g6-wi-fi-gateway-bridge-matter-enabled)
- [Forum Home Assistant — opinia o G6](https://community.home-assistant.io/t/ttlock-integration-with-ha/211244?page=8) · [Forum Homey — G6 Matter](https://community.homey.app/t/tt-lock-g6-matter-hub-for-simpled-door-locks/149656)
- [matter-survey.org — Hangzhou Sciener](https://matter-survey.org/vendor/hangzhou-sciener-smart-technology-co-ltd-5450)

**Odrzucone (sekcja 4.4) i integracje społecznościowe**
- [PyPI — ttlock-ble](https://pypi.org/project/ttlock-ble/) · [GitHub — roquerodrigo/ttlock-ble](https://github.com/roquerodrigo/ttlock-ble)
- [GitHub — kind3r/ttlock-sdk-js](https://github.com/kind3r/ttlock-sdk-js) · [kind3r/hass-addons](https://github.com/kind3r/hass-addons)
- [GitHub — jbergler/hass-ttlock (chmura)](https://github.com/jbergler/hass-ttlock)

**Alternatywy i producent**
- [Nuki — integracja Matter](https://help.nuki.io/hc/en-001/articles/14596795148561-Matter-integration) · [Nuki — Bridge HTTP API](https://developer.nuki.io/t/bridge-http-api/26)
- [LinkedIn — Liliwise](https://www.linkedin.com/company/liliwise-smart-door-lock)
