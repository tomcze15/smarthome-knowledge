# Szczera ocena modelu biznesowego

Spisane 2026-08-17 na prośbę o brutalną szczerość. Pytanie brzmiało: czy da się zbudować
firmę odsprzedając „niebezpieczne" urządzenia klasy 2–3 od Sonoffa/Shelly, a brandując
własnym logo proste klasy 0–1.

**[DEDUKCJA] — to jest opinia, nie fakt z dokumentacji. Waż ją odpowiednio.**

---

## Werdykt w jednym zdaniu

**Strategia doboru sprzętu jest poprawna. Ale to jest najmniej istotna część tego biznesu
i jednocześnie ta, która najłatwiej zje Ci kapitał i uwagę, jeśli zrobisz ją za wcześnie.**

---

## Co jest dobre — i to naprawdę

1. **Local-first to prawdziwa, obronna przewaga.** Google i Amazon **strukturalnie nie mogą**
   tego zaoferować, bo ich model biznesowy stoi na danych z chmury. To nie jest przewaga,
   którą Ci skopiują — to przewaga wynikająca z ich konfliktu interesów.
2. **Nie konkurujesz szerokością asortymentu** — sekcja 1 Twojej specyfikacji jest mądra
   i sam do tego doszedłeś przed naszą rozmową.
3. **Warstwa integracyjna to właściwe miejsce na wartość.** Hub, który spina bogaty świat
   lokalny z ekosystemami bez chmury, jest czymś, czego klient nie złoży sam.
4. **Timing na prywatność jest dobry** — świadomość rośnie, a UE reguluje w tę stronę.
5. Klasy ryzyka 0–1 do brandowania — dobra, ostrożna decyzja.

---

## 🔴 Co mnie martwi — uczciwie

### 1. Brandowanie sprzętu daje mniej, niż się wydaje

Policz na chłodno, co realnie dostajesz brandując licznik Modbus:

| Dostajesz | Płacisz |
|---|---|
| marżę na sprzęcie (cienką) | MOQ 500–1000 szt. = **kapitał zamrożony na starcie** |
| „wyglądamy poważniej" | certyfikacja LVD/EMC na siebie |
| — | magazyn, logistyka, RMA |
| — | odpowiedzialność producenta w rozumieniu CE |
| **lock-in: praktycznie żaden** | uwagę odciągniętą od softu |

Licznik Modbus jest **wymienny**. Nikt nie zostaje Twoim klientem dlatego, że ma Twoje logo
na liczniku. Zostaje, bo działa mu hub i automatyzacje.

🔴 **Brandowanie sprzętu jest kuszące, bo daje poczucie „prawdziwej firmy". Ale zjada
dokładnie te zasoby — gotówkę, czas, uwagę — których potrzebuje software, czyli jedyna
rzecz, która Cię naprawdę różnicuje.**

### 2. Odsprzedaż cudzego sprzętu to biznes o cienkiej marży i grubym wsparciu

Dystrybutorzy pracują na kilkunastu–kilkudziesięciu procentach. A Ty przy tym **przejmujesz
RMA i wsparcie za cudzy sprzęt** — przy pakiecie VIP obiecującym „najwyższy priorytet
niezawodności".

Każda kombinacja marka × model × wersja firmware'u to osobna komórka w macierzy wsparcia.
Przy heterogenicznym sprzęcie ta macierz rośnie szybciej niż przychody.

### 3. Największe ryzyko całego modelu nie leży w sprzęcie — leży w subskrypcji

Twoje pakiety BASIC/COMFORT/PRO/VIP zakładają **przychód powtarzalny od klienta B2C
w Polsce**. To jest najtrudniejsza część, nie hardware.

Polski klient detaliczny **stawia opór abonamentom za rzeczy domowe.** Sprzedasz mu
jednorazowo sprzęt i montaż. Przekonanie go do stałej opłaty za coś, co „przecież już
kupił i działa lokalnie" — to jest realna bariera. I ironia jest taka, że **im lepiej
zrobisz local-first, tym trudniej uzasadnić abonament**: skoro działa bez Was, to za co
płacę co miesiąc?

**To pytanie trzeba mieć rozwiązane w narracji sprzedażowej, zanim zamówisz jakikolwiek
sprzęt z własnym logo.**

### 4. Rzeczy, o których łatwo zapomnieć na etapie planowania

- **Uprawnienia SEP** — montaż w rozdzielnicy to praca elektryczna. Musisz mieć
  uprawnionego człowieka. To nie jest opcjonalne
- **Ubezpieczenie OC działalności** — jeśli Twój system jest choćby wspomniany przy
  pożarze instalacji, wchodzisz w spór. Dotyczy to **także odsprzedaży**, nie tylko
  własnej marki
- **Serwis po latach** — sprzęt schodzi z produkcji szybciej niż klient wymienia instalację

---

## 🎯 Co bym zmienił: kolejność, nie strategię

### Faza 1 — tylko software i hub (teraz)

Sprzedajesz **hub + wdrożenie + abonament**. Sprzęt peryferyjny klient kupuje sam
z Twojej listy klasy A, albo odsprzedajesz z małym narzutem, bez magazynu.

**Cel fazy: udowodnić, że ktoś zapłaci drugi raz.** Nie „czy da się zbudować", tylko
„czy ktoś przedłuży abonament po roku". Dopóki tego nie wiesz, każda złotówka w sprzęt
jest zakładem w ciemno.

### Faza 2 — brand na hubie, nie na liczniku

🎯 **Pierwszym produktem pod Twoją marką powinien być hub, nie miernik.**

- jest **klasy 0** — niskie napięcie, zero ryzyka elektrycznego
- to jedyny element, który klient **utożsamia z Twoim produktem**
- ma **naturalny lock-in** — na nim stoi Twój software
- obudowa Odroida z Twoim logo to koszt rzędu setek złotych, nie dziesiątek tysięcy
- **nie wymaga MOQ 1000 sztuk**

Brandujesz to, co i tak jest Twoje. Marża i tak jest w abonamencie.

### Faza 3 — licznik dopiero wtedy, gdy znasz swój najczęstszy SKU

Gdy po 50–100 wdrożeniach wiesz dokładnie, którego licznika używasz za każdym razem —
wtedy MOQ przestaje być zakładem, a staje się optymalizacją kosztu.

Zamawianie 800 sztuk przed tą wiedzą to ryzyko, że kupisz 800 sztuk czegoś, czego
przestaniesz używać po trzecim wdrożeniu.

---

## Podsumowanie bez owijania

**Czy da się z tego zrobić firmę?** Tak. Model jest sensowny, a local-first to prawdziwa
przewaga.

**Czy sprzęt pod własną marką jest do tego potrzebny na starcie?** Nie. To jest optymalizacja
etapu, którego jeszcze nie osiągnąłeś.

**Co jest realnym ryzykiem?** Nie dobór sprzętu — tylko **czy polski klient B2C zapłaci
abonament za system, który z definicji działa bez Ciebie.** Odpowiedź na to pytanie decyduje
o wszystkim innym i nie kosztuje ani złotówki, żeby ją sprawdzić: **wystarczy porozmawiać
z dwudziestoma potencjalnymi klientami, zanim zamówisz cokolwiek.**
