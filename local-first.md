# Local-first — co naprawdę działa, gdy router traci internet

Notatka nadrzędna, wspólna dla [`sonoff/`](sonoff/) i [`shelly/`](shelly/).
Spisane 2026-08-17.

## Założona topologia

```
telefon ──WiFi──┐
                 ├── router (BEZ internetu) ── Home Assistant (własny)
urządzenia ──────┘                                    │
                                                 Mosquitto (core/)
```

Internet bywa potrzebny (aktualizacje, zdalny dostęp), ale **nie jest warunkiem działania
domu**. Sterowanie z telefonu ma iść: telefon → LAN → HA → urządzenie. Zero ruchu przez
chmurę producenta.

## Werdykt: ta topologia działa

| Element | Bez internetu |
|---|---|
| Przekaźnik + przycisk fizyczny | ✅ zawsze |
| Shelly: panel WWW, RPC po HTTP/WS | ✅ |
| Shelly: MQTT do własnego brokera | ✅ |
| Shelly: skrypty na urządzeniu | ✅ (nawet gdy padnie HA) |
| Matter over Wi-Fi (praca bieżąca) | ✅ |
| Home Assistant Core + automatyzacje | ✅ |
| Aplikacja HA na telefonie w LAN | ✅ *(wymaga konfiguracji, patrz niżej)* |
| Aktualizacje firmware i HA | ❌ |
| Zdalny dostęp spoza domu | ❌ *(od tego jest headscale)* |

---

## Sonoff w tej topologii — zależy od modelu

Tabela wyżej opisuje Shelly. U Sonoffa odpowiedź rozjeżdża się na dwa modele.

### MINI-1GSP — pełny local-first, potwierdzony przez producenta

Strona produktu mówi **wprost** dokładnie o tym scenariuszu:

> „**In a LAN-only environment**, the device's local web interface and Home Assistant
> (via MQTT integration) **still synchronize data in real time**."

Czyli: router bez internetu, urządzenie gada z Twoim Mosquitto, HA dostaje dane na żywo.
Do tego panel WWW urządzenia „**without installing an app or connecting to the cloud**"
i pierwsza konfiguracja przez tryb AP i przeglądarkę, bez konta eWeLink.

Pod względem local-first **MINI-1GSP dorównuje Shelly**. Jedyna różnica: brak skryptów
on-device, czyli słabszy drugi poziom odporności (patrz niżej). Urządzenie ma komponent
`Timer`, więc harmonogramy i inching przeżyją padnięcie HA — ale własnej logiki tam nie
zaszyjesz.

[DO SPRAWDZENIA] W API jest komponent **`Ota`**, co sugeruje, że aktualizacja firmware'u
da się zrobić lokalnie przez RPC, bez eWeLink. Strona `API_Ota` nieczytana.

### BASIC-1GSP — działa lokalnie, ale okrojony do sterowania

Bez lokalnego RPC ([`sonoff/07-offline-i-chmura.md`](sonoff/07-offline-i-chmura.md))
zostaje **Matter**, a Matter jest protokołem lokalnym z założenia.

| Funkcja bez internetu | Stan |
|---|---|
| Włącz/wyłącz z HA przez Matter | ✅ działa lokalnie |
| Power On State | ✅ |
| Przycisk fizyczny | ✅ |
| Progi ochronne (prąd, napięcie, moc) | ✅ **działają, ale konfiguruje się je raz w eWeLink** |
| **Pomiar mocy/napięcia/prądu w HA** | ⚠️ **niepewne — to jest sedno problemu** |
| Historia 180 dni, koszty energii | ❌ żyje w aplikacji eWeLink |
| Aktualizacja firmware | ❌ tylko przez eWeLink (czyli przez chmurę) |

🔴 **Praktyczna konsekwencja: BASIC-1GSP nie jest urządzeniem w pełni bezobsługowym
w trybie local-first.** Do wstępnej konfiguracji progów zabezpieczeń i do aktualizacji
firmware'u i tak musisz go raz na jakiś czas pokazać chmurze eWeLink. Sterowanie i tak
działa lokalnie, ale „postawić i zapomnieć bez konta u producenta" — nie wyjdzie.

