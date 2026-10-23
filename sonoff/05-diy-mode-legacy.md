# Stare DIY Mode API — żeby nie pomylić

Źródło: `https://help.sonoff.tech/docs/DIY-MODE-API-PROTOCOL` (protokół v1.4, repozytorium
`github.com/itead/Sonoff_Devices_DIY_Tools`).

## Po co ta notatka

W internecie 90 % poradników o „Sonoff local API" dotyczy **tego** protokołu. Jest inny od
nowego RPC z pliku [02](02-api-rpc-lokalne.md) i mieszanie ich to pewna strata czasu.

## Różnice

| | Stare DIY API | Nowe RPC API |
|---|---|---|
| Port | **8081** | 80 |
| Ścieżka | `/zeroconf/<akcja>` | `/rpc` |
| Format | własny JSON (`deviceid`, `data`, `seq`, `error`) | **JSON-RPC 2.0** |
| Discovery | **mDNS `_ewelink._tcp`** | brak (IP z routera / panelu WWW) |
| Autoryzacja | opcjonalne szyfrowanie danych (błąd 401 gdy włączone) | digest SHA-256 RFC 7616 |
| Notyfikacje | brak | `NotifyStatus` / `NotifyEvent` po WS i MQTT |
| MQTT | nie | tak, z autodiscovery HA |
| Rate limit | **≥ 200 ms między żądaniami** | nieudokumentowany |

## Urządzenia [DOK]

BASICR3, RFR3, MINI / MINIR2, MINIR3, D1 (ściemniacz), SPM-Main (licznik),
B02-BL-A60, B05-BL (żarówki).

**BASIC-1GSP nie jest na tej liście** — ale to nic nie znaczy, bo lista dotyczy starej rodziny.

## Włączenie trybu DIY (wtyczki)

1. Przytrzymać przycisk parowania 5 s
2. Połączyć się z hotspotem `ITEAD-XXXXXXXXXX`, hasło `12345678`
3. Wejść na `http://10.10.7.1/`
4. Podać dane Wi-Fi

## Kształt żądania

```
POST http://<ip>:8081/<ścieżka>
{ "deviceid": "...", "data": {} }
```

`data` puste przy odpytywaniu. Odpowiedź zawiera `seq`, `error` i `data`.

## Wniosek

Jeśli BASIC-1GSP nie odpowie na `/rpc`, **warto sprawdzić też port 8081** zanim uzna się, że
lokalnego API nie ma. Ale biorąc pod uwagę, że BASIC-1GSP to sprzęt Matter z nowego rozdania,
[DEDUKCJA] szanse na stare DIY API są niewielkie.
