# Skrypty na urządzeniu — przewaga Shelly

Źródło: `gen2/Scripts/Overview`.

## O co chodzi

**Urządzenia Shelly uruchamiają JavaScript bezpośrednio na sobie.** Bez chmury, bez serwera
pośredniczącego, bez Home Assistanta. Środowisko to zmodyfikowany **Espruino** — blisko
standardu JS, zoptymalizowany pod systemy wbudowane.

To jest funkcja, której **Sonoff nie ma w ogóle**. Zmienia projektowanie systemu: logikę,
która musi działać zawsze (także gdy padnie serwer), można zejść na samo urządzenie.

## Co skrypty potrafią [DOK]

- automatyzować zachowanie urządzenia (reakcja na zdarzenia)
- **tworzyć endpointy HTTP** — urządzenie staje się mini serwerem WWW
- integrować z usługami zewnętrznymi (żądania HTTP, publikowanie MQTT)
- sterować **komponentami wirtualnymi** (wirtualne przełączniki, czujniki)
- przetwarzać dane z czujników, w tym odczyty Bluetooth
- planować akcje timerami

## Cykl życia skryptu

```bash
# 1. utwórz slot
http://192.168.33.1/rpc/Script.Create?name="my_first_script"
# → {"id": 1}

# 2. wgraj kod
http://192.168.33.1/rpc/Script.PutCode?id=1&code="console.log('Hello Shelly!');"

# 3. uruchom
http://192.168.33.1/rpc/Script.Start?id=1

# 4. autostart po bootcie
http://192.168.33.1/rpc/Script.SetConfig?id=1&config={"enable":true}
```

Pełne zarządzanie (lista, status, błędy, pobranie kodu, stop, usunięcie) — komponent `Script`,
strona nieczytana.

## Wzorce z dokumentacji

### Reakcja na zdarzenie

```js
Shelly.addEventHandler(function (event) {
  if (event.component === "input:0" && event.info.event === "single_push") {
    Shelly.call("Switch.Toggle", { id: 0 });
    console.log("Button pressed - toggling switch");
  }
});
```

### Automatyka na podstawie pomiaru

```js
Timer.set(60000, true, function () {
  let status = Shelly.getComponentStatus("temperature", 0);
  if (status && status.tC > 28) {
    Shelly.call("Switch.Set", { id: 0, on: true });
  } else if (status && status.tC < 25) {
    Shelly.call("Switch.Set", { id: 0, on: false });
  }
});
```

Zwróć uwagę na **`Shelly.getComponentStatus`** — wariant **synchroniczny**, w odróżnieniu od
asynchronicznego `Shelly.call`. Dokumentacja MCP wspomina, że dla `GetStatus`/`GetConfig`
warto używać synchronicznych odpowiedników.

### Wysyłka HTTP

```js
function sendAlert(message) {
  Shelly.call("HTTP.Request", {
    method: "POST",
    url: "https://your-webhook.com/alert",
    body: JSON.stringify({
      device: Shelly.getDeviceInfo().id,
      message: message,
      timestamp: Date.now(),
    }),
    headers: { "Content-Type": "application/json" },
  });
}
```

## Debugowanie

Włącz logi po WebSocket:

```
http://192.168.33.1/rpc/Sys.SetConfig?config={"debug":{"websocket":{"enable":true}}}
```

Potem:

```bash
wscat --connect ws://YOUR_SHELLY_IP/debug/log
```

## Repozytorium przykładów: `shelly-script-examples`

Gotowce wskazane przez producenta:

**BLE / Shelly BLU:**
| Skrypt | Opis |
|---|---|
| `ble-shelly-blu.js` | Uniwersalny skaner BLU, dekoduje BTHome i emituje zdarzenia dla innych skryptów |
| `ble-events-handler.js` | Obsługa zdarzeń BLU z konfigurowalnymi automatyzacjami |
| `ble-shelly-btn-gateway.js` | Gen2 jako bramka między BLU Button a innymi Shelly |

**Moc i energia:**
| Skrypt | Opis |
|---|---|
| `load-shedding.js` | Automatyczne zrzucanie obciążenia po przekroczeniu progów mocy (Pro4PM / Pro3EM) |
| `consume-limited-power.js` | Wyłączenie po zużyciu zadanej ilości energii |
| `failure-monitor.js` | Alarm gdy moc odbiornika spada do zera mimo załączonego przekaźnika |

**MQTT / Home Assistant:**
| Skrypt | Opis |
|---|---|
| `mqtt-discovery.js` | **Autodiscovery MQTT dla Home Assistanta** — to, co Sonoff ma wbudowane jako `enable_ha` |
| `mqtt-announce.js` | Zgodność z formatem MQTT Gen1 dla sieci mieszanych |

**Zaawansowane:**
| Skrypt | Opis |
|---|---|
| `scene.js` | Odtwarzacz scen z sekwencją akcji, opóźnieniami i warunkami |
| `remoterpc.js` | Abstrakcja zdalnego Shelly — wywoływanie RPC na innych urządzeniach |
| `telegram-interaction.js` | Framework bota Telegram z parsowaniem komend |
| `router-watchdog.js` | Monitorowanie łączności |

## Gdzie to realnie pomaga

`failure-monitor.js` i `load-shedding.js` to dokładnie ten typ logiki, który **nie powinien**
zależeć od serwera. Jeśli kolektor w Node padnie albo Wi-Fi się urwie, skrypt na urządzeniu
działa dalej.

[DEDUKCJA] Sensowny podział: twarde bezpieczeństwo i reakcje czasu rzeczywistego → skrypt na
urządzeniu; zbieranie danych, historia, wizualizacja, korelacja między urządzeniami →
serwer.

## Do doczytania

- `gen2/Scripts/LanguageReference` — na ile to odbiega od standardowego JS
- `gen2/Scripts/Tutorial`
- `gen2/Scripts/APIs/*` — pełne API skryptowe (jest tam m.in. AES)
- komponent `Script` — limity: ile slotów, ile pamięci na skrypt
- komponent `KVS` — trwały storage key-value, naturalny partner skryptów
