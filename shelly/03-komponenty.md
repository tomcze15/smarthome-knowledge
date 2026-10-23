# Komponenty Gen2+

Źródła: `gen2/ComponentsAndServices/` (wprowadzenie, `Switch`, `Shelly`, `PM1`),
`gen2/General/Notifications`.

## Koncepcja

Każdy **komponent** ma zawsze trzy metody:

```
Component.GetStatus     ← stan (zmienny, odczyt)
Component.GetConfig     ← konfiguracja (odczyt)
Component.SetConfig     ← konfiguracja (zapis)
```

Do tego metody specyficzne. **Usługi** (services) mogą nie mieć config/status.

Instancje identyfikowane jako `<typ>:<id>`, np. `switch:0`, `input:2`. Gdy typ występuje
w jednym egzemplarzu — sam typ, np. `sys`, `wifi`, `cloud`, `mqtt`.

⚠️ **Indeksy liczone od zera** (`switch:0`). U Sonoffa od jedynki (`id: 1`). Klasyczne
źródło pomyłek przy warstwie wspólnej.

## Pełna lista komponentów [DOK]

Dostępność zależy od modelu i profilu.

**Wyjścia i sterowanie:** `Switch` · `Cover` · `Light` · `CCT` · `RGB` · `RGBW` · `RGBCCT` · `DALI`

**Pomiary:** `PM1` · `EM` · `EM1` · `EMData` · `EM1Data` · `Temperature` · `Humidity` ·
`Illuminance` · `Voltmeter` · `DevicePower`

**Czujniki:** `Flood` · `Smoke` · `Presence` · `PresenceZone` · `Pill`

**Wejścia:** `Input`

**Łączność:** `WiFi` · `Eth` · `BLE` · `Cloud` · `Mqtt` · `Ws` · `Matter` · `Zigbee` ·
`Modbus` · `MbRtuClient` · `Serial`

**Systemowe / usługi:** `Shelly` · `Sys` · `Script` · `Schedule` · `Webhook` · `KVS` ·
`HTTP` · `CB` · `XMOD`

To jest dużo szerzej niż Sonoff (`Sonoff`, `System`, `Cloud`, `Input`, `Matter`, `Meter`,
`MQTT`, `Ota`, `Switch`, `Timer`, `WebSocket`, `WiFi`).

Warte szczególnej uwagi, bo nie mają odpowiednika u Sonoffa: **`Script`** (JS na urządzeniu),
**`KVS`** (trwały key-value store na urządzeniu), **`Webhook`** (wychodzące HTTP przy
zdarzeniach), **`Schedule`**, **`Modbus`**, **`Zigbee`**.

---

## `Shelly` — usługa zarządzania urządzeniem

Wspólna dla wszystkich Gen2+.

| Metoda | Opis |
|---|---|
| `Shelly.GetStatus` | **Status wszystkich komponentów naraz** |
| `Shelly.GetConfig` | **Konfiguracja wszystkich komponentów naraz** |
| `Shelly.ListMethods` | Lista dostępnych metod — z uwzględnieniem ACL i autoryzacji dla tego kanału/użytkownika |
| `Shelly.GetDeviceInfo` | Info o urządzeniu (bez auth) |
| `Shelly.GetComponents` | Lista komponentów |
| `Shelly.ListProfiles` / `SetProfile` | Profile (tylko urządzenia wieloprofilowe) |
| `Shelly.ListTimezones` | Strefy czasowe, z paginacją (`offset`, `total`) |
| `Shelly.DetectLocation` | Wykrywa lokalizację → `tz`, `lat`, `lon` |
| `Shelly.CheckForUpdate` / `Update` | Aktualizacja firmware |
| `Shelly.Reboot` | Restart |
| `Shelly.FactoryReset` | Reset fabryczny |
| `Shelly.ResetWiFiConfig` | Reset ustawień Wi-Fi |
| `Shelly.SetAuth` | Hasło |
| `Shelly.PutUserCA` / `PutTLSClientCert` / `PutTLSClientKey` | Certyfikaty klienta |
| `Shelly.PutHTTPServerCert` / `PutHTTPServerKey` / `PutHTTPServerCABundle` | Certyfikaty **serwera HTTPS urządzenia** |

Endpointy HTTP: **`/shelly`** (info, bez auth) i **`/ota`**.

