---
title: "Raporty pogodowe APRS"
description: "Raporty WX: historia, formaty, wymagane pola, brakujące pomiary, metodyka pomiarów i jakość danych CWOP/MADIS."
---

APRS umożliwia przesyłanie bieżących pomiarów meteorologicznych bezpośrednio przez sieć radiową oraz za pośrednictwem APRS-IS. Raport WX może zawierać pozycję stacji, informacje o wietrze, temperaturze, opadach, wilgotności i ciśnieniu atmosferycznym. Protokół przewiduje także dodatkowe parametry oraz formaty wykorzystywane przez starsze urządzenia.

Pogoda nie ma jednego, wyłącznego identyfikatora DTI. Dane meteorologiczne mogą być częścią zwykłego raportu pozycji, obiektu APRS albo osobnego raportu bez pozycji. O sposobie interpretacji decyduje połączenie DTI, struktury raportu i symbolu stacji.

## Jak rozwijały się raporty WX

APRS wykorzystywał pomiary pogodowe na długo przed pojawieniem się współczesnych internetowych serwisów meteorologicznych dla krótkofalowców. Pierwsze rozwiązania współpracowały m.in. ze stacjami Peet Bros ULTIMETER i Davis. Możliwa była również praca zdalna: stacja pogodowa, TNC i radiotelefon mogły przekazywać odczyty bez stale działającego komputera. Zastosowaniem była nie tylko obserwacja lokalnej pogody, ale również wymiana raportów w sieciach obserwatorów, takich jak SKYWARN.

Rozwój formatu dobrze pokazuje, w jaki sposób APRS dostosowywano do dostępnego sprzętu oraz nowych potrzeb:

- **Lata 90.** - APRS obsługiwał dane pochodzące z różnych modeli stacji pogodowych, w tym surowe formaty producentów. Dokument `WX.TXT` odnotowuje zmianę formatów w APRSdos 793 z czerwca 1997 r., która nie była wstecznie kompatybilna ze starszym sposobem prezentacji danych.
- **2000 r.** - w *APRS Protocol Reference 1.0.1* uporządkowano trzy postacie raportów: surową, bez pozycji oraz kompletną, zawierającą pozycję i pomiary.
- **2001 r. i późniejsze wyjaśnienia APRS 1.1** - doprecyzowano m.in. odróżnianie braku pomiaru od wartości zero, znaczenie liczników opadów oraz niejednoznaczność pola `s`, które w różnych wariantach może oznaczać prędkość wiatru albo opad śniegu.
- **Od lipca 2001 r.** - dane z sieci CWOP, wywodzącej się ze środowiska krótkofalarskiego i APRSWXNET, zasilają system NOAA MADIS. Raporty amatorskich stacji pogodowych zaczęły w ten sposób trafiać do szerszego obiegu obserwacji meteorologicznych.
- **2006 r.** - opisano wykorzystanie APRS do raportowania poziomu wody i zagrożenia powodziowego. Pojawiły się symbole wodowskazów oraz propozycja przenoszenia dodatkowych pomiarów w polach pogodowych.
- **Marzec 2011 r.** - po awarii elektrowni Fukushima Daiichi Bob Bruninga zaproponował rozszerzenie formatu WX o odczyty promieniowania. Dokument z 24 marca 2011 r. opisuje pole `Xxxx` oraz dalsze ujednolicenie sposobu oznaczania czujników i zagrożeń.

Historia pola promieniowania pokazuje istotną właściwość APRS: format WX zaczęto postrzegać również jako nośnik pomiarów środowiskowych, nie tylko klasycznej meteorologii. Należy jednak rozróżniać pola z podstawowej specyfikacji od późniejszych propozycji. Opisanie rozszerzenia w dokumentacji nie oznacza, że każda aplikacja je implementuje.

## Rodzaje raportów pogodowych

APRS Protocol Reference 1.0.1 wyróżnia trzy formaty:

| Format | Charakterystyka |
| --- | --- |
| **Complete Weather Report** | Dane meteorologiczne wraz z pozycją w jednym pakiecie. Format zalecany do nowych implementacji. |
| **Positionless Weather Report** | Dane meteorologiczne bez współrzędnych. Odbiornik musi wcześniej znać pozycję stacji z osobnego pakietu. |
| **Raw Weather Report** | Surowe dane w formacie określonego urządzenia meteorologicznego. Rozwiązanie historyczne, niezalecane dla nowych nadajników. |

Połączenie pozycji i aktualnych pomiarów w jednym pakiecie ogranicza zależność od wcześniejszych transmisji. Ma to szczególne znaczenie na kanale radiowym, gdzie nie ma gwarancji odebrania każdej ramki.

## Kompletny raport pogodowy

W podstawowej postaci Complete Weather Report jest raportem pozycji z symbolem pogodowym i następującymi po nim danymi WX. Można wykorzystać dowolny z czterech standardowych DTI pozycji:

| DTI | Znacznik czasu | Deklarowana obsługa APRS messaging |
| --- | --- | --- |
| `!` | nie | nie |
| `=` | nie | tak |
| `/` | tak | nie |
| `@` | tak | tak |

### Co jest obowiązkowe w kompletnym raporcie?

Dla **nieskompresowanego** Complete Weather Report z pozycją obowiązkowa jest struktura pozycyjna wynikająca z wybranego DTI, symbol pogodowy, siedmioznakowe pole kierunku i prędkości wiatru `ddd/sss` oraz pole temperatury `txxx`. Oznacza to **obowiązkową obecność pól, a nie obowiązek posiadania wszystkich czujników**. Jeżeli pomiaru nie ma, zachowujemy pozycję pola i wpisujemy kropki lub spacje o odpowiedniej długości.

