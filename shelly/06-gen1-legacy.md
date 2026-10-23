# Gen1 API — stare urządzenia

Źródło: `shelly-api-docs.shelly.cloud/gen1/` (streszczone, nie czytane strona po stronie —
oznaczam to uczciwie, bo reszta notatek jest przepisywana dosłownie).

## Po co ta notatka

Duża część poradników i bibliotek w sieci dotyczy **Gen1**. To zupełnie inne API niż Gen2+.
Jeśli trafisz na przykład z `/relay/0?turn=on` bez `/rpc` — to jest Gen1.

## Różnice

| | Gen1 | Gen2+ |
|---|---|---|
| Styl | REST, zasób per ścieżka | JSON-RPC 2.0 na `/rpc` |
| Autoryzacja | **Basic HTTP**, domyślnie **wyłączona** | Digest SHA-256, RFC 7616 |
| Push | **CoIoT/CoAP** | WebSocket / MQTT / UDP |
| Topiki MQTT | `shellies/<model>-<id>/<funkcja>` | `<device_id>/rpc`, `/events/rpc`, `/status/...` |
| mDNS | `_http._tcp` | `_http._tcp` + `_shelly._tcp`, TXT `gen=2`/`gen=3` |
| Skrypty | brak | JavaScript na urządzeniu |

## Endpointy HTTP

| Ścieżka | Opis |
|---|---|
| `/shelly` | Info o urządzeniu (typ, MAC, firmware) — **bez autoryzacji, tak jak w Gen2+** |
| `/settings` | Konfiguracja globalna (Wi-Fi, MQTT, cloud, strefa czasowa) |
| `/settings/ap`, `/settings/sta` | Wi-Fi AP / klient |
| `/settings/login` | Konfiguracja autoryzacji: `enabled`, `username`, `password` |
| `/status` | Stan urządzenia (połączenie, uptime, pamięć) |
| `/relay/<index>` | Sterowanie i status przekaźnika |
| `/light/0`, `/roller/<index>`, `/meter/<index>` | Specyficzne dla typu urządzenia |
| `/ota`, `/ota/check` | Aktualizacja firmware |
| `/reboot` | Restart |

Parametry w query stringu lub formularzu, odpowiedzi w JSON. Wartości logiczne przyjmowane
w wielu formach: `1`, `true`, `y` itp.

## CoIoT / CoAP

Protokół oparty na CoAP z rozszerzeniami JSON. Urządzenia rozgłaszają status multicastem na
`/cit/s` **co 15–30 sekund**. To był mechanizm push Gen1 — dziś zastąpiony przez WS/MQTT.

Home Assistant nadal używa CoIoT dla urządzeń Gen1.

## MQTT

- Topiki: `shellies/<model>-<deviceid>/<funkcja>`
- Komendy globalne: `shellies/command` (np. `announce`, `update`)
- LWT: `shellies/<model>-<deviceid>/online`

Zupełnie inny układ niż Gen2+. Skrypt `mqtt-announce.js` na Gen2+ dorabia format Gen1,
żeby w sieci mieszanej wszystko wyglądało jednolicie.

## Most między generacjami

W dokumentacji Gen2+ jest strona `gen2/General/gen1Compatibility` (nieczytana) — opisuje
warstwę zgodności. Widać ją m.in. w endpointcie `/relay/<id>` obecnym na Gen2+.
