---
title: Symbole APRS
description: Kodowanie i interpretacja symboli APRS, tablice, nakładki, praktyczne zastosowania oraz pełne zestawienie 188 symboli.
template: doc
tableOfContents: true
---

Symbol APRS nie jest wyłącznie obrazkiem oznaczającym pozycję na mapie. Jest również **zwięzłym nośnikiem informacji o rodzaju, funkcji i właściwościach stacji lub obiektu**. Odpowiednio dobrany symbol może wskazać pojazd, digipeater czy domową stację, a nakładka doprecyzować na przykład sposób zasilania, funkcjonalność urządzenia albo charakter działań terenowych.

APRS przesyła **kod symbolu, a nie jego grafikę**. W typowych raportach pozycyjnych wystarczają dwa znaki: identyfikator tablicy i znak symbolu. Nakładka nie dodaje trzeciego znaku, lecz zajmuje miejsce identyfikatora tablicy alternatywnej. Odbiornik interpretuje otrzymaną kombinację według własnej tablicy symboli. Dzięki temu można przekazać dodatkową informację bez zwiększania długości raportu o kolejny znak.

## Kodowanie symbolu w raporcie pozycyjnym

W nieskompresowanym raporcie pozycyjnym znak tablicy występuje bezpośrednio po szerokości geograficznej, a znak symbolu po długości geograficznej:

```text
SQ9MDD>APRS:!5003.50N/01956.00E>
                     ^         ^
                     |         |
                  tablica    symbol
```

Para `/>` oznacza samochód: `/` wybiera tablicę podstawową, `>` określa symbol w tej tablicy. Przedstawiona linia jest tekstową reprezentacją pakietu, nie zapisem wszystkich bajtów radiowej ramki AX.25.

| Element | Rola |
|---|---|
| **Znak tablicy** | Wybiera tablicę podstawową, alternatywną albo wariant symbolu alternatywnego z nakładką. |
| **Znak symbolu** | Wskazuje pozycję w wybranej tablicy i, wspólnie z nakładką, określa znaczenie kodu. |
| **Grafika** | Jest przechowywana lub generowana przez aplikację odbierającą, nie jest nadawana w pakiecie. |

Symbol występuje także w innych formatach raportów pozycyjnych, między innymi skompresowanych i Mic-E. Umiejscowienie i sposób kodowania mogą być inne, ale nadal chodzi o identyfikację symbolu, a nie przesyłanie bitmapy.

## Tablice podstawowa i alternatywna

Dwie tablice APRS zawierają po 94 pozycje:

- `/` wybiera **tablicę podstawową**, używaną między innymi dla typowych stacji i pojazdów;
- `\` wybiera **tablicę alternatywną**, obejmującą również rodziny symboli rozszerzanych nakładkami.

Znaczenie ustala się na podstawie obu znaków. Przykładowo `/>` to samochód z tablicy podstawowej, natomiast `\>` jest bazowym symbolem pojazdu z tablicy alternatywnej. Podobnie `/-` oznacza dom z tablicy podstawowej, a `\-` jest symbolem alternatywnym, którego nakładki mogą opisywać charakterystykę domowej stacji.

## Nakładki i dodatkowe znaczenie

**Nakładka** (*overlay*) jest literą `A`-`Z` albo cyfrą `0`-`9`, która **zastępuje znak `\`** w kodzie symbolu alternatywnego. Nie jest dodatkowym polem i nie wydłuża pary znaków określających symbol.

```text
Tablica alternatywna:   \-    dom (symbol bazowy)
Z nakładką S:           S-    dom, zasilanie słoneczne

                        ^
                        nakładka zamiast znaku tablicy
```

Symbol bazowy tworzy kategorię, a nakładka może wskazać konkretną odmianę. Jej znaczenie **zależy od znaku symbolu**: `S-` dotyczy sposobu zasilania domu, `S;` oznacza działalność SOTA, a `S#` opisuje funkcjonalność digipeatera. Nie istnieje uniwersalny słownik, w którym każda litera znaczyłaby to samo dla wszystkich ikon.

Rozszerzenie z 2007 roku przewiduje nakładki dla wszystkich symboli alternatywnych, ale **nie każda kombinacja ma zdefiniowane znaczenie**. Nie należy samodzielnie nadawać jej nowej interpretacji ani zakładać, że inne aplikacje ją rozpoznają. Mechanizm pozwala na dużą liczbę kombinacji, lecz ich użyteczność zależy od uzgodnionych definicji i implementacji.