| Element | Wymagany w nieskompresowanym raporcie kompletnym? | Brak odczytu |
| --- | --- | --- |
| DTI i prawidłowa pozycja | Tak | Pozycja jest elementem definiującym ten wariant; nie zastępujemy jej kropkami WX. |
| Symbol stacji pogodowej | Tak | W klasycznej postaci `/_` lub `\_`; znaczenie mają oba znaki symbolu. |
| `ddd/sss` - kierunek i prędkość wiatru | Tak | `.../...` albo trzy spacje po każdej stronie ukośnika. |
| `txxx` - temperatura | Tak | `t...` albo `t` i trzy spacje. |
| `gxxx` - poryw wiatru | Nie, w późniejszych wyjaśnieniach kompletnego formatu | Można pominąć lub użyć `g...`. |
| Opady, wilgotność, ciśnienie i pozostałe pola | Nie | Pomija się pole albo zastępuje jego cyfry kropkami/spacjami. |

Rozróżnienie dotyczące porywów jest istotne: w **raporcie bez pozycji** pole `gxxx` należy do obowiązkowego zestawu początkowego, natomiast późniejsze uściślenia pełnego formatu wymagają przede wszystkim `ddd/sss` i `txxx`. W tabelach i przykładach APRS101 często spotyka się również `gxxx`, gdy dostępny jest pomiar porywów.

Przykład kompletnej ramki w zapisie monitorowym:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_220/004g005t068r000p015P012h72b10132
```

Część znajdująca się po dwukropku jest polem Information. Jej podstawowe elementy to:

```text
! | 5003.50N | / | 01956.75E | _ | 220/004 | g005t068r000p015P012h72b10132
```

| Element | Znaczenie |
| --- | --- |
| `!` | DTI raportu pozycji bez znacznika czasu. |
| `5003.50N` | Szerokość geograficzna. |
| `/` | Identyfikator podstawowej tabeli symboli. |
| `01956.75E` | Długość geograficzna. |
| `_` | Kod symbolu stacji pogodowej. |
| `220/004` | Kierunek i prędkość wiatru. |
| `g005...` | Pozostałe dane meteorologiczne. |

Znaki `/` i `_` w części pozycyjnej tworzą oznaczenie symbolu `/_`. Nie należy mylić kodu symbolu `_` z DTI `_`, który występuje na początku osobnego raportu bez pozycji.

### Pola pomiarowe i jednostki

Klasyczny format WX wykorzystuje krótkie pola o ustalonej szerokości. W kompletnym raporcie kierunek i prędkość wiatru są zapisane bez literowych prefiksów, w siedmiobajtowym rozszerzeniu `ddd/sss`.

| Pole | Pomiar | Jednostka i sposób zapisu |
| --- | --- | --- |
| `ddd/sss` | Kierunek i średnia prędkość wiatru | Stopnie oraz mph; prędkość uśredniona z 1 minuty. **Pole wymagane.** |
| `gxxx` | Poryw wiatru | mph; maksymalna prędkość z ostatnich 5 minut. Opcjonalne w raporcie kompletnym. |
| `txxx` | Temperatura | °F; liczby ujemne, np. `t-07`. **Pole wymagane.** |
| `rxxx` | Opad z ostatniej godziny | Setne części cala. |
| `pxxx` | Opad z ostatnich 24 godzin | Setne części cala; przesuwające się okno 24-godzinne. |
| `Pxxx` | Opad od północy | Setne części cala. |
| `hxx` | Wilgotność względna | Procenty; `h00` oznacza 100%. |
| `bxxxxx` | Ciśnienie atmosferyczne | Dziesiąte części hPa (mbar). |

Wartości transmitowane są w jednostkach zdefiniowanych przez protokół, niezależnie od jednostek używanych przez aplikację do prezentacji danych. Odbiornik może wyświetlić temperaturę w °C, wiatr w km/h i opady w milimetrach, ale nie zmienia to kodowania raportu.

W przedstawionym przykładzie:

| Dane | Wartość odczytana z raportu |
| --- | --- |
| Wiatr | 220°, 4 mph |
| Poryw | 5 mph |
| Temperatura | 68°F (20°C) |
| Opad w ostatniej godzinie | 0 |
| Opad z ostatnich 24 godzin | 0,15 cala |
| Opad od północy | 0,12 cala |
| Wilgotność | 72% |
| Ciśnienie | 1013,2 hPa |

Pola `r`, `p` i `P` opisują różne przedziały czasu. W szczególności `p` nie oznacza opadu z poprzedniego dnia kalendarzowego, a `P` nie jest zamiennikiem `p`. Sposób wyznaczania tych wartości oraz różnice między pomiarami wiatru APRS i CWOP omówiono w części o metodyce pomiarowej.

### Brakujące pomiary: kropki, spacje i pola pominięte

APRS rozróżnia **rzeczywistą wartość zero** od **braku dostępnego pomiaru**. Jeżeli raport ma zdefiniowane pole, ale stacja nie ma odpowiedniego czujnika albo odczyt jest chwilowo niedostępny, wartości mogą zostać zastąpione kropkami (`.`) lub spacjami. Długość pola pozostaje bez zmian. Późniejsze wyjaśnienia autora protokołu wskazują kropki jako czytelniejszy zapis.

| Sytuacja | Przykład | Interpretacja |
| --- | --- | --- |
| Brak wiatromierza | `.../...` | Nie znamy kierunku ani prędkości wiatru. |
| Kierunek niedostępny, prędkość znana | `.../004` | Znamy prędkość 4 mph, ale nie kierunek. |
| Brak termometru | `t...` | Temperatura jest nieznana. |
| Brak pomiaru porywów w obecnym polu | `g...` | Nie znamy porywów; nie jest to 0 mph. |
| Brak odczytu ciśnienia w obecnym polu | `b.....` | Ciśnienie jest nieznane. |
| Brak deszczomierza w obecnym polu | `r...` | Brak danych o opadzie z ostatniej godziny. |
| Zmierzony brak opadów | `r000` | Odczyt wynosi dokładnie 0,00 cala. |

**Nie trzeba transmitować pustych pól opcjonalnych.** `r...` i pominięcie `r` oznaczają brak dostępnego odczytu, podczas gdy `r000` jest konkretnym wynikiem pomiaru. W polach obowiązkowych nie wolno jednak usuwać całego elementu tylko dlatego, że brakuje czujnika.

Przykład minimalnego nieskompresowanego raportu stacji posiadającej tylko deszczomierz:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_.../...t...r012
```

