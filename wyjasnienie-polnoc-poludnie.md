# Dlaczego „bogactwo w środku, wspólny mianownik na zewnątrz"

Wyjaśnienie pojęcia z [strategia-protokoly.md](strategia-protokoly.md).
Spisane 2026-08-17.

---

## 1. Dlaczego Matter jest z założenia ubogi

Matter ma jedno zadanie: **żeby DOWOLNE urządzenie DOWOLNEGO producenta działało
z DOWOLNYM ekosystemem.** Apple, Google, Amazon, Samsung — wszyscy naraz.

Żeby to osiągnąć, każda funkcja musi być **wcześniej opisana w specyfikacji**.
Apple musi wiedzieć, jak narysować przycisk dla „gniazdka", zanim ktokolwiek wyprodukuje
to gniazdko.

Konsekwencja jest nieunikniona:

> **Matter potrafi tylko to, co wszyscy wcześniej uzgodnili.**

Jeśli producent zrobi urządzenie z 40 funkcjami, a specyfikacja Matter opisuje 6 z nich —
przez Matter zobaczysz 6. Nie dlatego, że Matter jest źle zaprojektowany, tylko dlatego,
że **to jest cena uniwersalności**.

To nie jest wada. To jest kompromis, którego nie da się uniknąć przy standardzie mającym
połączyć wszystkich ze wszystkimi.

---

## 2. Analogia: RAW kontra JPEG

Jeśli robisz zdjęcia, znasz ten problem.

| | Aparat → RAW → eksport JPEG | Aparat → od razu JPEG |
|---|---|---|
| Co masz w archiwum | pełne dane z matrycy | już skompresowane |
| Możesz odzyskać szczegóły | ✅ | ❌ **bezpowrotnie stracone** |
| Możesz wyeksportować JPEG | ✅ zawsze | ✅ |

**Fotograf zapisuje RAW, bo z RAW-a zawsze zrobi JPEG-a — ale z JPEG-a nie odzyska RAW-a.**

Dokładnie to samo dotyczy protokołów w Twoim systemie:

| | Urządzenie → natywny protokół → hub → Matter | Urządzenie → Matter → hub |
|---|---|---|
| Co wchodzi do systemu | **pełne dane** | już okrojone |
| Możesz pokazać w Apple Home | ✅ | ✅ |
| Możesz robić własne analizy, wykresy, alarmy | ✅ | ❌ **danych nigdy nie było** |

🔴 **Sedno: jeśli Matter jest Twoim WEJŚCIEM, tracisz dane na progu systemu — i nie
odzyskasz ich nigdy.** Jeśli Matter jest Twoim WYJŚCIEM, trzymasz wszystko u siebie,
a na zewnątrz pokazujesz uproszczony widok.

---

## 3. „Północ" i „południe" — co to znaczy w Twoim kodzie

To są po prostu dwie strony Twojego huba.

```
        ╔═══════════════════════════════════════════════╗
        ║   PÓŁNOC — ekosystemy i użytkownik            ║
        ║   Apple Home · Google Home · Alexa            ║
        ╚═══════════════════════════════════════════════╝
                            ▲
                            │  wystawiasz jako MATTER
                            │  (Twój hub udaje urządzenia)
        ┌───────────────────────────────────────────────┐
        │                                               │
        │   TWÓJ HUB (Odroid + Node.js)                 │
        │                                               │
        │   • własny model urządzenia                   │
        │   • pełna telemetria, historia, logika        │
        │   • tu mieszka CAŁE bogactwo danych           │
        │                                               │
        └───────────────────────────────────────────────┘
                            ▲
                            │  pobierasz NATYWNIE
                            │  (każdy protokół po swojemu)
        ╔═══════════════════════════════════════════════╗
        ║   POŁUDNIE — urządzenia                       ║
        ║   Zigbee · Shelly RPC · MQTT · Matter         ║
        ╚═══════════════════════════════════════════════╝
```

**Południe = Twoje integracje (sterowniki).** Kod, który gada z urządzeniami.
Dla każdego rodzaju sprzętu inny, dobrany tak, żeby wyciągnąć **maksimum** danych:

- klient Zigbee2MQTT → czujniki
- klient JSON-RPC → Shelly
- kontroler Matter → urządzenia Matter
- HTTP/MQTT → reszta