🟢 **`Shelly.ListMethods` to bardzo dobre narzędzie do sondowania** — zwraca dokładnie to,
co dane urządzenie faktycznie potrafi. Sonoff czegoś takiego nie ma, tam trzeba strzelać
metodami i patrzeć na `-12803`.

🟢 **`Shelly.GetStatus` zwraca stan całego urządzenia jednym wywołaniem** — u Sonoffa trzeba
odpytywać komponent po komponencie.

---

## `Switch` — przekaźnik z opcjonalnym pomiarem

| Metoda | Opis |
|---|---|
| `Switch.Set` | `{id, on, toggle_after?, tag?}` → `{was_on}` |
| `Switch.Toggle` | `{id, tag?}` → `{was_on}` |
| `Switch.GetStatus` / `GetConfig` / `SetConfig` | standard |
| `Switch.ResetCounters` | `{id, type?}` — bez `type` kasuje wszystkie liczniki |

Dwa miłe drobiazgi, których Sonoff nie ma:

- **`toggle_after`** — timer flip-back w sekundach, prosto w `Set`
- **`tag`** — do 20 znaków identyfikujących **źródło** zmiany stanu; wraca potem w statusie

### Status — 🟢 jednostki SI, bez skalowania

| Pole | Jednostka |
|---|---|
| `output` | boolean |
| `source` | string — `init`, `WS_in`, `http`, `button`… |
| `tag` | string\|null |
| `apower` | **W** (moc czynna) |
| `voltage` | **V** |
| `current` | **A** |
| `pf` | współczynnik mocy |
| `freq` | **Hz** |
| `aenergy.total` | **Wh** |
| `aenergy.by_minute` | tablica **mWh** — trzy ostatnie pełne minuty |
| `aenergy.minute_ts` | timestamp początku bieżącej minuty (UTC) |
| `ret_aenergy.*` | to samo dla energii **oddanej** |
| `temperature.tC` / `.tF` | °C / °F, `null` gdy poza zakresem |
| `timer_started_at`, `timer_duration` | gdy timer aktywny |
| `errors` | tablica: `overtemp`, `overpower`, `overvoltage`, `undervoltage` |

🔴 **To jest największa praktyczna różnica wobec Sonoffa: Shelly podaje realne jednostki,
Sonoff wszystko przemnożone przez 100.** Warstwa wspólna musi normalizować.

Uwaga do `ret_aenergy`: energia dodana do tego licznika jest **także** dodawana do `aenergy`.
`aenergy` zbiera całość niezależnie od kierunku. Nie sumować obu.

`aenergy.by_minute` istnieje **tylko gdy zegar urządzenia jest zsynchronizowany**.

### Liczniki cyklu życia (`counts`)

`on_time` (minuty), `switch_on` (liczba przejść OFF→ON), `on_above_thr` (minuty powyżej progu
mocy) — każdy z własnym `*_rst_ts`. Włączane przez `counts.enable`, próg `counts.power_thr`
(domyślnie 100 W).

Przydatne do diagnostyki zużycia styków przekaźnika.

### Konfiguracja — wybrane

| Pole | Opis |
|---|---|
| `in_mode` | `momentary`, `follow`, `flip`, `detached`, `cycle`, `activate` |
| `in_locked` | ignoruje **wszystkie** zmiany z wejść fizycznych |
| `initial_state` | `off`, `on`, `restore_last`, `match_input` |
| `auto_on` / `auto_on_delay` | automatyczne włączenie po czasie (sekundy) |
| `auto_off` / `auto_off_delay` | automatyczne wyłączenie |
| `power_limit`, `voltage_limit`, `undervoltage_limit`, `current_limit` | progi ochronne; `null` = reset do domyślnej |
| `autorecover_voltage_errors` | przywróć stan po ustąpieniu błędu napięcia |
| `reverse` | odwrócenie kierunku pomiaru — **wymaga restartu** |

Znaczenie trybów wejścia:

- `follow` — stan przekaźnika = stan wejścia
- `flip` — zmiana wejścia przełącza przekaźnik
- `detached` — wejście nie wpływa na przekaźnik
- `momentary` / `cycle` — tylko gdy wejście jest bezstanowe (`type: button`)
- `follow` / `flip` — tylko gdy wejście ma stan (`type: switch`)
- `cycle` — tylko ShellyPlus2PM, Pro2PM, Pro2