Po symbolu `_` znajdują się obowiązkowe pola `.../...` i `t...`. Pominięto opcjonalne `g`, ponieważ stacja nie mierzy porywów. Jedyny dostępny pomiar to `r012`: w ostatniej godzinie spadło 0,12 cala deszczu. Jest to poprawna sytuacja: **format wymaga obecności określonych pól, ale nie wymaga wyposażenia w każdy czujnik**.

W przeciwieństwie do niego zapis:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_r012
```

nie jest równoważny. Pomija pola wymagane w takim nieskompresowanym raporcie i nie powinien być generowany jako poprawny Complete Weather Report.

Po obowiązkowych elementach kolejne parametry nie muszą występować w komplecie ani zawsze w tej samej kolejności. Parser powinien rozpoznawać ich identyfikatory i ustalone szerokości, zamiast oczekiwać pełnej sekwencji wszystkich pomiarów.

## Pozycja, czas i obiekty

Kompletny raport może mieć pozycję nieskompresowaną albo skompresowaną. W wariancie nieskompresowanym rozszerzenie `ddd/sss` znajduje się bezpośrednio za symbolem pogodowym. W wariancie skompresowanym informacja o wietrze zajmuje odpowiednie pola skompresowanej pozycji, więc nie dopisuje się ponownie siedmiobajtowego `ddd/sss`. Przykłady zastępowania wiatru kropkami w tym artykule dotyczą postaci nieskompresowanej, a nie bajtów kompresji.

Raport ze znacznikiem czasu może wyglądać następująco:

```text
SQ9MDD>APRS:@282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

DTI `@` oznacza wariant pozycji ze znacznikiem czasu i deklaracją obsługi wiadomości APRS. Zapis `282000z` oznacza 28. dzień miesiąca, godzinę 20:00 UTC.

Pomiary można przypisać również do obiektu APRS, np. gdy jedna stacja publikuje dane z odległego czujnika:

```text
SQ9MDD>APRS:;WX-KRAKOW*282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

W takim przypadku raport zaczyna się od DTI `;`, a nazwa `WX-KRAKOW` identyfikuje obiekt. Współrzędne i dane pogodowe opisują obiekt, niekoniecznie lokalizację stacji nadającej pakiet.

## Raport pogodowy bez pozycji

Positionless Weather Report rozpoczyna się od DTI `_`. Po nim znajduje się ośmiocyfrowy zapis czasu w postaci `MMDDHHMM`, a następnie pola pomiarowe. Kierunek i prędkość wiatru są tu oznaczone literami `c` i `s`, a nie zapisywane jako `ddd/sss`.

Przykład z APRS Protocol Reference:

```text
_10090556c220s004g005t077r000p000P000h50b09900wRSW
```

| Fragment | Znaczenie |
| --- | --- |
| `_` | DTI raportu pogodowego bez pozycji. |
| `10090556` | 9 października, 05:56. |
| `c220s004` | Wiatr 220°, 4 mph. |
| `g005t077` | Poryw 5 mph, temperatura 77°F. |
| `r000p000P000` | Trzy niezależne pomiary opadów. |
| `h50b09900` | Wilgotność 50%, ciśnienie 990,0 hPa. |
| `wRSW` | Historyczny identyfikator oprogramowania i urządzenia pogodowego. |

W tym wariancie obowiązkowy jest **początkowy zestaw**: `_` + ośmiocyfrowy czas `MMDDHHMM` + `cxxx` + `sxxx` + `gxxx` + `txxx`. Kolejność tych pól należy zachować. Pozostałe parametry mogą występować później, w różnej kolejności, albo zostać całkowicie pominięte. Tu `s` oznacza prędkość wiatru, a nie opad śniegu.

Przykład stacji wyposażonej wyłącznie w deszczomierz, zaczerpnięty co do struktury z APRS101:

```text
_10090556c...s...g...t...P012
```

Także tutaj pola `c`, `s`, `g` i `t` **muszą wystąpić**, mimo że żaden z tych pomiarów nie jest dostępny. `P012` oznacza 0,12 cala opadu od północy. Gdyby kierunek wiatru wynosił rzeczywiście 0°, należałoby użyć `c000`, a nie `c...`.

Pakiet nie zawiera współrzędnych. Aby pokazać stację pogodową na mapie, odbiornik musi znać jej położenie z wcześniej odebranego raportu pozycji. Z tego powodu późniejsze zalecenia APRS preferują kompletny raport zawierający jednocześnie pozycję i aktualne pomiary.

## Historyczne raporty surowe

Starsze stacje meteorologiczne mogły wysyłać własny zapis danych, bez konwersji do ogólnego formatu WX. APRS101 wymienia następujące identyfikatory:

| DTI | Historyczny format urządzenia |
| --- | --- |
| `!` | Ultimeter 2000 |
| `#` | Peet Bros U-II |
| `$` | Ultimeter 2000 |
| `*` | Peet Bros U-II |

