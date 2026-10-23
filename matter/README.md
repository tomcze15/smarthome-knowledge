# Matter — baza wiedzy

Wszystko o standardzie Matter w jednym miejscu. Notatki pisane **prostym językiem**,
z przykładami. Rosną w miarę potrzeb — nie ma tu być wszystko od razu, ma być
**poprawnie i sprawdzalnie**.

---

## Spis

| Plik | O czym | Stan |
|---|---|---|
| [01-podstawy.md](01-podstawy.md) | Czym Matter jest i czym nie jest, role urządzeń, jak gadają, parowanie, fabric | ✅ gotowe |
| `02-role-urzadzen.md` | Node, kontroler, komisjoner, most, Thread Border Router — rozwinięcie | ⏳ |
| [03-commissioning.md](03-commissioning.md) | Parowanie krok po kroku: kody QR i ręczne, PASE/CASE, atestacja DAC, multi-admin, chip-tool, rozwiązywanie problemów | ✅ gotowe |
| `04-model-danych.md` | Klastry, atrybuty, komendy, zdarzenia, endpointy — z przykładami | ⏳ |
| `05-fabric-i-bezpieczenstwo.md` | Certyfikaty, multi-admin, Joint Fabric, reset i przekazanie urządzenia | ⏳ |
| `06-siec-i-transport.md` | IPv6, mDNS, UDP, groupcast, wymagania wobec routera | ⏳ |
| [07-pomiar-energii.md](07-pomiar-energii.md) | Pomiar mocy i energii przez Matter: klastry z 1.3, trzy warunki, które aplikacje to pokazują, jak sprawdzić urządzenie przed zakupem, porównanie listew | ✅ gotowe |
| `08-typy-urzadzen.md` | Device types, w tym energetyczne z Matter 1.4 | ⏳ |
| `09-most-w-hubie.md` | Jak zrobić most Matter we własnym hubie — praktyka | ⏳ |

⏳ = zaplanowane, jeszcze nie napisane. **Pusty plik jest gorszy niż jego brak** —
dlatego stoją tu tylko w spisie, a nie jako puste pliki w folderze.

---

## Źródło prawdy

**[Specyfikacje CSA — pobieranie](https://csa-iot.org/developer-resource/specifications-download-request/)**

Core Specification · Application Cluster Specification · Device Library Specification ·
Zigbee Cluster Library. Dostęp przez formularz, bezpłatnie.

Szczegóły co w którym dokumencie: [01-podstawy.md](01-podstawy.md), sekcja 8.

---

## Powiązane notatki poza tym folderem

- [../strategia-protokoly.md](../strategia-protokoly.md) — Matter kontra Thread kontra Zigbee,
  która warstwa jest która
- [../wyjasnienie-polnoc-poludnie.md](../wyjasnienie-polnoc-poludnie.md) — dlaczego Matter
  ma być interfejsem „na zewnątrz", a nie sposobem gadania z urządzeniami
- [../strategia-doboru-sprzetu.md](../strategia-doboru-sprzetu.md) — test odcięcia WAN
  i klasy A/B/C
