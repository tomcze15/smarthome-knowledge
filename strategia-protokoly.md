# Matter, Thread, Zigbee — która warstwa jest która

Uzupełnienie do [strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md).
Spisane 2026-08-17.

---

## 🔴 Najczęstszy błąd: „Matter kontra Zigbee"

To porównanie nie ma sensu, bo **to nie są byty tego samego rzędu**.

```
              ┌─────────────────────────────────────┐
WARSTWA       │  Matter  (aplikacja, nad IP)        │
APLIKACJI     └─────────────────────────────────────┘
                    │           │           │
              ┌─────────┐ ┌──────────┐ ┌─────────┐
TRANSPORT     │  Wi-Fi  │ │ Ethernet │ │ Thread  │
              └─────────┘ └──────────┘ └─────────┘
                                            │
              ┌─────────────────────────────────────┐
RADIO         │        IEEE 802.15.4                │
              └─────────────────────────────────────┘

              ┌─────────────────────────────────────┐
ZIGBEE        │  własny stos: radio + sieć + apka   │  ← pełen komin
              │        na IEEE 802.15.4             │
              └─────────────────────────────────────┘
```

**Matter to warstwa aplikacji działająca nad IP.** Nie ma własnego radia. Jeździ po Wi-Fi,
Ethernecie albo Threadzie.

**Zigbee to pełny stos** — własne radio, własna sieć, własna warstwa aplikacji.

🎯 **Właściwe porównanie dla czujników bateryjnych brzmi: Thread kontra Zigbee.**
Oba na tym samym radiu 802.15.4, w tym samym paśmie, o podobnym profilu poboru mocy.

---

## Dlaczego Zigbee naprawdę wygrywa — tanie Tuya to tylko jeden z powodów

