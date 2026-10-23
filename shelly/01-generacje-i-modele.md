# Generacje Shelly i wykrywanie urządzeń

## Podział

| Generacja | API | Przykłady |
|---|---|---|
| **Gen 1** | REST (`/status`, `/relay/0`) + CoIoT/CoAP | stare Shelly 1, 2.5, Plug S |
| **Gen 2+** | JSON-RPC 2.0 na `/rpc` | serie **Plus** i **Pro** |
| **Gen 3, Gen 4** | to samo co Gen 2 | nowsze modele |

„Gen2+" w dokumentacji obejmuje generacje 2, 3 i 4 — **to jedno wspólne API**, różnice
sprowadzają się do dostępnych komponentów. Dokumentacja Gen1 żyje w osobnym drzewie
(`/gen1/`) i to zupełnie inny świat — plik [06](06-gen1-legacy.md).

## mDNS — wykrywanie w sieci [DOK]

Gen2+ ogłasza **dwie** usługi:

| Usługa | Opis |
|---|---|
| `_http._tcp` | serwer WWW na porcie 80 — ogłaszany **także przez Gen1** |
| `_shelly._tcp` | usługa specyficzna dla Gen2+ |

W rekordzie TXT obu usług jest klucz **`gen=2` lub `gen=3`**. To pozwala odróżnić generację
nawet gdy szukasz po `_http._tcp`, gdzie mieszają się stare i nowe.

Hostname urządzenia ma postać `shelly<model>-<mac>`, np. `shellypro4pm-f008d1d8b8b8` —
i **to samo jest `device_id` oraz `realm` przy autoryzacji**.

Porównanie: Sonoff w nowym API **nie ma mDNS** (IP trzeba wziąć z routera), a w starym DIY
ogłaszał się jako `_ewelink._tcp`. Shelly jest tu wygodniejszy — discovery działa out of the box.

## Szybka identyfikacja urządzenia

Endpoint **`/shelly`** działa **bez autoryzacji** na obu generacjach — to najprostszy sposób,
żeby dowiedzieć się z czym masz do czynienia:

```bash
curl http://${SHELLY}/shelly
```

Dla Gen2+ pełne dane daje `Shelly.GetDeviceInfo`:

| Pole | Opis |
|---|---|
| `id` | ID urządzenia = hostname = realm |
| `mac` | MAC |
| `model` | Model |
| **`gen`** | **Generacja — po tym rozgałęziać logikę klienta** |
| `fw_id` / `ver` | Firmware |
| `app` | Nazwa aplikacji |
| `profile` | Profil (tylko urządzenia wieloprofilowe) |
| `auth_en` | Czy autoryzacja włączona |
| `auth_domain` | Domena, `null` gdy wyłączona |
| `discoverable` | Obecne **tylko gdy `false`** — urządzenie ukryte |
| `provision` | Stan provisioningu (od 1.7.5), patrz niżej |

Z parametrem `ident: true` dochodzą `key` (cloud key, JWT), `batch`, `fw_sbits`.

## Provisioning — okna czasowe [DOK]

Od firmware 1.7.5 pole `provision` mówi, w jakim stanie jest świeże urządzenie:

| Stan | Znaczenie |
|---|---|
| `pending` | Stan początkowy: otwarty AP i BLE RPC dostępne przez **15 minut** (3 min na bateryjnych) |
| `confirmed` | Po pierwszym uzyskaniu IP: AP i BLE RPC przez **5 minut** karencji |
| `complete` | Karencja minęła — AP (o ile nie ma hasła) i BLE RPC wyłączone |
| `locked` | Okno minęło bez wejścia online — AP i BLE RPC wyłączone do czasu przepięcia zasilania, resetu albo wciśnięcia przycisku |

⚠️ Praktyczna konsekwencja: **świeże urządzenie ma ograniczony czas na konfigurację.**
Jeśli je rozpakujesz i zostawisz na tydzień, wpadnie w `locked` i będzie wymagało
przepięcia zasilania.

## Profile — urządzenia wieloprofilowe

Niektóre modele (Plus2PM, Pro2PM) działają w wykluczających się trybach — np. **switch** albo
**cover** (roleta). Metody: `Shelly.ListProfiles`, `Shelly.SetProfile`.

🔴 **`Shelly.SetProfile` kasuje wszystkie webhooki i harmonogramy na urządzeniu**, a zmiana
na inny profil wymusza automatyczny reboot. Dokumentacja oznacza to jako DANGER.

Profil determinuje, które komponenty są w ogóle widoczne — ten sam sprzęt raz wystawia
`switch:0` i `switch:1`, a raz `cover:0`.

## Limity, o których warto wiedzieć [DOK]

| Limit | Wartość |
|---|---|
| Równoległe nietrwałe kanały RPC | **6** |
| Zadania harmonogramu | 20 (przekroczenie → błąd `-108`) |
| Śledzone nonce'y (fw 2.0.0+) | 32 w buforze cyklicznym |
| Żądań na jeden nonce | 30 000 albo 1 godzina |