Przykładowy surowy raport Peet Bros U-II z dokumentacji:

```text
#50B7500820082
```

Te identyfikatory częściowo pokrywają się z DTI innych rodzajów danych APRS. Poprawna identyfikacja wymaga więc sprawdzenia dalszej składni. Przy projektowaniu nowego nadajnika należy przekształcić odczyty urządzenia do kompletnego formatu WX, zamiast transmitować surowy format producenta.

## Dodatkowe pola pogodowe

Poza podstawowymi pomiarami dokumentacja przewiduje także dodatkowe pola. Nie wszystkie urządzenia i aplikacje je obsługują.

| Pole | Znaczenie | Uwagi |
| --- | --- | --- |
| `Lxxx` | Nasłonecznienie / irradiancja | 0-999 W/m². |
| `lxxx` | Nasłonecznienie / irradiancja | 1000 W/m² i więcej; do trzech cyfr dodaje się 1000. |
| `sxxx` | Opad śniegu z ostatnich 24 godzin | W calach; w kompletnym raporcie `s` nie koliduje z pozycją prędkości wiatru. |
| `#xxx` | Surowy licznik deszczomierza | Bez uniwersalnej jednostki; interpretacja zależy od urządzenia. |

Przykładowo `L700` oznacza 700 W/m², natomiast `l123` oznacza 1123 W/m². W raporcie bez pozycji identyfikator `s` jest już używany do prędkości wiatru, dlatego nie należy dodawać w nim pola śniegu w sposób powodujący tę kolizję.

### Wodowskazy: rozszerzenie z 2006 roku

W czerwcu 2006 r. opisano wykorzystanie APRS do przekazywania poziomu wody i sygnalizowania powodzi. Wprowadzono symbole `/w` (wodowskaz) i `\w` (powódź) oraz zaproponowano możliwość dołączania pomiarów poziomu wody do klasycznych danych pogodowych.

Przykładowy zapis historycznie stosowany przez wodowskazy w sieci FIRENET ma postać obiektu APRS:

```text
;09428508 *061713z3401.40N/11424.75Ww3.57gh/82cfs
```

W tym zapisie `3.57gh` opisuje poziom odczytany przez wodowskaz, a `82cfs` przepływ w stopach sześciennych na sekundę. To **tekstowy opis obiektu**, a nie pole `Fxxxx` klasycznego raportu WX. Według aktualizacji z marca 2011 r. właśnie taki zapis występował wówczas w praktyce, mimo wcześniejszych propozycji rozszerzenia formatu pogodowego.

Propozycja przekazywania dodatkowych pomiarów wewnątrz WX obejmowała następujące pola:

| Pole | Znaczenie w dokumentacji rozszerzenia |
| --- | --- |
| `Fxxxx` | Poziom wody względem poziomu odniesienia, w dziesiątych częściach stopy, z możliwością wartości dodatnich i ujemnych. |
| `Vxxx` | Napięcie zasilania w dziesiątych częściach wolta, np. `V128` = 12,8 V. |
| `Zxx` | Kod typu urządzenia przewidziany w rozszerzonym opisie czujnika. |

Dla stacji pogodowej posiadającej wodowskaz proponowano zachowanie klasycznej struktury WX, np. `.../...t...V128F+123` (w tym przykładzie 12,3 stopy powyżej poziomu odniesienia), zamiast definiowania całkowicie nowego raportu. Opisy z 2011 r. rozróżniają wodowskazy przekazujące własne dane tekstowe od stacji pogodowych przenoszących rozszerzone pola pomiarowe. Sam symbol wodowskazu nie gwarantuje obecności pól WX.

### Fukushima i propozycja pomiaru promieniowania z 2011 roku

Po katastrofie w elektrowni jądrowej Fukushima Daiichi w marcu 2011 r. pojawiła się potrzeba przekazywania również odczytów promieniowania. Bob Bruninga bezpośrednio odniósł się do wydarzeń w Japonii w dokumencie *APRS 1.2.1 Weather Updates to the Spec* z 24 marca 2011 r. Proponowane rozwiązanie wykorzystywało istniejący format raportu pogodowego zamiast tworzyć całkowicie osobny sposób transmisji.

Nowe pole `Xxxx` miało kodować moc dawki promieniowania w nanosiwertach na godzinę (`nSv/h`). Po literze `X` następowały trzy cyfry: dwie cyfry znaczące i wykładnik potęgi dziesięciu. Przykładowo:

| Pole | Sposób odczytu | Wynik |
| --- | --- | --- |
| `X123` | 12 × 10³ nSv/h | 12 µSv/h |
| `X456` | 45 × 10⁶ nSv/h | 45 mSv/h |

Równolegle proponowano oznaczanie rodzaju czujnika lub zagrożenia za pomocą nakładek na istniejące symbole: zwykły symbol pogodowy dla odczytów tła, nakładkę `R` dla stacji monitorującej promieniowanie oraz odpowiednią nakładkę symbolu zagrożenia po przekroczeniu ustalonego progu. Pozwalałoby to wykorzystać dotychczasowy mechanizm prezentacji czujników na mapie i rozróżnić pomiar od sygnalizowanego zagrożenia.

**Status tego rozwiązania ma znaczenie:** dokument z marca 2011 r. opisuje `Xxxx` jako *propozycję* rozszerzenia. Nie należy zakładać obsługi tego pola i proponowanych nakładek przez wszystkie współczesne aplikacje ani traktować go jako obowiązkowego elementu APRS101. Podobnie `Fxxxx`, `Vxxx` i `Zxx` wymagają uwzględnienia rzeczywistej zgodności oprogramowania odbiorczego.

## Symbole a rozpoznawanie pogody