### Endpoint zgodności z Gen1: `/relay/<id>`

`?turn=on|off|toggle&timer=<s>` → zwraca `ison`, `has_timer`, `timer_*`, `overpower`, `source`.
Ukłon w stronę starych integracji.

### Sterowanie po MQTT (od 0.14.0)

Subskrybuje `<topic_prefix>/command/switch:<id>`, publikuje na
`<topic_prefix>/status/switch:<id>` i `<topic_prefix>/error/switch:<id>`.

Komendy: `status_update`, `on[,<sekundy>]`, `off[,<sekundy>]`, `toggle`.

🟢 Shelly ma **osobny topik błędów** — Sonoff nie ma nic, tam nieprawidłowa komenda ginie
po cichu.

### Webhooki

Cztery zdarzenia: `switch.on`, `switch.off`, `switch.active_power_change`,
`switch.active_power_measurement`. Dwa ostatnie niosą atrybut `apower`, którego można użyć
do **webhooków warunkowych**.

### Modbus

Cewka pod adresem `100 + switch_id * 10`. Rejestry wejściowe od `33000`
(status, napięcie, prąd, moc, częstotliwość, PF, energia, energia zwrócona, cztery flagi
błędów), kolejny komponent co **+20**.

---

## `PM1` — samodzielny pomiar

Metody: `GetStatus`, `GetConfig`, `SetConfig`, `ResetCounters`.

Status: `voltage` (V), `current` (A), `apower` (W), `aprtpower` (VA, moc pozorna) i dalej.
Konfiguracja: `name`, `reverse` (wymaga restartu) oraz **`alarms`** — progi jako pary
`['under','over']` dla `voltage`, `current`, `power`; `null` wyłącza dany próg,
`alarms: null` wyłącza wszystkie.

Do liczników historycznych służą osobne komponenty **`EMData` / `EM1Data`** — nieczytane,
doczytać gdy będzie potrzebna historia.

---

## Notyfikacje

Trzy metody: **`NotifyStatus`**, **`NotifyFullStatus`**, **`NotifyEvent`**.

- nie da się odbierać po HTTP
- WS: wyślij najpierw ramkę z poprawnym `src`
- MQTT: subskrybuj `<shelly-id>/events/rpc`

### `NotifyStatus` — tylko delta

```json
{
  "src": "shellypro4pm-f008d1d8b8b8",
  "dst": "user_1",
  "method": "NotifyStatus",
  "params": {
    "ts": 1631186545.04,
    "switch:0": { "id": 0, "output": true, "source": "button" }
  }
}
```

🔴 **To jest delta, nie pełny stan.** Zamysł: nakładasz zmiany z `NotifyStatus` na znany
status i wynik ma być identyczny z tym, co zwróciłoby świeże `GetStatus`. Niezmienione
atrybuty mogą się w ogóle nie pojawić.

🔴 **Pułapka:** niektóre klucze istnieją tylko w określonych sytuacjach. Gdy taki klucz
znika, notyfikacja o zmianie zawiera go **z wartością `null`**. Czyli `null` znaczy
„usuń to pole", a nie „wartość wynosi null".

`ts` to unix timestamp UTC, **z częścią dziesiętną**.

### `NotifyFullStatus` (od 0.11.0)

Ta sama semantyka, ale payload zawiera **pełny stan wszystkich komponentów**. Emitowane:

- po wychodzącym kanale WS RPC — dla wszystkich urządzeń
- po wychodzącym WS, MQTT i wychodzącym UDP — dla urządzeń bateryjnych (usypiających)

Bardzo przydatne przy starcie kolektora — nie trzeba osobno wołać `Shelly.GetStatus`.

### `NotifyEvent`

Dla zdarzeń nieodzwierciedlonych w statusie. `params.events[]`, każdy element ma `ts`,
`component` (`switch:0`, `wifi`, `sys`), `id`, `event`.

Przykłady: `single_push` na `input:0`, `sys_btn_push` na `sys`, `config_changed` na dowolnym
komponencie (z dodatkowymi `restart_require` i `cfg_rev`).

🟢 **`cfg_rev`** w `sys` rośnie przy każdej zmianie konfiguracji — wygodny sposób na
wykrycie „coś się zmieniło, dociągnij configi" bez porównywania całości.
