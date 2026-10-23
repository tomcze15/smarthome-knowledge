# Komponenty Switch i Meter

Źródła: `API_Switch`, `API_Meter`. Wszystko poniżej to **[DOK]**, przepisane z tabel.

---

# Switch — przekaźnik

| Metoda | Opis |
|---|---|
| `Switch.Set` | Ustaw stan (on/off) |
| `Switch.Toggle` | Przełącz |
| `Switch.GetStatus` | Odczytaj stan |
| `Switch.GetConfig` / `SetConfig` | Zachowanie po zaniku zasilania + pulse |
| `Switch.GetRecords` | Historia operacji |

`id` przekaźnika: `1 ~ max`, gdzie max zależy od produktu. BASIC-1GSP jest jednokanałowy,
więc `id: 1`.

## Set / Toggle / GetStatus

```json
{"jsonrpc":"2.0","id":1,"src":"user_1","method":"Switch.Set","params":{"id":1,"on":true}}
```

`GetStatus` zwraca po prostu `{"id":1,"on":true}`.

## Config

```jsonc
{
  "startup": {
    "startup": "stay",        // on | off | stay (ostatni stan) | invert
    "enable_delay": false,
    "delay_width": 500        // ms, wielokrotność 500, zakres 0 ~ 3599500
  },
  "pulse": {
    "enable": false,
    "on": false,              // stan PO zakończeniu impulsu
    "width": 5000             // ms, wielokrotność 500, od 0,5 s do 23:59:59.5; 0 gdy enable=false
  }
}
```

`enable_delay` i `delay_width` są w dokumentacji opisane jako „(implement as needed)" —
czyli opcjonalne po stronie firmware'u. [DO SPRAWDZENIA] czy BASIC-1GSP je faktycznie realizuje.

## GetRecords

Parametry: `offset` (0 = od najnowszego), `count` (max 100, ale **zalecane ≤ 30 na pakiet**).
Zwraca `next_offset`, `total_count`, `records[]`.

Rekord: `id`, `on`, `ts` (**timestamp w ms**) oraz `trigger_type` — i to jest ciekawe pole,
bo mówi **dlaczego** przekaźnik zmienił stan:

```
button · input · fastgo · timer · startup · pulse · fastScene (auto power-off)
Overcurrent protection · Overpower protection · Overvoltage protection
Undervoltage protection · overheat protection · Auto Recovery
eWeLink · Matter · Mqtt · websocket · http
```

⚠️ Wartości są **niespójne w wielkości liter** (`button` małą, `Overcurrent protection` wielką,
`Mqtt` / `websocket` / `http` różnie). Porównywać case-insensitive.

To jest realne złoto do diagnostyki: widać czy odcięcie było ręczne, z timera, czy z ochrony
nadprądowej.

## Notyfikacje

- `NotifyStatus` przy zmianie stanu → `{id, on}`
- `NotifyEvent` z `event: "config_changed"` przy zmianie konfiguracji — wtedy trzeba samemu
  dociągnąć `Switch.GetConfig`

```json
{
  "src": "sonoffmini1gsp-acebe61fae74",
  "dst": "web",
  "method": "NotifyEvent",
  "params": { "ts": 1626221112, "events": [ { "component": "switch:1", "id": 1, "event": "config_changed", "ts": 1626221112 } ] }
}
```

Zwróć uwagę na format `component`: `"switch:1"` — nazwa komponentu z indeksem instancji.

## Sterowanie po MQTT (uproszczone, bez RPC)

Wymaga `config.enable = true` w komponencie MQTT. `<topic_root>` domyślnie = ID urządzenia.

| Kierunek | Topik | Payload |
|---|---|---|
| subscribe | `<topic_root>/status` | pola `switch`, `power_on_behavior`, `startup_delay_enable`, `startup_delay_time` |
| publish | `<topic_root>/cmd/switch` | `ON`/`OFF`/`true`/`false`/`1`/`0` |
| publish | `<topic_root>/cmd/power_on_behavior` | `off`/`on`/`stay`/`invert` |
| publish | `<topic_root>/cmd/power_on_delay` | boolean |
| publish | `<topic_root>/cmd/power_on_delay_time` | `0` – `3599.5`, **sekundy**, krok 0.5 |

🔴 **Topiki komendowe nie zwracają odpowiedzi.** Potwierdzeniem wykonania jest aktualizacja
odpowiedniego pola w `<topic_root>/status`. Nieprawidłowe payloady mogą być po cichu
zignorowane.

Zależność przy opóźnieniu startu: najpierw `power_on_delay` = ON i potwierdzenie, że
`startup_delay_enable` to `true`, **dopiero potem** `power_on_delay_time`. Gdy opóźnienie nie
jest włączone, urządzenie ignoruje ustawianie czasu.

Uwaga na jednostki: w RPC `delay_width` jest w **milisekundach**, w MQTT `power_on_delay_time`
w **sekundach**. Łatwo się pomylić o trzy rzędy wielkości.

---

# Meter — pomiar energii

| Metoda | Opis |
|---|---|
| `Meter.GetStatus` | Parametry chwilowe + liczniki |
| `Meter.GetConfig` / `SetConfig` | Odwrócenie pomiaru + taryfy |
| `Meter.GetRecords` | Historia po godzinach / dniach / miesiącach |
| `Meter.Download` | Masowe pobranie historii |
| `Meter.ResetCounters` | ⚠️ Kasuje liczniki, koszty **i historię** |

## 🔴 Jednostki — wszystko przeskalowane

**Nic nie jest w jednostkach SI.** Zawsze dzielić:

| Pole | Jednostka | Konwersja |
|---|---|---|
| `voltage` | V, precyzja 0,01 | `/ 100` |
| `current` | A, precyzja 0,01 | `/ 100` |
| `power` | W, precyzja 0,01 | `/ 100` |
| `freq` | Hz, precyzja 0,01 | `/ 100` |
| `total_energy` | 0,01 kWh | `/ 100` |
| `total_supply` | 0,01 kWh | `/ 100` |
| `day_energy`, `day_supply` | 0,01 kWh | `/ 100` |
| `month_energy`, `month_supply` | 0,01 kWh | `/ 100` |
| `day_cost`, `month_cost` | 0,01 waluty (grosz) | `/ 100` |
| `day_run_time_sec`, `total_run_time_sec` | **sekundy** | bez zmian |

`*_supply` to energia **oddana** (przepływ wsteczny) — istotne przy fotowoltaice.
`*_energy` to pobór „w przód".

Przykład z dokumentacji: `"voltage": 22000` = **220,00 V**, `"freq": 5000` = **50,00 Hz**,
`"power": 1100000` = **11 000,00 W**.

## `Meter.GetStatus` — pełna odpowiedź [DOK]

```json
{
  "id": 1,
  "src": "sonoffmini1gsp-acebe61fae74",
  "dst": "user_1",
  "result": {
    "id": 1,
    "voltage": 22000,
    "current": 5000,
    "power": 1100000,
    "freq": 5000,
    "total_energy": 12345,
    "total_supply": 0,
    "day_energy": 120,
    "day_supply": 0,
    "day_cost": 60,
    "day_run_time_sec": 43200,
    "month_energy": 3500,
    "month_supply": 0,
    "month_cost": 1600,
    "total_run_time_sec": 864000
  }
}
```

## Notyfikacja statusu

Leci **przy zmianie stanu mocy** — czyli sama z siebie, bez pollingu. Struktura jest
zagnieżdżona głębiej niż przy Switch, uwaga na ścieżkę `params.meter.status`:

```json
{
  "src": "sonoffmini1gsp-acebe61fae74",
  "dst": "user_1",
  "method": "NotifyStatus",
  "params": {
    "ts": 1776657162,
    "meter": {
      "status": {
        "id": 1, "voltage": 21972, "current": 7, "power": 828, "freq": 4988,
        "total_energy": 0, "total_supply": 0, "day_energy": 0, "day_supply": 0,
        "day_cost": 0, "month_energy": 0, "month_supply": 0, "month_cost": 0,
        "day_run_time_sec": 148, "total_run_time_sec": 1911
      }
    }
  }
}
```

Tu `current: 7` = 0,07 A, `power: 828` = 8,28 W — realistyczne czuwanie.

**[DO SPRAWDZENIA]** jaki jest próg wyzwalania tej notyfikacji i jak często potrafi lecieć
przy zmiennym obciążeniu. Jeśli za rzadko — trzeba dołożyć polling `Meter.GetStatus`.

## Config i taryfy

```jsonc
{
  "reversed": true,              // odwrócenie kierunku pomiaru, domyślnie false
  "price_info": {
    "effective_time": "2026-03-01",  // YYYY-MM-DD, wchodzi o północy;
                                     // zakres: 30 dni wstecz do 30 dni w przód
    "normal_price": 55,              // cena bazowa, jednostka 0,01
    "use_weekend": true,             // czy rozróżniać weekendy
    "workday_slots": [               // max 5 przedziałów
      { "start": 0,  "end": 18, "price": 100 },
      { "start": 18, "end": 24, "price": 50 }
    ],
    "weekend_slots": [ { "start": 0, "end": 24, "price": 60 } ]
  }
}
```

`start`/`end` to **pełne godziny**. Max 5 przedziałów na każdą z list. Przydatne przy taryfie
G12 — ale liczenie kosztu i tak lepiej robić po swojej stronie, urządzenie ma to tylko dla
własnego wyświetlania.

## `Meter.GetRecords` — historia

| Parametr | Opis |
|---|---|
| `id` | Instancja komponentu |
| `type` | `hour` / `day` / `month` |
| `start_time`, `end_time` | `YYYY-MM-DD` |

Kwirki: przy `type: "hour"` używany jest **tylko `start_time`** i zwracane są godzinowe dane
z tego jednego dnia. Przy `type: "month"` część dzienna daty jest ignorowana.

Zwraca `records[]` z obiektami: `energy_unit` (0,01 kWh), `supply_unit` (0,01 kWh),
`cost_cent` (0,01 waluty). **Rekordy nie mają własnego znacznika czasu** — pozycja w tablicy
odpowiada kolejnej godzinie/dniu/miesiącowi zakresu.

## `Meter.Download` — masowy backfill

| Parametr | Opis |
|---|---|
| `type` | **number**: `0` = po godzinach, `1` = po dniach |
| `offset` | Indeks startowy, `0` = od najnowszego |
| `count` | **Max 32 na pakiet** |

Zwraca `next_offset`, `total_count` oraz `entries` — **tablicę tablic**, każdy wpis to
`[energy_unit, supply_unit, cost_cent]`:

```json
{ "id": 1, "next_offset": 2, "total_count": 5000, "entries": [ [10,0,5], [12,0,6] ] }
```

⚠️ Niespójność między metodami: w `GetRecords` `type` to **string** (`"hour"`), w `Download`
to **number** (`0`). Łatwo się naciąć.

`total_count: 5000` w przykładzie — przy 180 dniach historii godzinowej wychodzi ~4320 wpisów,
więc rząd wielkości się zgadza. Backfill całości = ~135 pakietów po 32.

## `Meter.ResetCounters`

Kasuje energię, koszty **oraz dane historyczne**. Nieodwracalne. Nie wołać przypadkiem.