Klasyczny symbol stacji pogodowej to `/_`, a tabela alternatywna pozwala użyć `\_`. W wyjaśnieniach APRS 1.1 uwzględniono ponadto `/W` i `\W` jako alternatywne symbole związane ze stacjami pogodowymi. Dokument z 2011 r. proponuje ujednolicenie rozpoznawania czujników przez użycie symboli pogodowych z nakładkami oraz symboli zagrożeń. Ta późniejsza propozycja nie zmienia reguł dekodowania podstawowych pól WX.

Nie oznacza to, że każdy pakiet zawierający znak `_` jest raportem WX. W raporcie z pozycją należy rozpoznać kod symbolu w odpowiednim miejscu i zweryfikować składnię następujących po nim danych. W raporcie bez pozycji `_` pełni inną funkcję: jest pierwszym bajtem pola Information, czyli DTI.


## Struktura kompletnego raportu WX

Poniższa tabela opisuje nieskompresowany raport pogodowy
z pozycją, uwzględniając podstawowy format APRS, pola
dodatkowe oraz późniejsze propozycje rozszerzeń.

| Pole | Wymagane | Znaczenie | Jednostka / kodowanie |
|---|---|---|---|
| `!` | Tak | DTI raportu pozycji | Alternatywnie `=`, `/`, `@` |
| `5215.01N` | Tak | Szerokość geograficzna | DDMM.mmN/S |
| `/` | Tak | Tabela symboli | `/` lub `\` |
| `02055.58E` | Tak | Długość geograficzna | DDDMM.mmE/W |
| `_` | Tak | Symbol stacji pogodowej | WX |
| `220` | Tak | Kierunek, z którego wieje wiatr | 000-360°, brak: `...` |
| `/` | Tak | Separator | Stały znak |
| `004` | Tak | Średnia prędkość wiatru z 1 minuty | mph, brak: `...` |
| `g005` | Zalecane* | Maksymalny poryw z ostatnich 5 minut | mph, brak: `g...` |
| `t030` | Tak | Temperatura powietrza | °F, brak: `t...` |
| `r000` | Nie | Opad z ostatniej godziny | 0,01 cala |
| `p000` | Nie | Opad z ostatnich 24 godzin | 0,01 cala |
| `P000` | Nie | Opad od północy | 0,01 cala |
| `h00` | Nie | Wilgotność względna | %, `00` = 100% |
| `b10218` | Nie | Ciśnienie atmosferyczne | 0,1 hPa, `10218` = 1021,8 hPa |
| `L840` | Nie | Promieniowanie słoneczne | 0-999 W/m² |
| `l123` | Nie | Promieniowanie słoneczne | 1000-1999 W/m², tutaj 1123 W/m² |
| `s002` | Nie | Opad śniegu z ostatnich 24 godzin | cale |
| `#123` | Nie | Surowy licznik opadów | Impulsy, zależne od urządzenia |
| `F+123` | Nie | Poziom wody względem poziomu odniesienia | 0,1 stopy, tutaj +12,3 stopy |
| `fxxxx` | Nie | Historyczna propozycja poziomu wody | Metry, zastąpiona przez `F` |
| `V128` | Nie | Napięcie zasilania | 0,1 V, tutaj 12,8 V |
| `X123` | Nie | Moc dawki promieniowania | nSv/h, `12 × 10³` = 12 µSv/h |
| `Zxx` | Nie | Kod typu urządzenia | Propozycja APRS 1.2 |
| `wRSW` | Nie | Identyfikator oprogramowania i urządzenia WX | Historyczny przykład APRS101 |

*W kompletnym raporcie `gxxx` może być pominięte według
późniejszych wyjaśnień. Jego obecność jest jednak
zalecana dla zgodności ze starszym oprogramowaniem.*

Pola `L` i `l` są alternatywne. Podobnie nie należy
jednocześnie stosować `F` i historycznego `f`.

Pola `F`, `V`, `X` i `Z` pochodzą z późniejszych propozycji
rozszerzenia protokołu. Ich obsługa nie jest gwarantowana.

Pola wymagane muszą występować nawet wtedy, gdy pomiar
jest niedostępny. Wówczas stosujemy kropki. Pola
opcjonalne bez dostępnego pomiaru można pominąć.

### Przykłady raportów

**1. Minimalny raport, tylko temperatura**

```text
!5215.01N/02055.58E_.../...g...t030
```

Temperatura 30°F. Brak pomiarów wiatru i porywów.

**2. Temperatura i wiatr**

```text
!5215.01N/02055.58E_220/004g005t068
```

Wiatr z kierunku 220°, średnia prędkość 4 mph,
porywy 5 mph, temperatura 68°F (20°C).

**3. Temperatura, wilgotność i ciśnienie**

```text
!5215.01N/02055.58E_.../...g...t068h72b10132
```

Temperatura 20°C, wilgotność 72% i ciśnienie
1013,2 hPa. Brak danych o wietrze.

**4. Typowy kompletny raport meteorologiczny**

```text
!5215.01N/02055.58E_220/004g005t068r012p018P018h72b10132L840
```

Raport zawiera wiatr, porywy, temperaturę, trzy
pomiary opadów, wilgotność, ciśnienie i promieniowanie
słoneczne.

**5. Maksymalny przykład obejmujący dostępne pola**

```text
!5215.01N/02055.58E_220/004g005t068r012p018P018h72b10132L840s002#123F+123V128X123Z00wRSW
```

Przykład demonstracyjny, obejmujący również pola
historyczne i proponowane rozszerzenia. Kod `Z00`
ilustruje składnię, nie stanowi zalecenia wyboru
typu urządzenia.

Długość pola Information: **88 bajtów**.

Limit pola Information w APRS/AX.25: **256 bajtów**.

