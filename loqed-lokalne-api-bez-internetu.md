# LOQED — lokalne API i praca bez internetu

Spisane 2026-09-22 z dokumentacji producenta (support.loqed.com, odczyt 21–22.09.2026).
Legenda: [KONWENCJA-NOTATEK.md](KONWENCJA-NOTATEK.md).
Powiązane: [zamki-ttlock-podlaczenie-do-huba.md](zamki-ttlock-podlaczenie-do-huba.md) (porównanie z TTLock) ·
[kto-w-ue-brandu-chinskie-klamki-sprawdzone.md](kto-w-ue-brandu-chinskie-klamki-sprawdzone.md) ·
[jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md](jak-sprawdzic-czy-urzadzenie-ma-legalny-bluetooth.md)

---

## 1. Produkt

**LOQED Touch Smart Lock 2S** — sprzedawany przez Shelly (LOQED należy do Shelly).
W bazie Bluetooth SIG: `LOQED Touch Smart Lock`, model **2.0**, **Loqed B.V.** — Adopter ✅
([karta](https://qualification.bluetooth.com/ListingDetails/195261)).

🔴 **Rozróżnienie modeli** [DOK]: **LOQED Touch → LOQED Bridge**, **LOQED Pure → LOQED BLU Gateway**.
Lokalne API jest „primarily Bridge-oriented", więc bierzemy **Touch z Bridge**.

Zamek łączy się z mostkiem po **Bluetooth LE**; mostek jest na **Wi-Fi**. Brak Zigbee, Z-Wave,
Thread i Mattera w samym zamku.

## 2. Trzy API [DOK]

| API | Do czego |
|---|---|
| Web API | aplikacje webowe, przez chmurę LOQED |
| 🟢 **Local bridge API** | *„The LOQED Bridge **runs a mini-webserver**. If you are integrating with an application **in your home** such as a smart home hub, this is the recommended way."* |
| OAuth 2.0 | dla usług typu Google Home, tylko po zgodzie LOQED |

**Kluczowa pozycja z ich tabeli porównawczej:** „Control lock, **your own application generates
the signature and you keep the encryption key** (more secure)" → dla lokalnego API: **Yes**.

## 3. Jak to działa

```
zamek ──BLE──► LOQED Bridge (Wi-Fi, mini-serwer HTTP, mDNS)
                    │  ▲
   GET open/latch/  │  └── webhook POST ──► NASZ HUB (Node.js) ──► apka
   night_lock/status┘
```

- **sterowanie**: `GET` na IP mostka — `open`, `latch` (otwarte), `night_lock` (zamknięte), `status`
- **zdarzenia**: mostek sam wysyła webhooki przy zmianie stanu, z `key_local_id` (kto otworzył),
  stanem baterii i utratą łączności BLE
- **podpis**: albo generuje mostek (klucz lata po LAN otwartym tekstem — LOQED ostrzega),
  albo 🟢 **my, trzymając klucz u siebie** — przykłady w JS i PHP na `app.loqed.com/Bridge-API-example/`
- **weryfikacja webhooka**: `sha256(BODY + TIMESTAMP + base64_decode(klucz))` w nagłówku
- status odpytywać **max raz dziennie** — do reszty webhooki

Home Assistant: *„This integration uses the **local bridge API**"*, mostek wykrywany przez
**Zeroconf**, *„does not support bridges outside your local network"* [DOK].

## 4. 🟢 Co działa BEZ internetu [DOK — artykuł „Does my LOQED lock work without a bridge, gateway or internet?"]

- **otwieranie i zamykanie z apki LOQED przez Bluetooth**, gdy telefon jest przy zamku
- **kod PIN** na zewnętrznym panelu (LOQED Touch ma go standardowo)
- **lokalne akcesoria Bluetooth**, np. Shelly BLU Button, jeśli wcześniej skonfigurowane
- **[DEDUKCJA]** sterowanie z naszego huba przez lokalne API mostka — cała ścieżka jest w LAN-ie

## 5. 🔴 Co WYMAGA internetu [DOK]

- zdalne otwieranie i zamykanie spoza domu
- zmiana większości ustawień spoza zasięgu Bluetooth
- synchronizacja kluczy, harmonogramów i statusu z chmurą
- **Touch to Open / Auto Unlock** (otwieranie po przyjściu) — wymaga **online'owego mostka,
  internetu w telefonie i uprawnień w tle**. To sztandarowa funkcja i jest chmurowa
- **jednorazowo przy wdrożeniu**: klucz API i sekret mostka z `app.loqed.com/API-Config` —
  *„There is no other way to retrieve this key"*

## 6. 🔴 Jedyne otwarte pytanie #do-sprawdzenia

**Czy mostek dalej odpowiada na lokalne API po odcięciu WAN-u?** Dokumentacja tego wprost
nie mówi — pisze tylko, że mostek musi być „connected to Wi-Fi", a Wi-Fi to nie internet.
Poszlaki są mocne (Home Assistant działa wyłącznie po LAN, mDNS, podpis po naszej stronie),
ale **dowodem jest test odcięcia WAN na próbce**. To pierwsza rzecz do zrobienia po zakupie.

## 7. Dlaczego to jest przełom

| | TTLock | **LOQED** |
|---|---|---|
| Lokalne API bramki | 🔴 **brak** | 🟢 **jest, udokumentowane** |
| Klucz po naszej stronie | 🔴 nie | 🟢 tak |
| Integracja w Homey | 🔴 **nie ma, od 2022 tylko prośby** | — |
| Integracje | tylko przez chmurę | HA, Loxone, Fibaro, Domoticz, IP-SYMCON |

**Homey nie zrobił TTLocka przez trzy lata nie z lenistwa — tylko dlatego, że bez lokalnego
API nie da się tego zrobić dobrze.** LOQED to API ma i integracje są wszędzie.

## 8. Jedno zdanie

> **Tak, LOQED Touch 2S działa offline: telefonem po Bluetooth przy drzwiach, kodem PIN na panelu
> i z naszego huba przez lokalne API mostka — internet jest potrzebny raz przy konfiguracji,
> do zdalnego dostępu i do automatycznego otwierania po przyjściu.**