Nakładka była pierwotnie pomyślana jako cyfra lub litera nałożona na bazową ikonę. Dokumentacja dopuszcza jednak również narysowanie całkowicie odrębnej grafiki dla konkretnej kombinacji, jeżeli pozwala to czytelniej przekazać jej znaczenie.

## Praktyczne wykorzystanie symboli i nakładek

### Domowa stacja: rodzaj zasilania i obecność operatora

![Bazowy symbol domu z tablicy alternatywnej](./_img/verG/a12.gif)

Rodzina alternatywnego symbolu domu `\-` szczególnie dobrze pokazuje, że ikona może przekazywać więcej niż sam rodzaj obiektu. Według dostarczonego wykazu rozszerzeń możliwe są następujące kombinacje:

| Kod | Znaczenie |
|---|---|
| `\-` | Dom, alternatywny symbol bazowy; historycznie używany do oznaczania stacji HF. |
| `B-` | Zasilanie akumulatorowe lub praca poza siecią elektroenergetyczną. |
| `C-` | Połączone alternatywne źródła energii. |
| `E-` | Zasilanie awaryjne na wypadek zaniku zasilania sieciowego. |
| `G-` | Energia geotermalna. |
| `H-` | Energia wodna. |
| `S-` | Energia słoneczna. |
| `W-` | Energia wiatrowa. |
| `O-` | Operator obecny w stacji. |
| `5-`, `6-` | Oznaczenie niestandardowej dla danego obszaru częstotliwości sieci zasilającej: odpowiednio 50 albo 60 Hz. |

Przykładowy raport domowej stacji z nakładką `S`:

```text
SQ9MDD>APRS:!5003.50NS01956.00E-
```

W tym zapisie `S` umieszczone po szerokości geograficznej zastępuje znak `\`, a końcowy `-` określa symbol domu. **Dwa znaki `S-` wystarczają, aby przekazać informację o deklarowanym zasilaniu słonecznym.** Nie jest to dodatkowe pole, rozszerzenie komentarza ani trzeci znak symbolu.

Informacja jest deklaracją sposobu oznaczenia stacji, nie pomiarem jej aktualnego stanu. `E-` nie dowodzi, że zasilanie awaryjne właśnie pracuje; podobnie `S-` nie jest telemetrią produkcji energii. Jeden kod zawiera też tylko jedną nakładkę. Jeżeli istotnych właściwości jest więcej, należy uzupełnić je komentarzem, telemetrią lub innymi przewidzianymi mechanizmami APRS.

**Uwaga historyczna:** starsze wykazy przypisywały `C-` klubowi krótkofalarskiemu. W dostarczonej rewizji `symbols-new.txt` z 2017 roku `C-` oznacza już połączone alternatywne źródła energii, a **klub krótkofalarski przeniesiono do `Ch`** (rodzina budynków `\h`). Przy porównywaniu starszych programów i tabel trzeba uwzględnić tę zmianę.

### Digipeater: informacja o funkcjonalności

![Bazowy symbol digipeatera z tablicy alternatywnej](./_img/verG/a02.gif)

Nakładka digipeatera może wskazywać funkcję, a nie tylko rodzaj urządzenia:

| Kod | Znaczenie według wykazu rozszerzeń |
|---|---|
| `/#` | Ogólny symbol digipeatera z tablicy podstawowej. |
| `1#` | Digipeater WIDE1-1. |
| `A#` | Digipeater z alternatywnym wejściem, np. na innej częstotliwości. |
| `E#` | Digipeater z zasilaniem awaryjnym. |
| `I#` | Digipeater wyposażony także w funkcję IGate. |
| `V#` | Digipeater wykorzystujący mechanizm Viscous. |

`I#` nie oznacza tego samego co sam symbol bramy APRS-IS. Pierwszy kod opisuje digipeater z dodatkową funkcją, drugi należy do odrębnej rodziny symboli bram. Wybór zależy od tego, którą funkcję chcemy wyeksponować na mapie.

### IGate: kierunek i sposób przekazywania danych

![Bazowy symbol bramy z tablicy alternatywnej](./_img/verG/a05.gif)

Rodzina `\&` może informować o rodzaju funkcjonalności bramy:

| Kod | Znaczenie |
|---|---|
| `I&` | Ogólny symbol IGate; dokumentacja zaleca użycie bardziej szczegółowej nakładki, jeśli to możliwe. |
| `R&` | IGate tylko odbierający z RF, bez przekazywania wiadomości w kierunku RF. |
| `T&` | Nadawczy IGate z trasą wiadomości ograniczoną do jednego przeskoku. |
| `2&` | Nadawczy IGate z trasą dwóch przeskoków; wykaz zaznacza, że zwykle nie jest to zalecane. |

Ikona może więc pomóc odróżnić stację odbiorczą od bramy zapewniającej także przekazywanie wiadomości na RF. Symbol opisuje jednak zadeklarowaną funkcję, a nie potwierdza aktualnego stanu połączenia z APRS-IS lub poprawności konfiguracji.

### Działalność terenowa i wydarzenia

W rodzinie symbolu działalności przenośnej różne nakładki identyfikują rodzaj aktywności:

| Kod | Znaczenie |
|---|---|
| `/;` | Podstawowy symbol pracy przenośnej / obozowiska. |
| `F;` | Field Day. |
| `I;` | IOTA (*Islands on the Air*). |
| `S;` | SOTA (*Summits on the Air*). |
| `W;` | WOTA (*Wainwrights on the Air*). |

To przykład znaczenia nakładek niezwiązanego z wyposażeniem lub zasilaniem. Ta sama ikona bazowa może informować, w jakim rodzaju aktywności uczestniczy stacja.

### Pojazdy i obiekty specjalne

W rodzinie pojazdów `\>` można wyróżnić między innymi `B>` dla pojazdu akumulatorowego, `P>` dla hybrydy typu plug-in oraz `S>` dla pojazdu zasilanego energią słoneczną. Z kolei w rodzinie schronień `\z` kod `Ez` oznacza obiekt z zasilaniem awaryjnym, a `Tz` punkt segregacji medycznej (*triage*). Są to przykłady informowania o określonej właściwości lub funkcji obiektu bez rozbudowy samej reprezentacji symbolu.

Dobierając taki symbol, należy deklarować rzeczywistą rolę obiektu. Nie warto używać oznaczeń służb, zdarzeń lub zagrożeń wyłącznie ze względów graficznych.

## Jak dobierać i interpretować symbole

Symbol powinien przede wszystkim odpowiadać **aktualnej roli stacji lub obiektu**. Dla typowych zastosowań można wybrać na przykład `/[` dla stacji pieszej, `/>` dla samochodu, `/b` dla roweru, `/-` dla domowej stacji, `/#` dla digipeatera, `/r` dla przemiennika albo `/_` dla stacji pogodowej. Bardziej szczegółową informację warto zakodować nakładką tylko wtedy, gdy jej kombinacja ma udokumentowane znaczenie.

Odczytując nieskompresowany raport pozycyjny, należy odszukać znak tablicy bezpośrednio po szerokości geograficznej i znak symbolu po długości. Następnie interpretuje się **parę znaków jako jeden kod**. Na przykład `!5003.50N/01956.00E>` zawiera symbol samochodu `/>`, a `!5003.50NS01956.00E-` kod `S-`, wskazujący domową stację z zasilaniem słonecznym.

Warto odróżniać symbol od pozostałych danych raportu. Określa on kategorię lub cechę obiektu, lecz nie zastępuje komentarza, telemetrii ani informacji o jego faktycznym stanie. Jeśli dana właściwość ma znaczenie operacyjne, powinna być również możliwa do zrozumienia przez użytkowników nieobsługujących najnowszych nakładek.

## Dlaczego ten sam symbol może wyglądać inaczej

Grafiki symboli są przechowywane albo generowane lokalnie przez aplikację. Program korzystający ze starszej tablicy może wyświetlić nową kombinację jako sam symbol bazowy, pominąć literę lub przedstawić ją inaczej niż nowoczesny klient. Również w pełni zgodne programy mogą wybrać różne style graficzne przy zachowaniu tego samego znaczenia kodu.

Dokumentacja rozszerzeń zwraca uwagę na zgodność: zbyt częste zmiany znaczenia symboli oraz brak obsługi nakładek mogą prowadzić do odmiennych interpretacji tego samego pakietu. Dlatego oprogramowanie powinno zachowywać **oba odebrane znaki symbolu**, nawet jeśli samo nie potrafi jeszcze przedstawić danej kombinacji graficznie. Nieznana nakładka nie powinna powodować odrzucenia prawidłowego raportu pozycyjnego.