⚠️ **KOREKTA:** napisałem wcześniej, że przegranie firmware'u odpada. To było zbyt
kategoryczne. Tasmota i ESPHome faktycznie nie obsługują BK7238, ale **[OpenBeken]
(https://github.com/openshwprojects/OpenBK7231T_App) tak** — projekt wprost wymienia
BK7238 wśród wspieranych układów i istnieje udokumentowany przypadek konwersji
**Sonoffa S61STPF z firmware'u Matter na OpenBeken** (BK7238).

Czyli droga istnieje. Ale przy BASIC-1GSP odradzam: to urządzenie na 32 A wpięte na
stałe w rozdzielnicę, otwieranie go i lutowanie UART to co innego niż wtyczka. Do tego
brak potwierdzonego raportu dla tego konkretnego modelu, utrata gwarancji, a relacje
z forum mówią, że „success in the flasher does not guarantee a bootable runtime image
on BK7238". Przy sprzęcie przełączającym 32 A to zła relacja ryzyka do zysku.

### Wspólne dla obu: urządzenie dobija się do chmury

Sonoff z aktywnym kontem eWeLink będzie w kółko próbował się połączyć, gdy nie ma
internetu. Nie psuje to działania w LAN, ale generuje ruch i logi. W API jest komponent
`Cloud` — [DO SPRAWDZENIA] czy da się wyłączyć tak jak u Shelly.

---

## 🔴 Co realnie się psuje — lista wg prawdopodobieństwa ugryzienia

### 1. Telefon sam ucieka na dane komórkowe

**Najczęstsza przyczyna „nie działa", a nie ma nic wspólnego ze smart home.**

Android i iOS wykrywają brak internetu w Wi-Fi i potrafią **przełączyć się na LTE** albo
oznaczyć sieć jako wadliwą. Wtedy telefon nie widzi HA, mimo że fizycznie jest w tej samej
sieci.

Co zrobić:
- Android: wyłączyć „Przełączaj na dane komórkowe, gdy Wi-Fi nie ma dostępu do internetu"
  (nazwa zależy od nakładki, szukać w opcjach Wi-Fi / sieci)
- iOS: wyłączyć „Wi-Fi Assist"
- Docelowo lepiej: router ma odpowiadać na sprawdzanie łączności tak, żeby telefon nie
  uznawał sieci za zepsutą [DO SPRAWDZENIA na konkretnym routerze]

### 2. DNS

Gdy internet pada, router zwykle nadal jest serwerem DNS, ale przestaje umieć rozwiązywać
cokolwiek. Każde zapytanie DNS wisi do timeoutu.

Skutki: HA startuje wolno, integracje sypią błędami, aplikacja mobilna zwleka.

Co zrobić:
- Adresować urządzenia **po IP**, nie po nazwach — rezerwacje DHCP w routerze
- Albo mDNS: Shelly ogłasza się jako `_shelly._tcp` i `<id>.local`, co działa **w pełni
  lokalnie**, bez DNS
- HA pod stałym IP, w aplikacji mobilnej ustawić **Internal URL** jako `http://<ip>:8123`

### 3. Czas (NTP) — cichy zabójca harmonogramów

Bez internetu nie ma synchronizacji czasu. Konsekwencje:

- harmonogramy i timery kalendarzowe na urządzeniach przestają być wiarygodne
- Shelly: `aenergy.by_minute` **w ogóle nie istnieje** przy niezsynchronizowanym zegarze
  ([DOK] z dokumentacji)
- znaczniki `ts` w notyfikacjach robią się bezużyteczne do korelacji

Co zrobić: **postawić lokalny serwer NTP** — na routerze albo na hoście HA — i wskazać go
w konfiguracji urządzeń (Shelly: komponent `Sys`). Wtedy zegar trzyma się nawet przy
tygodniach bez internetu.

To jest rzecz, o której się zapomina, a psuje dane pomiarowe po cichu.

### 4. Certyfikaty TLS

Jeśli HA stoi za HTTPS z Let's Encrypt, **odnowienie certyfikatu wymaga internetu**.
Wygasły certyfikat = aplikacja mobilna odmawia połączenia, mimo że wszystko działa.

Co zrobić: do dostępu w LAN używać zwykłego HTTP albo certyfikatu o długiej ważności.
HTTPS zostawić dla dostępu zdalnego.

### 5. Urządzenia dobijają się do chmury

Shelly i Sonoff z włączoną chmurą będą w kółko próbowały się połączyć. Nie psuje to
działania, ale generuje ruch, logi i czasem opóźnienia.

Co zrobić na Shelly: `Cloud.SetConfig {"enable": false}`. Chmura wyłączona całkowicie,
reszta działa bez zmian.

### 6. Matter — parowanie vs praca

Praca bieżąca Matter jest w pełni lokalna. **Parowanie nowego urządzenia lepiej zrobić,
gdy internet jest** — proces weryfikacji certyfikatów urządzenia bywa wrażliwy.
[DEDUKCJA — nie weryfikowane; ale to tani środek ostrożności: nowe urządzenia dodawać
przy działającym łączu]

---

## Checklista local-first

- [ ] Rezerwacje DHCP dla wszystkich urządzeń i HA — stałe IP
- [ ] Lokalny serwer NTP + wskazanie go w konfiguracji urządzeń
- [ ] Chmura producenta wyłączona (Shelly: `Cloud.SetConfig`)
- [ ] MQTT: własny Mosquitto **z hasłem** — steruje obwodami pod napięciem
- [ ] HA: Internal URL po IP, HTTP w LAN
- [ ] Telefon: wyłączone automatyczne przełączanie na LTE przy Wi-Fi bez internetu
- [ ] Krytyczna logika bezpieczeństwa zejść na urządzenie (skrypty Shelly), nie zostawiać
      jej wyłącznie w HA
- [ ] Sprawdzić realnie: odłączyć WAN na godzinę i przejść scenariusze

Ostatni punkt jest najważniejszy. **Local-first, którego nikt nie przetestował przez
odpięcie kabla, to założenie, nie właściwość systemu.**

---

## Trzy poziomy odporności

Warto świadomie zdecydować, co ma przeżyć każdą z awarii:

| Co pada | Co ma dalej działać |
|---|---|
| **Internet** | Wszystko poza aktualizacjami i dostępem spoza domu |
| **Home Assistant** | Przekaźniki, przyciski fizyczne, skrypty na urządzeniach Shelly, harmonogramy na urządzeniach |
| **Wi-Fi / router** | Wyłącznie sterowanie fizycznym przyciskiem |

Z tego wynika praktyczny podział: **logika, która musi zadziałać zawsze — na urządzeniu.
Logika wymagająca stanu globalnego i korelacji — w HA.**

To argument za Shelly niezależny od API: skrypty on-device (`load-shedding.js`,
`failure-monitor.js`) działają na drugim poziomie odporności, gdzie HA już nie ma.
Sonoff tego poziomu nie oferuje wcale.

---

## Dostęp spoza domu bez chmury producenta

W repo jest już `headscale-server/` — własny control plane Tailscale. To właściwa odpowiedź
na „chcę mieć dostęp zdalny, ale bez Nabu Casa i bez chmury producenta".

Uwaga na kolejność zależności: headscale **wymaga internetu**, więc jest warstwą
*dodatkową*, a nie fundamentem. Fundament to LAN, który działa sam.