Twoja diagnoza („dużo taniego sprzętu") jest prawdziwa, ale to nie jest główny powód
techniczny. Zigbee trzyma się z trzech twardszych przyczyn:

| Powód | Szczegół |
|---|---|
| **Bateria** | Czujnik Zigbee: 1–3 lata na ogniwie pastylkowym. **Urządzenie Matter-over-Wi-Fi nie może być czujnikiem bateryjnym** — Wi-Fi zjada za dużo prądu |
| **Mesh** | Urządzenia zasilane sieciowo działają jako routery i rozszerzają zasięg. Wi-Fi nie ma mesh na poziomie urządzeń |
| **Nie dotyka Wi-Fi** | 200 czujników Zigbee nie zjada dzierżaw DHCP ani czasu antenowego Wi-Fi |

⚠️ **I rzecz, która zaskakuje:** Thread miał być lepszy od Zigbee w zużyciu energii
(deklaratywnie 2–5 lat na pastylce), ale **narzut stosu IPv6 + Matter sprawia, że wiele
wczesnych czujników Thread/Matter wypada w praktyce gorzej niż dobrze zestrojone
Zigbee**. To stan na 2026, nie prawo natury — ale planując produkt trzeba liczyć się
z tym, co jest, a nie z tym, co obiecano.

---

## 🎯 Korekta mojej rady o ODM: rób go w Thread, nie w Zigbee

Poprzednio napisałem „ODM w Zigbee". **To była rada o gwarancji local-first, nie
o wyborze strategicznym.** Skoro chcesz iść w Matter — da się mieć jedno i drugie.

Mechanizm gwarancji, który miałem na myśli, brzmi: **urządzenie bez Wi-Fi nie ma
fizycznej drogi do chmury producenta.** A to jest prawdziwe tak samo dla Zigbee,
jak i dla **Thread**.

| | ODM Matter-over-Wi-Fi | **ODM Matter-over-Thread** | ODM Zigbee |
|---|---|---|---|
| Matter natywnie | ✅ | ✅ | ❌ (przez most) |
| **Fizyczny brak drogi do chmury** | ❌ | ✅ | ✅ |
| Czujnik bateryjny | ❌ | ✅ | ✅ |
| Mesh | ❌ | ✅ | ✅ |
| Nie obciąża Wi-Fi | ❌ | ✅ | ✅ |
| Wymaga Border Routera | ❌ | ✅ | ✅ (koordynator) |
| Certyfikacja CSA | ✅ koszt | ✅ koszt | ❌ nie dotyczy |
| Dojrzałość dostawców ODM | wysoka | **niższa** | wysoka |

🟢 **Matter over Thread daje Ci jednocześnie: strategiczny wybór (Matter), gwarancję
local-first z fizyki (brak Wi-Fi) i czujniki bateryjne.** To jest właściwa odpowiedź
na Twoje pytanie.

Sonoff **wymienia Thread** wśród protokołów w ofercie ODM
([sonoff/09-odm-partnership.md](sonoff/09-odm-partnership.md)) — mimo że nie widziałem
Threada w ich asortymencie detalicznym. Warto o to zapytać wprost.

### 🔴 Matter over Wi-Fi to najgorsza z opcji dla Twojego produktu

Ma Wi-Fi (czyli **istnieje droga do chmury producenta**), nie nadaje się do zasilania
bateryjnego, nie robi mesh, **a do tego przez Matter dostajesz okrojony zestaw funkcji
w stosunku do protokołu producenta** — dokładnie tak, jak zobaczyliśmy przy `BASIC-1GSP`
(przez Matter: on/off i Power On State, i tyle).

Jego jedyna zaleta: nie wymaga Border Routera.

---

## 🟢 Dobra wiadomość dla Twojego use case: Matter 1.4 umie energię

To ważne, bo pomiar energii jest u Ciebie osią wartości. Matter 1.4 dorzucił typy
urządzeń wprost pod zarządzanie energią:

`ElectricalMeter` · `ElectricalUtilityMeter` · `ElectricalEnergyTariff` ·
`MeterReferencePoint` · `BatteryStorage` · `SolarPower` · `EnergyEvse` · `HeatPump` ·
`WaterHeater`

Konkrety:
- **EVSE** — preferencje ładowania, użytkownik wskazuje kiedy auto ma być naładowane
- **Podgrzewacze wody** — temperatura albo procent, komenda „boost", monitoring poziomu
  ciepłej wody
- **Fotowoltaika** — falowniki, pojedyncze panele i łańcuchy, systemy hybrydowe z magazynem
- **Taryfy** — `ElectricalEnergyTariff` jako typ urządzenia

⚠️ Ale: **typ urządzenia w specyfikacji ≠ obsługa w konkretnym sprzęcie.** Producent musi
to zaimplementować i wystawić. Sprawdzaj per model, testem z
[strategia-doboru-sprzetu.md](strategia-doboru-sprzetu.md).

---

## Konsekwencje dla architektury huba

### Twój Odroid i tak potrzebuje radia 802.15.4

Zigbee wymaga koordynatora. Thread wymaga Border Routera. **Oba jadą na 802.15.4.**

Możliwości:

1. **Dwa osobne dongle** — jeden Zigbee, jeden Thread. Najbezpieczniejsze, brak konfliktów
2. **Jeden dongle multiprotokołowy** — tańsze, ale [DO SPRAWDZENIA] jak zachowuje się
   jednoczesny Zigbee + Thread na jednym radiu; znane są kompromisy wydajnościowe,
   bo oba dzielą czas antenowy w tym samym paśmie
3. Osobne urządzenie jako TBR w sieci

🟢 **Thread Border Router zwykle jest jednocześnie kontrolerem Matter** — czyli Twój
Odroid może pełnić obie role.

⚠️ Uwaga na pasmo: 802.15.4 i Wi-Fi 2,4 GHz **nakładają się**. Przy Twoich pakietach
z dedykowanym AP i Mesh warto planować kanały tak, żeby Zigbee/Thread nie siedziały
na tym samym kanale co Wi-Fi.

### 🎯 Hub jako most Zigbee → Matter to Twój atut sprzedażowy

Skoro i tak masz koordynator Zigbee i kontroler Matter w jednym urządzeniu, możesz
**wystawiać urządzenia Zigbee klienta jako urządzenia Matter** do Apple Home, Google Home
i Alexa. Tak działa np. Aqara Hub M3.

Dla klienta wygląda to tak: kupuje tanie czujniki Zigbee, a widzi je w Apple Home.
**Ty dostarczasz warstwę, która to spina** — i to jest namacalna wartość, za którą można
wziąć pieniądze.

---

## Rekomendowany podział warstw

| Warstwa | Technologia | Uzasadnienie |
|---|---|---|
| **Interfejs na zewnątrz** (Apple/Google/Alexa) | **Matter** | Przyszłościowe, to jest ta strategiczna decyzja |
| **Aktory zasilane sieciowo** (przekaźniki, gniazdka) | Matter over Wi-Fi **albo** Zigbee | Wi-Fi OK, ale pamiętaj o VLAN; funkcje przez Matter bywają okrojone |
| **Czujniki bateryjne** | **Zigbee dziś, Thread docelowo** | Zigbee ma dziś lepszą baterię i tańszy sprzęt |
| **Własny ODM** | **Thread** (albo Zigbee, jeśli koszt CSA boli) | Matter natywnie + fizyczna gwarancja local-first |
| **Obwody siłowe, rozdzielnica** | Wi-Fi/Ethernet z lokalnym API (Shelly Pro) | Tu bateria nie gra roli, a chcesz pełni funkcji |

**[DEDUKCJA] Sedno:** Matter jako **strategia i interfejs**, Zigbee jako **pragmatyka
warstwy urządzeń**, Thread jako **ścieżka migracji i kierunek własnego sprzętu**.
To nie są konkurenci, tylko różne piętra tego samego budynku.

---

## 🔴 SPROSTOWANIE: „okrojone funkcje" to wina producenta, nie transportu

Wyżej wrzuciłem „przez Matter dostajesz okrojony zestaw funkcji" do wad **Matter over
Wi-Fi**. **To był błąd kategorialny.**

Cienka implementacja klastrów to cecha **firmware'u producenta**, nie transportu.
Urządzenie Sonoffa **Matter over Thread miałoby dokładnie to samo ryzyko** — jeśli
producent nie zaimplementuje `ElectricalMeter`, to nie zaimplementuje go niezależnie
od tego, czy jedzie po Wi-Fi czy po Threadzie.

Prawdziwe wady Matter over Wi-Fi to **tylko trzy** i wszystkie są strukturalne:

1. ma Wi-Fi → istnieje droga do chmury producenta
2. nie zasilisz go baterią
3. nie robi mesh

Punkt „okrojone funkcje" **skreślam z tej listy.** To osobna oś, ortogonalna do transportu.

### Do tego moje źródła o BASIC-1GSP są sprzeczne

- recenzja/raport użytkownika: przez Matter w HA widać „mainly the on/off control
  and Power On State"
- **strona producenta**: pomiar energii przez Matter dostępny w Home Assistant
  i SmartThings

Nie rozstrzygnąłem tego i nie mam sprzętu. Zostaje jako [DO SPRAWDZENIA] — możliwe,
że raport dotyczył starszego firmware'u albo starszej wersji integracji Matter w HA.

---

## 🎯 Kluczowy wniosek architektoniczny: Matter na północ, nie na południe

I to jest rzecz, która rozbraja całe zmartwienie.

**Twój hub nie musi rozmawiać z urządzeniami przez Matter.**

```
            Apple Home · Google Home · Alexa · SmartThings
                              ▲
                              │  MATTER  ← interfejs „na północ"
                    ┌─────────────────────┐
                    │   Twój hub (Odroid) │
                    └─────────────────────┘
              ┌───────────┼───────────┬──────────────┐
              │           │           │              │
          Zigbee      Shelly RPC   Matter        MQTT/HTTP
          (Z2M)      (pełne dane)  (jak trzeba)
              │           │           │              │
                    interfejs „na południe"
```

Rozdzielenie ról:

| Kierunek | Protokół | Po co |
|---|---|---|
| **Na północ** (do ekosystemów) | **Matter** | Przyszłościowy standard, kompatybilność z Apple/Google/Alexa. Tu Matter jest bezkonkurencyjny |
| **Na południe** (do urządzeń) | **co jest najlepsze dla danego urządzenia** | Zigbee dla czujników, natywne RPC dla Shelly, Matter gdzie wystarcza |

🟢 **Problem cienkiego Mattera gryzie tylko wtedy, gdy używasz Mattera jako protokołu
URZĄDZEŃ. Jeśli używasz go jako protokołu EKOSYSTEMU, masz same zalety i zero
ograniczeń.**

Przykład konkretny, na Twoim use case z energią:

- hub gada z `Shelly Pro 3EM` przez **natywne RPC** → dostajesz komplet: napięcie, prąd,
  moc czynna i pozorna, PF, częstotliwość, energia w obu kierunkach, liczniki, historia
- hub gada z czujnikami przez **Zigbee** → pełna telemetria, baterie na lata
- hub **wystawia to wszystko na zewnątrz jako Matter** → klient widzi w Apple Home

Nic nie tracisz. Matter jest tam, gdzie daje wartość (kompatybilność), a nie tam,
gdzie odbiera (dostęp do danych).

**[DEDUKCJA] To zresztą model, którym idzie Home Assistant i Aqara** — most z bogatych
protokołów lokalnych do ubogiego, ale uniwersalnego Mattera. Bogactwo zostaje w środku,
na zewnątrz idzie wspólny mianownik.

---

## Czy Matter over Wi-Fi w ogóle ma sens w Twoim produkcie

Tak, w jednej roli: **aktory zasilane sieciowo, gdzie potrzebujesz tylko on/off**
i nie zależy Ci na danych. Włącznik światła, gniazdko. Wtedy trzy wady strukturalne
nie bolą (nie potrzebujesz baterii ani mesh), a droga do chmury jest zamknięta VLAN-em.

Gdzie **nie** ma sensu:

- ❌ czujniki bateryjne → Zigbee albo Thread
- ❌ urządzenia, z których chcesz **bogate dane** → natywne API (Shelly) albo Zigbee
- ❌ pakiet BYON bez VLAN-u, jeśli obiecujesz prywatność