Przykład mieści się w limicie, jednak nie należy
transmitować wszystkich dostępnych pól bez potrzeby.


## CWOP: od stacji APRS do profesjonalnych obserwacji meteorologicznych

Format WX znalazł zastosowanie również poza sieciami krótkofalarskimi. Przykładem jest **Citizen Weather Observer Program (CWOP)**, wywodzący się z inicjatywy APRSWXNET i środowiska radioamatorskiego. Program umożliwia ochotnikom przekazywanie pomiarów z prywatnych stacji pogodowych do wspólnego zasobu danych meteorologicznych. Mogą w nim uczestniczyć zarówno krótkofalowcy, jak i obserwatorzy przesyłający raporty bezpośrednio przez Internet, bez korzystania z transmisji radiowej.

Od 1 lipca 2001 r. obserwacje CWOP trafiają do systemu **MADIS (Meteorological Assimilation Data Ingest System)**, rozwijanego przez amerykańską NOAA. MADIS integruje pomiary pochodzące z wielu niezależnych źródeł, ujednolica ich format, jednostki i znaczniki czasu oraz przeprowadza automatyczną kontrolę jakości. Do danych dołączane są informacje o wynikach tej kontroli, tak aby odbiorcy mogli uwzględnić wiarygodność poszczególnych obserwacji.

W przypadku stacji krótkofalarskiej raport WX może dotrzeć do APRS-IS przez radiowy IGate. Stacje CWOP mogą również przesyłać dane odpowiednim połączeniem internetowym. Uproszczony przepływ dla **stacji uczestniczących w CWOP** wygląda następująco:

```text
Stacja WX -> radio -> IGate -> APRS-IS --+
                                        +-> CWOP / APRSWXNET -> NOAA MADIS
Stacja WX -> Internet -------------------+                         |
                                                                  +-> służby meteorologiczne
                                                                  +-> instytucje badawcze
                                                                  +-> uczelnie i inni odbiorcy
```

To schemat funkcjonalny, a nie opis wszystkich wewnętrznych połączeń. Sposób pobierania danych przez MADIS zmieniał się z czasem: od 2023 r. NOAA wskazuje bezpośrednie pozyskiwanie danych z serwerów APRSWXNET i CWOP, zamiast wcześniejszego pośrednictwa serwisu findU.

Dane CWOP są udostępniane licznej grupie użytkowników meteorologicznych, w tym biurom prognoz amerykańskiej **National Weather Service (NWS)**, ośrodkom badawczym, uniwersytetom i podmiotom prywatnym. Mogą uzupełniać obserwacje ze stacji profesjonalnych, wspierać monitorowanie warunków lokalnych, weryfikację prognoz oraz zastosowania modelowe. NWS wskazuje również wykorzystanie takich obserwacji przy przygotowywaniu prognoz i ostrzeżeń pogodowych. Nie oznacza to jednak, że każdy pojedynczy odczyt jest wykorzystywany we wszystkich tych zastosowaniach.

### Jakość i rejestracja stacji mają znaczenie

Praktyczna wartość takich raportów zależy nie tylko od poprawnej składni WX. Niezbędne są także właściwe rozmieszczenie czujników, prawidłowe jednostki i czas pomiaru, aktualne współrzędne stacji oraz unikanie błędów pomiarowych. Kontrola jakości MADIS pozwala wykrywać część anomalii i oznaczać podejrzane dane, ale nie zastępuje prawidłowej instalacji stacji meteorologicznej.

**Nie każdy raport pogodowy widoczny w APRS-IS automatycznie trafia do MADIS.** Uczestnictwo w CWOP wymaga rejestracji i poprawnego skonfigurowania sposobu przekazywania danych. NOAA udostępnia oddzielny formularz dla nowych uczestników i aktualizacji istniejących stacji, w tym dla krótkofalowców posługujących się znakami wywoławczymi.

CWOP pokazuje szersze znaczenie formatu WX: poprawnie zakodowany pakiet APRS może być nie tylko informacją wyświetlaną na mapie, lecz także elementem systemu gromadzenia i udostępniania pomiarów wykorzystywanych przez zawodową meteorologię.

## Metodyka pomiarów: zgodność APRS a jakość danych CWOP

Poprawnie zbudowana ramka WX nie gwarantuje wiarygodnych danych. Specyfikacja APRS opisuje format i znaczenie pól, natomiast [przewodnik CWOP z 2005 r.](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf) określa zalecaną metodykę pomiarową, parametry urządzeń i warunki instalacji. Oba dokumenty należy czytać łącznie, lecz nie utożsamiać ich wymagań. Celem własnej implementacji powinno być poprawne raportowanie APRS i możliwie wysoka jakość obserwacji przekazywanych do CWOP/MADIS, a nie deklarowanie gwarantowanego przejścia kontroli jakości.

### Wiatr: dwa różne sposoby wyznaczania wartości

| Parametr | Klasyczny APRS WX | Zalecenia przewodnika CWOP z 2005 r. |
| --- | --- | --- |
| Średnia prędkość wiatru | Średnia z ostatniej 1 minuty | Średnia z ostatnich 2 minut |
| Kierunek wiatru | Kierunek, z którego wieje wiatr, w stopniach | Średni kierunek z ostatnich 2 minut, względem północy geograficznej |
| Poryw `gxxx` | Maksymalna prędkość z ostatnich 5 minut | Maksymalny odczyt prędkości z ostatnich 10 minut |
| Próbkowanie | `WX.TXT` opisuje historyczny przykład czterech próbek co 15 sekund do obliczenia średniej minutowej | Przewodnik CWOP zaleca odczyt czujników przynajmniej co 5 sekund |