Przy działaniach operacyjnych nie należy polegać wyłącznie na nietypowej ikonie. Najważniejsze informacje, takie jak rola obiektu, częstotliwość albo dostępność usług, można dodatkowo umieścić w odpowiednim komentarzu APRS.

## Pełna tablica symboli
Poniższe zestawienie obejmuje wszystkie 188 pozycji dostarczonych tablic: 94 z tablicy podstawowej i 94 z alternatywnej. Wiersze są sparowane według tego samego znaku symbolu. Grafiki pochodzą z lokalnego zestawu `_img/verG` i przedstawiają symbole bazowe, a nie wszystkie możliwe warianty nakładek.

**Legenda:** „wolny” oznacza pozycję niewyznaczoną przez wskazany wykaz. „Baza nakładek” oznacza symbol, którego znaczenie może zostać doprecyzowane literą lub cyfrą.

| Kod podstawowy | Ikona | Znaczenie | Kod alternatywny | Ikona | Znaczenie |
|---|:---:|---|---|:---:|---|
| `/!` | ![Policja](./_img/verG/00.gif) | Policja / szeryf. | `\!` | ![Alarm](./_img/verG/a00.gif) | Alarm; baza nakładek. |
| `/"` | ![Zarezerwowany](./_img/verG/01.gif) | Zarezerwowany (dawniej deszcz). | `\"` | ![Zarezerwowany](./_img/verG/a01.gif) | Zarezerwowany. |
| `/#` | ![Digipeater](./_img/verG/02.gif) | Digipeater. | `\#` | ![Digipeater z nakładką](./_img/verG/a02.gif) | Digipeater z nakładką / zielona gwiazda. |
| `/$` | ![Telefon](./_img/verG/03.gif) | Telefon. | `\$` | ![Bankomat](./_img/verG/a03.gif) | Bank lub bankomat. |
| `/%` | ![DX Cluster](./_img/verG/04.gif) | DX Cluster. | `\%` | ![Elektrownia](./_img/verG/a04.gif) | Elektrownia; baza nakładek. |
| `/&` | ![Brama HF](./_img/verG/05.gif) | Brama HF. | `\&` | ![Brama](./_img/verG/a05.gif) | Brama; nakładki IGate. |
| `/'` | ![Mały samolot](./_img/verG/06.gif) | Mały samolot. | `\'` | ![Miejsce zdarzenia](./_img/verG/a06.gif) | Miejsce wypadku / zdarzenia. |
| `/(` | ![Satelita](./_img/verG/07.gif) | Ruchoma stacja satelitarna. | `\(` | ![Zachmurzenie](./_img/verG/a07.gif) | Zachmurzenie; baza odmian chmur. |
| `/)` | ![Wózek](./_img/verG/08.gif) | Wózek inwalidzki. | `\)` | ![Firenet](./_img/verG/a08.gif) | Firenet MEO / obserwacja Ziemi MODIS. |
| `/*` | ![Skuter śnieżny](./_img/verG/09.gif) | Skuter śnieżny. | `\*` | ![Wolny](./_img/verG/a09.gif) | Wolny. |
| `/+` | ![Czerwony Krzyż](./_img/verG/10.gif) | Czerwony Krzyż. | `\+` | ![Kościół](./_img/verG/a10.gif) | Kościół. |
| `/,` | ![Skauci](./_img/verG/11.gif) | Boy Scouts. | `\,` | ![Harcerki](./_img/verG/a11.gif) | Girl Scouts. |
| `/-` | ![Dom VHF](./_img/verG/12.gif) | Dom / QTH w paśmie VHF. | `\-` | ![Dom HF](./_img/verG/a12.gif) | Dom (historycznie stacja HF); baza rodziny nakładek, m.in. `O-` — operator obecny. |
| `/.` | ![X](./_img/verG/13.gif) | Znak X. | `\.` | ![Pozycja niejednoznaczna](./_img/verG/a13.gif) | Pozycja niejednoznaczna (duży znak zapytania). |
| `//` | ![Punkt](./_img/verG/14.gif) | Czerwony punkt. | `\/` | ![Punkt docelowy](./_img/verG/a14.gif) | Punkt docelowy / waypoint. |
| `/0` | ![Koło przestarzałe](./_img/verG/15.gif) | Koło — symbol przestarzały. | `\0` | ![Koło](./_img/verG/a15.gif) | Koło; IRLP, EchoLink, WiRES i nakładki. |
| `/1` | ![Wolny](./_img/verG/16.gif) | Wolny / historycznie numerowane koło. | `\1` | ![Wolny](./_img/verG/a16.gif) | Wolny. |
| `/2` | ![Wolny](./_img/verG/17.gif) | Wolny / historycznie numerowane koło. | `\2` | ![Wolny](./_img/verG/a17.gif) | Wolny. |
| `/3` | ![Wolny](./_img/verG/18.gif) | Wolny / historycznie numerowane koło. | `\3` | ![Wolny](./_img/verG/a18.gif) | Wolny. |
| `/4` | ![Wolny](./_img/verG/19.gif) | Wolny / historycznie numerowane koło. | `\4` | ![Wolny](./_img/verG/a19.gif) | Wolny. |
| `/5` | ![Wolny](./_img/verG/20.gif) | Wolny / historycznie numerowane koło. | `\5` | ![Wolny](./_img/verG/a20.gif) | Wolny. |
| `/6` | ![Wolny](./_img/verG/21.gif) | Wolny / historycznie numerowane koło. | `\6` | ![Wolny](./_img/verG/a21.gif) | Wolny. |
| `/7` | ![Wolny](./_img/verG/22.gif) | Wolny / historycznie numerowane koło. | `\7` | ![Wolny](./_img/verG/a22.gif) | Wolny. |
| `/8` | ![Wolny](./_img/verG/23.gif) | Wolny / historycznie numerowane koło. | `\8` | ![Węzeł sieci](./_img/verG/a23.gif) | Węzeł 802.11 lub innej sieci. |
| `/9` | ![Wolny](./_img/verG/24.gif) | Wolny / historycznie numerowane koło. | `\9` | ![Stacja paliw](./_img/verG/a24.gif) | Stacja paliw. |
| `/:` | ![Pożar](./_img/verG/25.gif) | Pożar. | `\:` | ![Wolny](./_img/verG/a25.gif) | Wolny (grad przeniesiony do nakładek). |
| `/;` | ![Kemping](./_img/verG/26.gif) | Kemping / działania przenośne. | `\;` | ![Park](./_img/verG/a26.gif) | Park lub miejsce piknikowe; obsługuje nakładki wydarzeń. |
| `/<` | ![Motocykl](./_img/verG/27.gif) | Motocykl. | `\<` | ![Ostrzeżenie](./_img/verG/a27.gif) | Ostrzeżenie / pojedyncza flaga meteorologiczna. |
| `/=` | ![Lokomotywa](./_img/verG/28.gif) | Lokomotywa. | `\=` | ![Baza nakładek](./_img/verG/a28.gif) | Wolna grupa symboli z nakładkami. |
| `/>` | ![Samochód](./_img/verG/29.gif) | Samochód. | `\>` | ![Pojazd](./_img/verG/a29.gif) | Pojazd; baza nakładek. |
| `/?` | ![Serwer](./_img/verG/30.gif) | Serwer plików. | `\?` | ![Punkt informacyjny](./_img/verG/a30.gif) | Punkt informacyjny. |
| `/@` | ![Prognoza](./_img/verG/31.gif) | Punkt przewidywanej pozycji (H/C). | `\@` | ![Huragan](./_img/verG/a31.gif) | Huragan / burza tropikalna. |
| `/A` | ![Punkt pomocy](./_img/verG/32.gif) | Punkt pomocy. | `\A` | ![Pudełko](./_img/verG/a32.gif) | Pudełko; DTMF, RFID, XO i inne nakładki. |
| `/B` | ![BBS](./_img/verG/33.gif) | BBS / PBBS. | `\B` | ![Wolny](./_img/verG/a33.gif) | Wolny (zamieć śnieżna jako nakładka). |
| `/C` | ![Kajak](./_img/verG/34.gif) | Kajak. | `\C` | ![Straż przybrzeżna](./_img/verG/a34.gif) | Straż przybrzeżna. |
| `/D` | ![Wolny](./_img/verG/35.gif) | Wolny. | `\D` | ![Skład](./_img/verG/a35.gif) | Skład / depot; baza nakładek. |
| `/E` | ![Oko](./_img/verG/36.gif) | Oko; wydarzenie lub punkt zwracający uwagę. | `\E` | ![Dym](./_img/verG/a36.gif) | Dym i inne kody widzialności. |
| `/F` | ![Traktor](./_img/verG/37.gif) | Pojazd rolniczy / traktor. | `\F` | ![Wolny](./_img/verG/a37.gif) | Wolny (marznący deszcz jako nakładka). |
| `/G` | ![Lokator](./_img/verG/38.gif) | Lokator Maidenhead, 6 znaków. | `\G` | ![Wolny](./_img/verG/a38.gif) | Wolny (przelotny śnieg jako nakładka). |
| `/H` | ![Hotel](./_img/verG/39.gif) | Hotel. | `\H` | ![Mgła](./_img/verG/a39.gif) | Zamglenie; także baza zagrożeń. |
| `/I` | ![TCP IP](./_img/verG/40.gif) | Stacja TCP/IP w sieci radiowej. | `\I` | ![Przelotny deszcz](./_img/verG/a40.gif) | Przelotny deszcz. |
| `/J` | ![Wolny](./_img/verG/41.gif) | Wolny. | `\J` | ![Wolny](./_img/verG/a41.gif) | Wolny (błyskawica jako nakładka). |
| `/K` | ![Szkoła](./_img/verG/42.gif) | Szkoła. | `\K` | ![Radiotelefon](./_img/verG/a42.gif) | Radiotelefon Kenwood. |
| `/L` | ![Komputer](./_img/verG/43.gif) | Użytkownik komputera podłączony do APRS. | `\L` | ![Latarnia](./_img/verG/a43.gif) | Latarnia morska. |
| `/M` | ![MacAPRS](./_img/verG/44.gif) | MacAPRS. | `\M` | ![MARS](./_img/verG/a44.gif) | MARS; nakładki rodzajów służby. |
| `/N` | ![NTS](./_img/verG/45.gif) | Stacja National Traffic System. | `\N` | ![Boja](./_img/verG/a45.gif) | Boja nawigacyjna. |
| `/O` | ![Balon](./_img/verG/46.gif) | Balon. | `\O` | ![Rakieta](./_img/verG/a46.gif) | Rakieta amatorska / rodzina balonów z nakładką. |
| `/P` | ![Policja](./_img/verG/47.gif) | Policja. | `\P` | ![Parking](./_img/verG/a47.gif) | Parking. |
| `/Q` | ![Wolny](./_img/verG/48.gif) | Wolny. | `\Q` | ![Trzęsienie ziemi](./_img/verG/a48.gif) | Trzęsienie ziemi. |
| `/R` | ![Kamper](./_img/verG/49.gif) | Pojazd rekreacyjny / kamper. | `\R` | ![Restauracja](./_img/verG/a49.gif) | Restauracja. |
| `/S` | ![Prom](./_img/verG/50.gif) | Wahadłowiec / prom kosmiczny. | `\S` | ![Satelita](./_img/verG/a50.gif) | Satelita / PACSAT. |
| `/T` | ![SSTV](./_img/verG/51.gif) | SSTV. | `\T` | ![Burza](./_img/verG/a51.gif) | Burza z wyładowaniami. |
| `/U` | ![Autobus](./_img/verG/52.gif) | Autobus. | `\U` | ![Słońce](./_img/verG/a52.gif) | Słonecznie. |
| `/V` | ![ATV](./_img/verG/53.gif) | ATV / quad. | `\V` | ![VORTAC](./_img/verG/a53.gif) | Pomoc nawigacyjna VORTAC. |
| `/W` | ![NWS](./_img/verG/54.gif) | Stacja National Weather Service. | `\W` | ![NWS z nakładką](./_img/verG/a54.gif) | Stacja NWS z nakładkami. |
| `/X` | ![Śmigłowiec](./_img/verG/55.gif) | Śmigłowiec. | `\X` | ![Apteka](./_img/verG/a55.gif) | Apteka. |
| `/Y` | ![Jacht](./_img/verG/56.gif) | Jacht żaglowy. | `\Y` | ![Radio](./_img/verG/a56.gif) | Radia i urządzenia APRS. |
| `/Z` | ![WinAPRS](./_img/verG/57.gif) | WinAPRS. | `\Z` | ![Wolny](./_img/verG/a57.gif) | Wolny. |
| `/[` | ![Osoba](./_img/verG/58.gif) | Osoba / stacja piesza. | `\[` | ![Chmura ścienna](./_img/verG/a58.gif) | Chmura ścienna; także osoba z nakładką. |
| `/\` | ![Trójkąt](./_img/verG/59.gif) | Trójkąt — radiopelengacja. | `\\` | ![Symbol GPS](./_img/verG/a59.gif) | Nowy symbol GPS z nakładkami. |
| `/]` | ![Poczta](./_img/verG/60.gif) | Poczta / urząd pocztowy. | `\]` | ![Wolny](./_img/verG/a60.gif) | Wolny. |
| `/^` | ![Samolot](./_img/verG/61.gif) | Duży samolot. | `\^` | ![Lotnictwo](./_img/verG/a61.gif) | Lotnictwo; inne typy samolotów z nakładką. |
| `/_` | ![Pogoda](./_img/verG/62.gif) | Stacja pogodowa. | `\_` | ![Pogoda z nakładką](./_img/verG/a62.gif) | Stacja pogodowa / zielony digi z nakładką. |
| <code>/&#96;</code> | ![Antena](./_img/verG/63.gif) | Antena paraboliczna. | <code>&#92;&#96;</code> | ![Deszcz](./_img/verG/a63.gif) | Deszcz; rodzaje opadu z nakładką. |
| `/a` | ![Ambulans](./_img/verG/64.gif) | Ambulans. | `\a` | ![ARRL](./_img/verG/a64.gif) | ARRL, ARES, Winlink, D-STAR i inne nakładki. |
| `/b` | ![Rower](./_img/verG/65.gif) | Rower. | `\b` | ![Wolny](./_img/verG/a65.gif) | Wolny (pył lub piasek jako nakładka). |
| `/c` | ![Punkt dowodzenia](./_img/verG/66.gif) | Punkt dowodzenia zdarzeniem. | `\c` | ![Obrona cywilna](./_img/verG/a66.gif) | Obrona cywilna; RACES, SATERN i inne nakładki. |
| `/d` | ![Straż pożarna](./_img/verG/67.gif) | Straż pożarna. | `\d` | ![DX spot](./_img/verG/a67.gif) | DX spot według znaku wywoławczego. |
| `/e` | ![Koń](./_img/verG/68.gif) | Koń / jeździectwo. | `\e` | ![Śnieg z deszczem](./_img/verG/a68.gif) | Śnieg z deszczem. |
| `/f` | ![Wóz strażacki](./_img/verG/69.gif) | Wóz strażacki. | `\f` | ![Lejek](./_img/verG/a69.gif) | Chmura lejowa. |
| `/g` | ![Szybowiec](./_img/verG/70.gif) | Szybowiec. | `\g` | ![Wiatr](./_img/verG/a70.gif) | Flagi sztormowe. |
| `/h` | ![Szpital](./_img/verG/71.gif) | Szpital. | `\h` | ![Sklep](./_img/verG/a71.gif) | Sklep / giełda krótkofalarska; `Ch` — klub krótkofalarski. |
| `/i` | ![IOTA](./_img/verG/72.gif) | IOTA — Islands on the Air. | `\i` | ![Punkt zainteresowania](./_img/verG/a72.gif) | Pudełko / punkt zainteresowania. |
| `/j` | ![Jeep](./_img/verG/73.gif) | Jeep. | `\j` | ![Roboty drogowe](./_img/verG/a73.gif) | Roboty drogowe. |
| `/k` | ![Ciężarówka](./_img/verG/74.gif) | Ciężarówka. | `\k` | ![Pojazd specjalny](./_img/verG/a74.gif) | Pojazd specjalny, SUV, ATV lub 4×4. |
| `/l` | ![Laptop](./_img/verG/75.gif) | Laptop. | `\l` | ![Obszar](./_img/verG/a75.gif) | Obszar: prostokąt, koło, linia lub trójkąt. |
| `/m` | ![Przemiennik Mic-E](./_img/verG/76.gif) | Przemiennik Mic-E. | `\m` | ![Tablica wartości](./_img/verG/a76.gif) | Tablica wartości / signpost. |
| `/n` | ![Węzeł](./_img/verG/77.gif) | Węzeł. | `\n` | ![Trójkąt z nakładką](./_img/verG/a77.gif) | Trójkąt z nakładką. |
| `/o` | ![EOC](./_img/verG/78.gif) | Centrum działań kryzysowych (EOC). | `\o` | ![Małe koło](./_img/verG/a78.gif) | Małe koło. |
| `/p` | ![Pies](./_img/verG/79.gif) | ROVER / pies. | `\p` | ![Wolny](./_img/verG/a79.gif) | Wolny (częściowe zachmurzenie jako nakładka). |
| `/q` | ![Lokator](./_img/verG/80.gif) | Lokator Maidenhead (opis 128 m). | `\q` | ![Wolny](./_img/verG/a80.gif) | Wolny. |
| `/r` | ![Przemiennik](./_img/verG/81.gif) | Przemiennik. | `\r` | ![Toaleta](./_img/verG/a81.gif) | Toalety. |
| `/s` | ![Statek](./_img/verG/82.gif) | Statek z napędem. | `\s` | ![Łódź z nakładką](./_img/verG/a82.gif) | Statek / łódź z nakładką. |
| `/t` | ![Postój ciężarówek](./_img/verG/83.gif) | Postój dla ciężarówek. | `\t` | ![Tornado](./_img/verG/a83.gif) | Tornado. |
| `/u` | ![Ciężarówka](./_img/verG/84.gif) | Ciężarówka 18-kołowa. | `\u` | ![Ciężarówka z nakładką](./_img/verG/a84.gif) | Ciężarówka z nakładką. |
| `/v` | ![Van](./_img/verG/85.gif) | Van. | `\v` | ![Van z nakładką](./_img/verG/a85.gif) | Van z nakładką. |
| `/w` | ![Woda](./_img/verG/86.gif) | Stacja wodna. | `\w` | ![Powódź](./_img/verG/a86.gif) | Powódź, lawina albo osuwisko. |
| `/x` | ![xAPRS](./_img/verG/87.gif) | xAPRS / Unix. | `\x` | ![Przeszkoda](./_img/verG/a87.gif) | Wypadek lub przeszkoda na drodze. |
| `/y` | ![Yagi](./_img/verG/88.gif) | Antena Yagi przy QTH. | `\y` | ![Skywarn](./_img/verG/a88.gif) | Skywarn. |
| `/z` | ![Wolny](./_img/verG/89.gif) | Wolny. | `\z` | ![Schronienie](./_img/verG/a89.gif) | Schronienie z nakładką. |
| `/{` | ![Wolny](./_img/verG/90.gif) | Wolny. | `\{` | ![Wolny](./_img/verG/a90.gif) | Wolny (mgła jako nakładka). |
| <code>/&#124;</code> | ![Przełącznik TNC](./_img/verG/91.gif) | Przełącznik strumieni TNC. | <code>&#92;&#124;</code> | ![Przełącznik TNC](./_img/verG/a91.gif) | Przełącznik strumieni TNC. |
| `/}` | ![Wolny](./_img/verG/92.gif) | Wolny. | `\}` | ![Wolny](./_img/verG/a92.gif) | Wolny. |
| `/~` | ![Przełącznik TNC](./_img/verG/93.gif) | Przełącznik strumieni TNC. | `\~` | ![Przełącznik TNC](./_img/verG/a93.gif) | Przełącznik strumieni TNC. |

| Dodatkowy plik obrazu | Ikona | Uwagi |
|---|:---:|---|
| `x.gif` | ![Czerwony Krzyż](./_img/verG/x.gif) | Dodatkowa kopia grafiki Czerwonego Krzyża; kodem symbolu w tabeli podstawowej jest `/+`. |


## Źródła i uwagi do wykazów

- [APRS Symbols - master symbol list](http://www.aprs.org/symbols/symbolsX.txt) - podstawowa i alternatywna tablica symboli, w tym oznaczenia symboli rozszerzanych nakładkami.
- [APRS symbol overlays and extensions](http://www.aprs.org/symbols/symbols-new.txt) - przykłady i przypisane znaczenia nakładek dla domów, digipeaterów, IGate, pojazdów i innych rodzin.
- [Overlay Extension to APRS Symbol Set](http://www.aprs.org/symbols/symbols-overlays.txt) - uzasadnienie rozszerzenia zbioru symboli oraz założenia zgodności wstecznej.
- [Background on Updating APRS Symbols](http://www.aprs.org/symbols/symbols-background.txt) - sposób wyświetlania symboli i ograniczenia starszego oprogramowania.

Pełna tablica powyżej przedstawia **symbole bazowe** i ich opisy w zestawieniu przygotowanym dla artykułu. Warianty z nakładkami należy sprawdzać oddzielnie w wykazie rozszerzeń. Przy rozbieżnościach między starszymi tabelami a późniejszym wykazem należy zwracać uwagę na datę rewizji danej definicji, szczególnie w przypadku kodów o zmienionym znaczeniu, takich jak `C-`.