**Północ = jedna implementacja.** Twój hub **udaje zestaw urządzeń Matter** i wystawia
je do Apple Home, Google Home i Alexy. W nomenklaturze Mattera nazywa się to
**Matter Bridge** — to oficjalny typ urządzenia w specyfikacji, stworzony właśnie po to.

🟢 **To jest asymetria, o którą chodzi:** na południu masz wiele bogatych protokołów,
na północy jeden ubogi, ale uniwersalny. Konwersja idzie tylko w dół i **tylko na samym
brzegu systemu**.

---

## 4. Ten sam sprzęt, dwie drogi — konkret

Weźmy `Shelly Pro 3EM` mierzący cały dom.

### Droga A: hub pobiera przez natywne RPC ✅

Dostajesz komplet z komponentu `EM`:

napięcie · prąd · **moc czynna** · **moc pozorna** · **współczynnik mocy** ·
częstotliwość · energia pobrana · **energia oddana** · liczniki minutowe ·
**dane per faza** · temperatura · błędy · liczniki cyklu życia

Do tego skrypty na urządzeniu, webhooki, konfiguracja progów.

### Droga B: hub pobiera przez Matter

Dostajesz to, co producent zaimplementował z `ElectricalMeter` — w najlepszym razie
moc i energię sumarycznie.

**Ginie:** współczynnik mocy, rozbicie na fazy, energia oddana osobno, częstotliwość,
liczniki minutowe.

### Dlaczego to boli akurat Ciebie

Twój model biznesowy stoi na **danych i analizie zużycia**. Bez współczynnika mocy nie
zdiagnozujesz problemu z instalacją. Bez rozbicia na fazy nie wykryjesz asymetrii.
Bez energii oddanej nie obsłużysz fotowoltaiki.

**Te dane nie „są schowane" — one po prostu nigdy nie weszły do Twojego systemu.**

### A na północ i tak idzie to samo

W Apple Home klient zobaczy „zużycie: 2,4 kW" w obu przypadkach. Bo Apple Home i tak
więcej nie pokaże.

🎯 **Czyli droga A nie kosztuje Cię nic po stronie użytkownika, a daje wszystko po
stronie Twojego produktu.**

---

## 5. Trzy zasady, które z tego wynikają

### Zasada 1 · Na wejściu bierz najbogatszy dostępny protokół

Kolejność preferencji przy każdym urządzeniu:

1. **natywne lokalne API** (Shelly RPC, Sonoff MINI-1GSP RPC) — najbogatsze
2. **Zigbee przez Z2M** — bardzo bogate, Z2M wystawia wszystkie atrybuty
3. **Matter** — gdy nic lepszego nie ma
4. chmura producenta — nigdy

### Zasada 2 · Matter obiecuj klientowi, nie sobie

„Działa z Apple Home, Google i Alexą, i będzie działać za pięć lat" — **to jest
obietnica marketingowa i jest w pełni prawdziwa.**

Ale wewnątrz systemu nie ograniczaj się do tego, co Matter potrafi.

### Zasada 3 · Konwertuj raz, na samym brzegu

Twój wewnętrzny model urządzenia trzyma wszystko. Dopiero warstwa mostu Matter decyduje,
co pokazać na zewnątrz. Jeśli jutro Matter 1.6 doda nowe klastry — **zmieniasz tylko
warstwę mostu, dane już masz**.

Gdybyś zbierał przez Matter, musiałbyś czekać, aż producenci zaktualizują firmware.

---

## 6. Kto już tak robi

**Home Assistant** — integracje pobierają natywnie (Zigbee, Z-Wave, Shelly, Tasmota,
setki innych), a osobny komponent wystawia encje HA jako Matter do Apple i Google.
Bogactwo w środku, wspólny mianownik na zewnątrz.

**Aqara Hub M3** — gada ze swoimi czujnikami po Zigbee, a wystawia je jako urządzenia
Matter. Klient kupuje tanie czujniki Zigbee i widzi je w Apple Home.

**[DEDUKCJA] I to jest dokładnie miejsce dla Twojego produktu.** Wartość, za którą klient
płaci, to nie jest „kolejny czujnik" — to **warstwa, która spina bogaty świat lokalny
z ubogim, ale uniwersalnym światem ekosystemów, i robi to bez chmury.**

---

## 7. Jedno zdanie do zapamiętania

> **Matter to język, którym Twój hub rozmawia ze ŚWIATEM.
> Nie musi być językiem, którym rozmawia ze swoimi URZĄDZENIAMI.**