Rozbieżność jest udokumentowana. Autorzy przewodnika CWOP sami umieścili zmianę okresów z **1 do 2 minut** i z **5 do 10 minut** na liście postulowanych zmian formatu APRS. Nie należy zatem przedstawiać okresów CWOP jako definicji pól przyjętej w APRS101. Co istotne, pola `ddd/sss` i `gxxx` nie zawierają metadanych informujących odbiorcę o zastosowanym okresie pomiarowym. W oprogramowaniu obsługującym oba zastosowania warto przechowywać próbki źródłowe i obliczać oddzielne wyniki dla profilu APRS oraz profilu pomiarowego CWOP. Profil nadawanego raportu musi być świadomie wybrany i udokumentowany.

Do wyznaczania średniej prędkości wystarczy odpowiednie okno czasowe. Kierunków nie należy jednak uśredniać zwykłą średnią arytmetyczną: pomiary 359° i 1° oznaczają wiatr z północy, a nie ze 180°. Należy zastosować średnią kołową lub właściwy algorytm wektorowy, z uwzględnieniem sposobu, w jaki czujnik dostarcza pomiary. Przy ciszy i niewiarygodnym kierunku nie należy tworzyć pozornej precyzji. Warto też pamiętać, że maksimum próbek chwilowych opisane w przewodniku CWOP nie musi odpowiadać innym definicjom porywu, np. maksymalnej średniej 3-sekundowej stosowanej w metodyce WMO.

### Opady: trzy niezależne okresy

Najbezpieczniejszą podstawą obliczeń jest ciągła historia przyrostów opadu z czasem ich wystąpienia, np. zdarzeń z deszczomierza korytkowego. Na tej podstawie generator przygotowuje osobno `rxxx` dla ostatnich 60 minut, `pxxx` dla ruchomych 24 godzin i `Pxxx` dla okresu od **lokalnej północy stacji**. Nie wolno wyliczać `p` z `P` ani zastępować ruchomego okna opadem od początku bieżącej doby. Wyniki koduje się w setnych częściach cala, niezależnie od jednostki czujnika.

Po restarcie urządzenia lub utracie historii nie należy raportować niepełnej sumy tak, jakby obejmowała cały wymagany okres. Jeśli archiwum nie pozwala odtworzyć danego okna, trzeba oznaczyć odpowiedni pomiar jako niedostępny lub pominąć opcjonalne pole. Przy zerowaniu licznika deszczomierza należy oddzielić rzeczywisty opad od skoku wywołanego resetem. Ważne są też prawidłowa strefa czasowa stacji oraz zachowanie znaczników czasu dla przyrostów.

### Temperatura, wilgotność i ciśnienie

APRS określa jednostki i sposób kodowania tych pól, ale nie narzuca im jednolitego okna uśredniania. Przewodnik CWOP z 2005 r. **zaleca** średnią temperatury z ostatnich 5 minut i średnią z ostatniej minuty dla wilgotności wykorzystywanej do obliczania punktu rosy. To zalecenia jakościowe, a nie dodatkowe obowiązkowe bajty raportu WX. Każdy wynik powinien odpowiadać rzeczywistemu czasowi pomiaru, a nie ostatniej przypadkowo zachowanej wartości.

Szczególnej uwagi wymaga pole `bxxxxx`: APRS podaje jego jednostkę (dziesiąte części hPa), lecz samo poprawne kodowanie nie rozstrzyga, **jakie ciśnienie** zwraca urządzenie. Przewodnik CWOP z 2005 r. jako przyjęty parametr wskazuje *altimeter setting* (QNH), czyli ciśnienie zredukowane według właściwej metody, a nie surowy odczyt na wysokości czujnika. Nie należy też bez sprawdzenia utożsamiać QNH z meteorologicznym ciśnieniem na poziomie morza (QFF). Przed wysyłaniem danych do CWOP trzeba sprawdzić rodzaj wartości eksportowanej przez stację, jej kalibrację oraz zalecenia stosowanego oprogramowania.

### Instalacja czujników i kontrola jakości

Algorytm nie naprawi błędów wynikających z niewłaściwej lokalizacji urządzeń. Przewodnik CWOP zaleca osłonięty przed promieniowaniem, wentylowany termometr około 1,5 m nad reprezentatywnym podłożem, anemometr docelowo na wysokości 10 m w możliwie otwartym miejscu oraz deszczomierz wypoziomowany i chroniony przed zakłóceniami przepływu powietrza. W zabudowie kompromisy bywają nieuniknione, ale warto je dokumentować. Metadane stacji, zwłaszcza współrzędne i wysokość, muszą odpowiadać rzeczywistemu położeniu pomiarów.

[MADIS](https://madis.ncep.noaa.gov/madis_qc.shtml) wykonuje kontrole poprawności zakresów, spójności wewnętrznej, zmian w czasie i zgodności przestrzennej, zależnie od parametru i dostępnego poziomu kontroli. Wyniki mają postać oznaczeń jakości przypisanych do obserwacji. Nie jest to uniwersalny test, który raz na zawsze zatwierdza całą stację; nawet prawidłowy pomiar może zostać oznaczony jako podejrzany, a formalnie poprawna ramka może zawierać błędne wartości. Operator powinien regularnie analizować informacje zwrotne, porównywać pomiary z właściwymi stacjami referencyjnymi i kontrolować kalibrację.

### Wskazówki dla autora oprogramowania WX

Oddziel trzy etapy przetwarzania: **pobranie próbek**, **wyznaczenie obserwacji** i **kodowanie APRS**. Dzięki temu zmiana częstotliwości transmisji nie zmienia przypadkowo okresów uśredniania, a ten sam strumień pomiarów może zasilać różne profile obserwacyjne. W szczególności:

1. Przechowuj czas każdej próbki, jednostkę źródłową, informację o ważności odczytu i wystarczającą historię dla najdłuższego używanego okna pomiarowego.
2. Rozpoznawaj braki danych, utratę łączności z czujnikiem, reset licznika i niepełne okna po uruchomieniu; nie zamieniaj tych sytuacji na pomiar zerowy.
3. Wyliczaj statystyki na danych źródłowych, a dopiero potem zaokrąglaj i przeliczaj na jednostki ramki APRS. Unikaj wielokrotnych konwersji i zaokrągleń pośrednich.
4. Zachowuj opis zastosowanej metodyki, okresów uśredniania i konfiguracji czujników. Pozwoli to poprawnie interpretować dane oraz diagnozować ewentualne oznaczenia jakości MADIS.

Sama zgodność protokołu nie gwarantuje przyjęcia danych do zbiorów CWOP ani pozytywnej oceny każdego pomiaru. Do tego potrzebne są rejestracja stacji, poprawny kanał przesyłania, wiarygodne pomiary i stała kontrola ich jakości.

## Uwagi implementacyjne

Niezależnie od metodyki pomiarów generator i parser WX muszą zachować zgodność składniową z protokołem. Szczególnie istotne są następujące zasady:

1. Preferuj Complete Weather Report, który przenosi pozycję i pomiary w jednej transmisji.
2. Zachowuj jednostki protokołu na wejściu i wyjściu. Konwersję do jednostek metrycznych wykonuj dopiero na poziomie prezentacji lub przed zakodowaniem wartości nadawanych.
3. Rozróżniaj `r`, `p` i `P` jako trzy różne przedziały pomiaru opadu.
4. Rozdziel walidację **obecności wymaganych pól** od oceny dostępności samych pomiarów. W nieskompresowanym raporcie kompletnym zachowaj `ddd/sss` i `t`; w raporcie bez pozycji zachowaj czas, `c`, `s`, `g` i `t`.
5. Nie utożsamiaj niedostępnego pomiaru z wartością zero; uwzględniaj kropki, spacje i dopuszczalne pominięcia pól. Preferuj kropki ze względu na czytelność zrzutów tekstowych.
6. Rozpoznawaj odmienny zapis wiatru w raporcie kompletnym, bez pozycji i ze skompresowaną pozycją.
7. Oddzielaj klasyczne pola WX od późniejszych rozszerzeń i zachowuj możliwość zignorowania nieznanych pól. Nie zakładaj, że wszystkie klienty obsługują zaproponowane w 2011 r. `Xxxx`.
8. Nie traktuj raportu pomiarowego jak ostrzeżenia pogodowego. Komunikaty o zagrożeniach, w tym NWS-WARN i inne systemy alarmowe, wykorzystują odrębne mechanizmy.
9. Nie zmieniaj semantyki pól tylko dlatego, że aplikacja współpracuje z CWOP. Dokumentuj profil pomiarowy i odróżniaj wymogi formatu APRS od zaleceń dotyczących jakości danych.

## Źródła

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF), rozdział 12: Weather Reports.
- [APRS 1.1: Weather Specification Comments](https://www.aprs.org/aprs11/spec-wx.txt), WB4APR, aktualizacja z 24 marca 2011 r.
- [APRS 1.2.1: Weather Updates to the Spec](https://www.aprs.org/aprs12/weather-new.txt), WB4APR, 24 marca 2011 r.; dokument zawiera również propozycje rozszerzeń.
- [Water Gauges in APRS](https://www.aprs.org/aprs12/watergage.txt), WB4APR, 2006 r.; aktualizacja z 24 marca 2011 r.
- [IAEA: informacje o awarii Fukushima Daiichi](https://www.iaea.org/newscenter/news/fukushima-nuclear-accident-update-log-20), dokumentacja wydarzeń z marca 2011 r.
- WB4APR, *WX.TXT: Using APRS in Weather and SKYWARN Applications*, wersja 8.3.5, 10 marca 1999 r., aktualizacja z 18 sierpnia 2010 r. (materiał historyczny).
- [NOAA MADIS: Citizen Weather Observer Program Data](https://madis.ncep.noaa.gov/madis_cwop.shtml), cele CWOP, historia i przetwarzanie danych.
- [NOAA MADIS: APRSWXNET/CWOP Snow Project](https://madis.ncep.noaa.gov/snow_project.shtml), odbiorcy danych oraz przykłady wykorzystania obserwacji.
- [NOAA NWS: Join CWOP](https://www.weather.gov/pub/JoinCWOP), zastosowanie raportów w prognozowaniu i ostrzeganiu.
- [NOAA MADIS: Registration and Update Form](https://madis.ncep.noaa.gov/cwop_signup.shtml), zasady zgłaszania stacji.
- [CWOP Weather Station Siting, Performance, and Data Quality Guide](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf), wersja 1.0 z 8 marca 2005 r.; zalecenia pomiarowe i historyczna lista postulowanych zmian APRS.
- [NOAA MADIS: Quality Control](https://madis.ncep.noaa.gov/madis_qc.shtml), ogólny opis kontroli jakości i oznaczeń obserwacji.
- [NOAA MADIS: Meteorological Surface Quality Control](https://madis.ncep.noaa.gov/madis_sfc_qc.shtml), zakres i poziomy kontroli obserwacji powierzchniowych.
- [WMO Guide to Meteorological Instruments and Methods of Observation](https://www.weather.gov/media/epz/mesonet/CWOP-WMO8.pdf), odniesienie do międzynarodowych definicji pomiaru wiatru i porywów.
- [NOAA MADIS: Recent Updates](https://madisqa.ncep.noaa.gov/madis_recent.shtml), zmiana sposobu pozyskiwania danych CWOP w 2023 r.
