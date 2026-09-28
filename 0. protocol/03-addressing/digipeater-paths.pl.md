---
title: Ścieżki APRS w praktyce
---

Ścieżka APRS określa, które digipeatery mogą powtórzyć ramkę radiową i w jakiej kolejności. Może wskazywać konkretne stacje albo korzystać z aliasów obsługiwanych przez wiele digipeaterów. O rzeczywistym przebiegu transmisji decydują zarówno zapis ścieżki, jak i konfiguracja oraz wzajemne zasięgi stacji.

Poniżej opisano klasyczne adresowanie AX.25, mechanizm `WIDEn-N`, ścieżki trasowane i nietrasowane, aliasy regionalne i okolicznościowe, współczesne podejście do retransmisji przez fill-in oraz szczególny przypadek digipeaterów satelitarnych. Przykłady pokazują możliwe przekształcenia ramki przy założonej konfiguracji digipeaterów. Nie stanowią deklaracji, że wymienione aliasy działają w całej sieci APRS.

## 1. Budowa ścieżki i jej zapis

Ścieżka znajduje się w polu adresowym ramki AX.25, za adresami docelowym i źródłowym. W czytelnym zapisie TNC2 jej elementy rozdzielają przecinki:

```text
SQ9MDD-9>APRS,WIDE2-1:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SP2-2:...
```

`SQ9MDD-9` jest źródłem ramki, a `APRS` przykładowym adresem docelowym (TOCALL), nie nazwą digipeatera. Ścieżką są dopiero adresy występujące po pierwszym przecinku. Jeżeli ich nie ma, ramka jest nadawana bez żądania powtórzenia:

```text
SQ9MDD-9>APRS:...
```

Urządzenia mogą określać taką konfigurację jako `DIRECT`. Nie jest to jednak dodatkowy hop ani alias wymagający umieszczenia w ramce. Ramkę bez ścieżki nadal mogą odebrać odległe stacje oraz przekazać do APRS-IS bramki IGate, które usłyszą ją bezpośrednio.

W standardowym przetwarzaniu digipeater sprawdza **pierwszy niewykorzystany element** ścieżki. Dalsze elementy nie są aktywne, dopóki wcześniejsze nie zostaną obsłużone. Wyjątkiem są specjalnie skonfigurowane mechanizmy, takie jak preemptive digipeating, opisane dalej.

### Ograniczenia AX.25

Każdy adres w radiowej ramce AX.25 zajmuje siedem bajtów: sześć na nazwę stacji lub alias (uzupełnianą spacjami, jeśli jest krótsza) oraz jeden bajt zawierający między innymi czterobitowy SSID i znaczniki adresowe. SSID AX.25 ma zakres `0..15`. W tradycyjnym formacie wykorzystywanym przez APRS stosuje się maksymalnie osiem adresów digipeaterów. Nie oznacza to, że należy wykorzystywać wszystkie osiem: każda dodatkowa pozycja zwiększa długość ramki, a trasowanie może zużyć kolejne wolne miejsca.

Przy projektowaniu aliasów należy rozróżnić dwa przypadki:

- alias prosty, np. `ARISS` lub `RAJD`, mieści się w sześciu znakach adresu;
- w aliasie typu `n-N`, np. `RAJD2-2`, część `RAJD2` jest sześciobajtowym polem nazwy (`RAJD` + cyfra `n`), natomiast `-2` to SSID pełniący funkcję licznika `N`.

Dla jednocyfrowego `n` nazwa bazowa takiego aliasu powinna więc mieć najwyżej pięć znaków. Tekstowy zapis nie znosi ograniczeń binarnego pola adresowego AX.25.

### Gwiazdka i bit H

Każdy adres digipeatera ma własny bit **H** (*has been repeated*), informujący, że dany element ścieżki został już wykorzystany. W typowym zapisie monitorowym TNC2 gwiazdka występuje **tylko przy ostatnim wykorzystanym elemencie**; wykorzystanie wszystkich wcześniejszych jest domyślne:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...   # przed powtórzeniem
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...  # po pierwszym digi
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...  # po drugim digi
```

Ostatnia linia nie oznacza, że `SR5AAA` nie powtórzył ramki. W binarnym AX.25 bity H obu adresów są ustawione. Niektóre programy diagnostyczne pokazują gwiazdkę przy każdym wykorzystanym adresie, ale nie jest to typowy, skrócony zapis TNC2. Sam znak `*` w tekście jest reprezentacją bitu H, a nie znakiem przesyłanym jako część adresu AX.25.

## 2. Skąd wziął się mechanizm New-N Paradigm

W starszych sieciach APRS stosowano między innymi aliasy `RELAY`, `WIDE`, `TRACE` i `TRACEn-N`. Pozwalały one rozszerzać zasięg, jednak część ówczesnych implementacji powodowała nadmierne powielanie tych samych ramek. Dodatkowo pierwotny `WIDEn-N` często nie zapisywał znaków digipeaterów, co utrudniało analizę ruchu i planowanie sieci.

Zapoczątkowana pod koniec 2004 roku inicjatywa **New-N Paradigm** uporządkowała te mechanizmy:

- historyczny `RELAY` zastąpiono aliasem `WIDE1-1`, zachowując możliwość wykorzystania prostych domowych digipeaterów pomocniczych przez stacje mobilne;
- zamiast starego, pojedynczego `WIDE` upowszechniono `WIDEn-N` z licznikiem pozostałych powtórzeń;
- `WIDEn-N` zaczęto obsługiwać w trybie trasowanym, historycznie realizowanym między innymi przez `UITRACE`;
- mechanizm nietrasowany `UIFLOOD` pozostawiono między innymi do regionalnych sieci `SSn-N`;
- ograniczanie nadmiernych wartości liczników i eliminowanie duplikatów stały się podstawowymi elementami konfiguracji digipeaterów.

Nazwy `UITRACE` i `UIFLOOD` wywodzą się z określonych implementacji TNC. Inne programy mogą oferować analogiczne funkcje pod innymi nazwami. `RELAY`, stary `WIDE` i `TRACE` należy dziś traktować przede wszystkim jako element historii i starszych konfiguracji, a nie zamienne odpowiedniki współczesnego `WIDEn-N`.

## 3. Trasowanie przez konkretny znak i alias prosty

Najprostsza ścieżka wskazuje bezpośrednio digipeater, a kolejne znaki wyznaczają kolejność przekazywania:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...
```

Ramka nie zostanie powtórzona przez `SR5BBB` na pierwszym etapie tylko dlatego, że ta stacja odebrała transmisję. Najpierw musi zostać zrealizowany adres `SR5AAA`. Takie ścieżki sprawdzają się przy świadomym wyznaczaniu trasy przez określone stacje, na przykład w łączności między punktami.

Digipeater może również obsługiwać **alias prosty**, np. `RAJD`. Po odbiorze ramki adresowanej przez `RAJD` może zastąpić alias własnym znakiem albo pozostawić alias i dopisać własny znak. Sposób modyfikacji zależy od implementacji i konfiguracji:

```text
SQ9MDD-9>APRS,RAJD:...
SQ9MDD-9>APRS,SR5AAA*:...       # zastąpienie aliasu znakiem
```

W drugim wariancie identyfikacja stacji może być dołączona osobno, kosztem kolejnego pola adresowego. Alias prosty nie zawiera licznika pozostałych powtórzeń. Nie należy więc zakładać, że zadziała jak `WIDE2-2` lub że wszystkie stacje rozumiejące tę samą nazwę zapewnią identyczne zasady powtarzania.

## 4. Jak czytać `n-N`

W rodzinie aliasów `WIDEn-N` litera `n` oznacza cyfrę przed myślnikiem, a `N` jest wartością SSID po myślniku:

```text
WIDE2-2
    ^ ^
    n N
```

`n` określa klasę aliasu i deklarowaną początkową liczbę hopów; **`N` jest licznikiem powtórzeń, które jeszcze pozostały**. Zwykle ramka rozpoczyna trasę z `n = N`, ale `WIDE2-1` także jest poprawnym przykładem: należy do rodziny `WIDE2`, lecz prosi już tylko o jeden hop.

Przy każdym zgodnym powtórzeniu licznik `N` maleje o jeden. Uproszczony przebieg bez identyfikacji kolejnych digi wygląda tak:

```text
WIDE2-2 -> WIDE2-1 -> WIDE2*
SP2-2   -> SP2-1   -> SP2*
```

Po wyczerpaniu licznika element może być widoczny bez `-0`, gdyż zerowy SSID pomija się w zapisie tekstowym. Bit H oznacza go wówczas jako wykorzystany. Poszczególne implementacje różnią się sposobem zachowania lub zastępowania wyczerpanego aliasu, dlatego w rzeczywistym logu nie zawsze zobaczymy dokładnie taki sam zestaw pól.

`WIDE2-2` nie gwarantuje **dwóch transmisji w całej sieci**. Oznacza najwyżej dwa kolejne powtórzenia *w danej gałęzi drogi pakietu*. Jeśli pierwotną ramkę usłyszy kilka digipeaterów, każdy może utworzyć własną gałąź, a łączna liczba transmisji będzie większa.

## 5. Ścieżki trasowane (trace)

W ścieżce trasowanej digipeater pozostawia informację o swojej tożsamości. Jest to zasadnicza cecha współczesnego użycia `WIDEn-N` zgodnie z New-N Paradigm: można ustalić drogę, którą przebyła odebrana kopia ramki.

Przykład jednej gałęzi trasowania `WIDE2-2`:

```text
SQ9MDD-9>APRS,WIDE2-2:...
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...
```

W przykładzie digipeatery dopisują swój znak, a wyczerpany alias pozostaje w ścieżce. Inna poprawnie skonfigurowana implementacja może zastąpić alias ostatnim znakiem i dać krótszy zapis końcowy, np. `SR5AAA,SR5BBB*`. Przy analizie logów trzeba więc uwzględnić zachowanie konkretnego oprogramowania, zamiast zakładać identyczną postać wszystkich nagłówków.

Trasowanie ułatwia diagnostykę, ale każdy dopisany znak zajmuje siedem kolejnych bajtów pola adresowego. Przy długich ścieżkach może zabraknąć miejsca na kolejne adresy.

## 6. Ścieżki nietrasowane (flood)

W wariancie nietrasowanym digipeater zmniejsza licznik aliasu, ale **nie dopisuje do ścieżki swojego znaku**. Nie jest to inny protokół ani szczególny format informacji APRS. To sposób przetwarzania adresu przez digipeatery, historycznie związany z `UIFLOOD`.

Przykład dla sieci, w której skonfigurowano nietrasowany alias regionalny `SP`:

```text
SQ9MDD-9>APRS,SP2-2:...
SQ9MDD-9>APRS,SP2-1:...
SQ9MDD-9>APRS,SP2*:...
```

Wszystkie trzy linie mogą dotyczyć tej samej ramki przekazywanej przez różne stacje. Z jej końcowego nagłówka nie wynika, które digipeatery uczestniczyły w przekazaniu. Odbiornik słyszący `SP2-1` nie może na podstawie samej ścieżki rozstrzygnąć, kto wykonał wcześniejszy hop.

Najważniejsze właściwości flood:

- długość ścieżki nie rośnie przy każdym powtórzeniu o nowy znak digi;
- brak pełnego śladu utrudnia odtwarzanie przebiegu transmisji;
- nadal konieczne są kontrola liczników i eliminacja duplikatów;
- flood nie oznacza nieograniczonego rozgłaszania: jego zasięg wyznacza grupa stacji obsługujących alias oraz ich konfiguracja.

### Flood z częściową identyfikacją

Nietrasowanie nie musi oznaczać całkowitego braku informacji o stacjach pośredniczących. Historyczne konfiguracje `UIFLOOD` z opcją `ID` pozwalały zachować między innymi informację o pierwszym i ostatnim digipeaterze obsługującym regionalną ścieżkę. W ścieżce mieszanej `WIDE1-1,SSn-N` pierwszy znak może ponadto pochodzić z osobno zrealizowanego członu `WIDE1-1`.

Takie rozwiązanie **nie daje pełnego trace**. Sposób identyfikacji i miejsce pozostawionych aliasów zależą od konkretnego TNC. Nie należy utożsamiać każdego użycia nazwy regionalnej z flood: dokładnie ten sam alias może zostać skonfigurowany do trasowania.

## 7. Ścieżki jedno- i wieloczłonowe oraz fill-in digi

Liczba członów to liczba wpisanych pozycji oddzielonych przecinkami; liczba hopów wynika z ich liczników i sposobu przetwarzania. Jeden człon może obejmować więcej niż jedno powtórzenie:

| Ścieżka | Liczba członów | Żądane hop-y w jednej gałęzi |
| --- | ---: | ---: |
| `WIDE2-1` | 1 | 1 |
| `WIDE2-2` | 1 | 2 |
| `SP2-2` | 1 | 2 |
| `WIDE1-1,WIDE2-1` | 2 | 2 |
| `WIDE1-1,WIDE2-2` | 2 | 3 |
| `SP1-1,SP2-2` | 2 | 3, jeżeli oba człony są obsługiwane |

Przy normalnym przetwarzaniu drugi człon zaczyna działać dopiero po zużyciu pierwszego. Nie oznacza to jednak, że każdy element ma być obsłużony przez inną *kategorię* urządzeń. O tym decydują skonfigurowane aliasy.

### Geneza `WIDE1-1`: lokalne fill-in dla stacji mobilnych

W starszych konfiguracjach APRS stacje mobilne korzystały między innymi ze ścieżek rozpoczynających się od `RELAY`. Pierwszy hop mogła wówczas wykonać pobliska stacja domowa z prostym TNC pracującym jako **fill-in digi**, nawet jeśli nie dysponowała ona funkcjami pełnego digipeatera regionalnego. Miało to znaczenie zwłaszcza dla mobilnych i ręcznych stacji o mniejszej mocy, gorszej antenie lub poruszających się w lokalnych zagłębieniach zasięgu. Historyczne ścieżki zawierające `RELAY` i `WIDE` sprzyjały jednak powstawaniu nadmiernej liczby duplikatów.

Wprowadzając New-N Paradigm, zastąpiono `RELAY` aliasem **`WIDE1-1`**. Było to rozwiązanie uwzględniające możliwości istniejących, nieskomplikowanych urządzeń domowych, w tym konstrukcji typu mini-digi. Takie TNC nie musiało rozumieć algorytmu `WIDEn-N` ani zmniejszać jego licznika: wystarczało ustawienie dokładnego aliasu `WIDE1-1` i jednokrotne powtórzenie ramki z oznaczeniem tego członu jako wykorzystanego. Za dalsze trasowanie odpowiadał już pełny digipeater obsługujący `WIDEn-N`.

Z tego wynika konstrukcja ścieżki przeznaczonej dla mobili korzystających z lokalnych fill-in:

```text
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...          # mobil nadaje
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...           # domowy fill-in, pierwszy hop
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...      # regionalny digi, drugi hop
```

`SR5AAA` reprezentuje tu prosty fill-in zastępujący `WIDE1-1` własnym znakiem, a `SR5BBB` obsługuje pozostający `WIDE2-1`. Bardziej rozbudowane urządzenie może zachować również zużyty alias `WIDE1`, przez co wynikowy nagłówek będzie dłuższy. Prosty digipeater obsługujący `WIDE1-1` jako zwykły alias może natomiast oznaczyć go jako wykorzystany bez zmiany jego SSID; oznaczenie bitu H, a nie koniecznie widoczny zapis `WIDE1-0`, kończy wtedy pierwszy człon.

**Klasyczny, prosty fill-in obsługujący wyłącznie `WIDE1-1` nie powinien obsługiwać `WIDE2-1` ani pozostałych członów szerszej ścieżki.** Jego zadaniem jest jednorazowe przekazanie pakietu z lokalnej luki zasięgowej do digipeatera regionalnego. Uruchamianie takich przekaźników tam, gdzie stacje mobilne mają już dobry dostęp do sieci regionalnej, dokłada niepotrzebne kopie ramek do wspólnego kanału. Inne reguły mogą obowiązywać w nowoczesnym fill-in, który warunkuje transmisję również sposobem odbioru i obserwowanym ruchem; opisano to poniżej.

`WIDE1-1` nie jest jednak zastrzeżone wyłącznie dla prostych fill-in. Pełny digipeater regionalny również może obsłużyć ten alias, jeżeli usłyszy mobil bezpośrednio. Wówczas pierwszy hop z `WIDE1-1,WIDE2-1` zostaje wykorzystany bez udziału stacji domowej; **nie oznacza to dodatkowego hopu ponad dwa żądane w tej ścieżce**.

### Dlaczego `WIDE1-1` nie było ścieżką zalecaną dla stacji domowych

Należy rozdzielić **obsługiwanie aliasu `WIDE1-1` przez domowy fill-in** od **nadawania własnych beaconów stacji domowej ze ścieżką `WIDE1-1`**. W pierwotnych zaleceniach New-N Paradigm ten pierwszy przypadek służył przede wszystkim mobilom, natomiast dla zwykłych stacji stałych przewidziano ścieżki `WIDEn-N` o liczbie hopów dobranej do regionu, historycznie często `WIDE2-2` w warunkach opisywanych przez autorów projektu. Nie projektowano `WIDE1-1` jako domyślnego pierwszego członu ścieżki stacji domowych.

Przyczyna jest topologiczna. Stacja stała dysponuje zwykle stabilną lokalizacją i korzystniejszą instalacją antenową, przez co często może dotrzeć bezpośrednio do digipeatera regionalnego. Dołączenie `WIDE1-1` aktywuje także pobliskie fill-in, które nie są jej potrzebne, a ich retransmisje mogą nakładać się na transmisję digipeatera regionalnego. Sama zamiana `WIDE2-2` na `WIDE1-1,WIDE2-1` nie zwiększa dopuszczalnej liczby hopów, za to otwiera pierwszy z nich dla dodatkowej grupy przekaźników.

Nie jest to zakaz wynikający z AX.25: wyjątkowa stacja stała w rzeczywistej luce zasięgowej może technicznie korzystać z fill-in, jeśli uzasadnia to lokalna topologia i ustalenia operatorów. Trzeba jednak odróżnić taki wyjątek od **pierwotnego przeznaczenia i zaleceń**: `WIDE1-1` wprowadzono jako sposób wsparcia stacji mobilnych przez proste, lokalne digipeatery, a nie jako uniwersalną ścieżkę dla wszystkich urządzeń APRS.

Ścieżka `SP1-1,SP2-2` działa analogicznie pod względem kolejności i liczników, **o ile** lokalna sieć ma odpowiednie reguły dla obu członów. Sam zapis `SP1-1` nie czyni z pierwszego digipeatera stacji fill-in. Dla aliasów regionalnych taka rola wymaga osobnych ustaleń operatorów.

### Współczesny fill-in: `direct-only` i `viscous delay`

Historyczna konstrukcja `WIDE1-1,WIDE2-1` rozwiązywała konkretny problem: prosty domowy mini-digi rozpoznawał jeden alias i powtarzał ramkę bez możliwości oceny, czy większy digipeater regionalny zrobił to już wcześniej. Współczesne oprogramowanie może podejmować decyzję o retransmisji także na podstawie pochodzenia ramki i ruchu obserwowanego na kanale. Nie oznacza to zmiany zasad adresowania AX.25, lecz bardziej świadome wykorzystanie dostępnych mechanizmów.

Dwa uzupełniające się rozwiązania to:

- **`direct-only`**: digipeater bierze pod uwagę tylko ramki usłyszane bezpośrednio od nadawcy, a nie kopie po wcześniejszym przekazaniu przez inny digi. W ten sposób lokalny przekaźnik nie staje się automatycznie kolejnym ogniwem każdej napotkanej trasy.
- **`viscous delay`**: digipeater wstrzymuje kwalifikującą się ramkę przez krótki, skonfigurowany czas. Jeżeli w tym czasie odbierze odpowiadającą jej retransmisję z innego digipeatera, może anulować własne nadawanie. Gdy takiej retransmisji nie zaobserwuje, nadaje oczekującą ramkę zgodnie ze swoimi regułami.

Rozwiązania te nie są nowym formatem ścieżki. APRX dokumentował *viscous digipeater* już w 2009 roku, a jego tryb `directonly` można połączyć z `viscous-delay`. Istotna jest więc nie data powstania algorytmów, ale możliwość stosowania ich zamiast bezwarunkowego powtarzania ramek przez proste mini-digi.

Przykładowo lokalny inteligentny fill-in można **celowo skonfigurować** do obsługi bezpośrednio odebranego `WIDE2-2`, z opóźnieniem i kontrolą duplikatów. Jeżeli ramkę pierwszy przekaże digi regionalny i lokalna stacja usłyszy tę retransmisję w czasie oczekiwania, fill-in zrezygnuje z TX. Jeżeli regionalny digi nie prześle ramki, lokalny fill-in może wykonać pierwszy hop, pozostawiając `WIDE2-1` do dalszej obsługi. To przykład możliwej polityki dla takiej sieci, **nie domyślne zachowanie wszystkich digipeaterów**:

```text
SQ9MDD-9>APRS,WIDE2-2:...  # transmisja mobilna

# Przypadek A: regionalny digi odbiera stację bezpośrednio
# Regionalny digi retransmituje; fill-in słyszy kopię i anuluje własny TX.

# Przypadek B: regionalny digi nie odbiera stacji bezpośrednio
# Fill-in nie słyszy innej kopii, więc po opóźnieniu retransmituje:
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
```

W tak skonfigurowanej sieci znaczenie ma nie tylko to, **który alias wpisano**, ale także **czy dany digipeater rzeczywiście musi nadawać**. Osobny pierwszy człon `WIDE1-1` może wówczas przestać być potrzebny. Nadal jednak pozostaje niezbędny tam, gdzie lokalne urządzenia obsługują wyłącznie ten alias. Również `direct-only` i `viscous delay` nie nadają ramce bez ścieżki automatycznego prawa do powtórzenia: digi musi mieć odpowiednią regułę adresowania lub świadomie skonfigurowane zachowanie specjalne.

Opóźnienie nie daje gwarancji wyeliminowania wszystkich duplikatów. Jeżeli fill-in nie słyszy transmisji digipeatera regionalnego, nie może na tej podstawie stwierdzić, że taka retransmisja nie nastąpiła. Ponadto opóźniony TX zwiększa czas dostarczenia ramki, a zbyt wiele podobnych przekaźników może nadal przeciążać wspólny kanał. Parametry i obsługiwane aliasy trzeba dobierać do rzeczywistej topologii sieci.

Wynika z tego istotna zmiana podejścia: **historyczne zalecenie ścieżki dla mobili było sposobem współpracy z ograniczeniami ówczesnej infrastruktury, a nie ponadczasową koniecznością protokołu**. W sieciach z inteligentnymi digipeaterami polityka retransmisji może mieć większe znaczenie niż tradycyjny podział na osobną ścieżkę dla mobilnej stacji z fill-in i stacji mającej bezpośredni dostęp do digi regionalnego. Pole ścieżki nadal określa jednak, które retransmisje są dopuszczalne.

## 8. Aliasy regionalne i okolicznościowe

Alias regionalny pozwala wyznaczyć *logiczną grupę digipeaterów*, które mają obsługiwać określony ruch. Klasyczna koncepcja `SSn-N` powstała po to, aby ramki mogły docierać do odległych części danego regionu bez angażowania całej sąsiedniej sieci `WIDEn-N`. W różnych krajach spotyka się różne konwencje nazewnicze i różne konfiguracje.

Przykłady możliwych zapisów:

```text
SP2-2
WM2-2
```

W obu przypadkach odpowiednio `SP` i `WM` są nazwami bazowymi aliasów, a nie automatycznie rozpoznawanymi przez protokół granicami administracyjnymi. Działają wyłącznie tam, gdzie operatorzy skonfigurowali ich obsługę. Co więcej, ten sam alias może być przetwarzany w trybie trace lub flood, zależnie od konfiguracji.

### Aliasy dla wydarzeń, ćwiczeń i aktywności

Ten sam mechanizm można wykorzystać na potrzeby rajdu, ćwiczeń łączności, imprezy krótkofalarskiej albo czasowej sieci terenowej. Załóżmy, że kilka uzgodnionych digipeaterów obsługuje bazowy alias `RAJD` w trybie nietrasowanym:

```text
SQ9MDD-9>APRS,RAJD2-2:...
SQ9MDD-9>APRS,RAJD2-1:...
SQ9MDD-9>APRS,RAJD2*:...
```

To **przykład projektowy**, a nie istniejący, powszechnie obsługiwany alias APRS. Po zakończeniu wydarzenia operatorzy mogą wyłączyć `RAJD` bez wpływania na standardową obsługę `WIDEn-N`. Gdy potrzebne jest śledzenie przebiegu wiadomości, ten sam uzgodniony alias można zamiast tego obsługiwać w trybie trasowanym.

Historycznym przykładem podobnego zastosowania jest `TEMPn-N`, opisywany w New-N Paradigm dla tymczasowych digipeaterów wykorzystywanych między innymi podczas Field Day oraz w sytuacjach awaryjnych. Nie oznacza to jednak, że każde urządzenie APRS ma fabrycznie włączony alias `TEMP`.

Utworzenie aliasu wymaga uzgodnienia co najmniej jego nazwy, obsługujących stacji, rodzaju trasowania, dopuszczalnych liczników, filtracji duplikatów i okresu działania. Należy też unikać kolizji nazw z lokalną siecią i domyślnymi regułami używanego oprogramowania.

**Wydzielenie aliasem ma charakter logiczny, nie radiowy.** Na tej samej częstotliwości każda dodatkowa retransmisja nadal zajmuje wspólny kanał. Digipeater wyposażony w kilka aliasów może też przekazywać inne ramki zgodnie z pozostałymi regułami. Lokalność aliasu nie zapewnia sama z siebie ani izolacji ruchu, ani poufności.

## 9. Aliasy satelitarne

Digipeater umieszczony na satelicie lub na Międzynarodowej Stacji Kosmicznej również może być adresowany przez pole ścieżki AX.25. Tutaj szczególne znaczenie mają **proste aliasy i konkretne znaki stacji**, a nie rozbudowane naziemne ścieżki `WIDEn-N`.

| Adres w ścieżce | Charakter |
| --- | --- |
| `ARISS` | Wspólny alias obsługiwany przez ISS i niektóre inne satelity, zależnie od aktualnej konfiguracji. |
| `APRSAT` | Historyczny alias wspólny opisywany w materiałach APRS; nie należy zakładać jego obecnej obsługi na każdym satelicie. |
| `RS0ISS`, `NA1SS` | Znaki wykorzystywane przez stację na ISS; możliwość użycia ich jako adresów digi zależy od aktywnego wyposażenia i konfiguracji. |
| Znak konkretnego satelity | Adres określony w dokumentacji danego przekaźnika, np. `W3ADO-1` lub `PCSAT-1` dla NO-44. |

Przykład użycia wspólnego aliasu przez satelitę, który go obsługuje:

```text
SQ9MDD-7>APRS,ARISS:...
```

`ARISS` jest tu jednym, prostym adresem. Nie należy bez uzasadnienia dołączać do niego naziemnego `WIDE1-1,WIDE2-1`. Po przekaźniku satelitarnym ramkę mogą odebrać liczne naziemne stacje i bramki satelitarne, ale nie zmienia to znaczenia samego aliasu.

Historyczne materiały APRS opisywały wspólne aliasy `ARISS`, `APRSAT` i `WIDE` oraz eksperymenty z bardziej złożonym przekazywaniem satelitarnym. Nie są to jednak uniwersalne współczesne ustawienia. Zestawienie AMSAT z **7 września 2026 r.** wymienia dla ISS między innymi `RS0ISS`, `NA1SS` i `ARISS`, a dla innych satelitów również odrębne adresy. Przed transmisją trzeba sprawdzić aktualny stan konkretnego satelity, obsługiwany adres, częstotliwość i rodzaj modulacji; sama obecność w zestawieniu nie gwarantuje dostępności usługi podczas danego przelotu.

## 10. Ograniczanie duplikatów i nadmiernych powtórzeń

Licznik `N` ogranicza długość pojedynczej gałęzi trasy, ale nie liczbę wszystkich kopii w sieci. Jeżeli `SR5AAA` i `SR5BBB` usłyszą bezpośrednio ramkę `WIDE2-2`, oba mogą wykonać pierwszy hop. Następnie różne sąsiednie digipeatery mogą powtórzyć otrzymane kopie po raz drugi. Dlatego dwa żądane hopy nie oznaczają tylko dwóch transmisji RF.

Poprawnie skonfigurowane digipeatery powinny wykrywać niedawno przekazane duplikaty, zwykle na podstawie źródła, adresu docelowego i pola informacji, niezależnie od zmian ścieżki. Dokładny algorytm, czas przechowywania i reguły wyjątków zależą od implementacji. Eliminacja duplikatów nie zapobiega jednak każdej kolizji: dwie stacje, które równocześnie odebrały pierwszą kopię, mogą podjąć niezależne decyzje o nadawaniu.

Ważne są również:

- ograniczanie obsługiwanych wartości `n` i `N`, w tym odrzucanie lub przycinanie zbyt długich tras (*trapping*);
- sprawdzanie, czy licznik jest sensowny, np. niedopuszczanie `WIDE1-7` w regułach `WIDEn-N`;
- unikanie ponownego przekazywania ramki przez digipeater, którego znak występuje już w wykorzystanej części ścieżki;
- kontrola częstotliwości własnych beaconów i niepotrzebnych retransmisji na wspólnym kanale.

Zbyt długi licznik w aliasie regionalnym także może powodować nadmierne obciążenie wewnątrz danego regionu. Wybór mniejszej lub większej wartości wymaga znajomości rzeczywistej topologii i lokalnych ustaleń, nie tylko deklarowanego zasięgu nadajnika.

## 11. Preemptive digipeating

Normalnie digipeater obsługuje wyłącznie pierwszy niewykorzystany element ścieżki. Niektóre implementacje oferują **preemptive digipeating**, czyli możliwość rozpoznania własnego znaku lub specjalnego aliasu w dalszej części ścieżki i odpowiedniego zmodyfikowania wcześniejszych elementów.

Mechanizm bywa użyteczny w świadomie zaprojektowanych sieciach specjalnych, gdy ramka trafi bezpośrednio do stacji znajdującej się dalej na zaplanowanej trasie. Nie jest częścią zachowania, które można zakładać dla dowolnego digipeatera APRS. Jego wynik zależy od konkretnej implementacji i wybranego trybu pomijania wcześniejszych pozycji. W zwykłych przykładach z tego artykułu zakłada się przetwarzanie bez preemption.

## 12. `RFONLY`, `NOGATE` i przejście do APRS-IS

Na końcu ścieżki można spotkać:

```text
SQ9MDD-9>APRS,WIDE2-1,RFONLY:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1,NOGATE:...
```

`RFONLY` i `NOGATE` są **znacznikami przeznaczonymi dla bramek**, a nie dodatkowymi żądaniami powtórzenia. Same nie zwiększają liczby hopów. Ich obecność w polu ścieżki nie zwalnia z ograniczeń liczby i długości adresów AX.25.

Specyfikacja APRS-IS wymienia je jako przesłanki do nieprzekazywania ramki z RF do internetu, ale **obsługa obu znaczników po stronie IGate jest opcjonalna**. Nie można zatem zagwarantować, że każda bramka zablokuje taki pakiet. Nie są to mechanizmy zapewniające prywatność transmisji radiowej.

Po poprawnym przekazaniu do APRS-IS bramka dodaje odpowiedni *q-construct*, np. `qAR` z własnym znakiem lub `qAO` w przypadku bramki tylko odbierającej. Są to elementy nagłówka internetowego, **nie część radiowej ścieżki AX.25**. Nie powinny pojawiać się w ramce emitowanej bezpośrednio przez nadajnik APRS. Szczegóły należą do osobnego zagadnienia działania IGate.

## 13. Interpretacja przykładowych ramek

| Zapis | Co można z niego ustalić |
| --- | --- |
| `SQ9MDD-9>APRS:...` | Nadawca nie zażądał digipeatingu. Nie wyklucza to bezpośredniego odbioru przez IGate. |
| `SQ9MDD-9>APRS,WIDE2-1:...` | Żądany jest jeden kolejny hop przez stację obsługującą rodzinę `WIDE2`. |
| `SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...` | `SR5AAA` występuje jako ostatni wykorzystany adres, a człon `WIDE2-1` pozostaje aktywny. |
| `SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...` | Dwa wskazane adresy są wykorzystane; gwiazdka występuje tylko przy ostatnim. |
| `SQ9MDD-9>APRS,SP2-1:...` | Pozostał jeden hop aliasu `SP`, ale przy flood nie da się odczytać znaku poprzedniego digi. |
| `SQ9MDD-9>APRS,SP2*:...` | Człon `SP2` został zużyty; nie ustala to liczby kopii odebranych przez inne stacje. |
| `SQ9MDD-9>APRS,ARISS:...` | Nadawca wskazał prosty alias `ARISS`; obsługa zależy od aktualnej konfiguracji odbierającego satelity. |
| `SQ9MDD-9>APRS,WIDE2-1,NOGATE:...` | Żądany jest jeden hop oraz sygnalizowana prośba o niebramkowanie do APRS-IS. |

Przy analizie pakietów warto oddzielać trzy kwestie: **co nadawca wpisał do ścieżki**, **jak odbierający digipeater faktycznie ją przekształcił** oraz **co dopisała później infrastruktura APRS-IS**. Bez tego łatwo pomylić brak identyfikacji w trybie flood z odbiorem bezpośrednim albo uznać kilka kopii jednej wiadomości za kolejne hopy tej samej gałęzi.

## Dokumentacja i źródła

- [Bob Bruninga, *Fixing the APRS Network: The New n-N Paradigm*](https://www.aprs.org/fix14439.html) - historia i zasady `WIDEn-N`, `SSn-N`, `UITRACE`, `UIFLOOD`, `TEMPn-N` oraz pierwotne zalecenia dla stacji mobilnych, stałych i fill-in.
- [Bob Bruninga, *MD/VA Digipeater Plan*](https://www.aprs.org/digis/digis-md.html) - historyczne, szczegółowe rozróżnienie ścieżek zalecanych dla mobili wymagających fill-in i dla stacji stałych.
- [aprs.fi, *How APRS paths work*](https://blog.aprs.fi/2020/02/how-aprs-paths-work.html) - interpretacja ścieżek AX.25 i rzeczywistych przykładów `WIDEn-N` oraz fill-in.
- [APRX, *Viscous Digipeater*](https://github.com/PhirePhly/aprx/blob/master/ViscousDigipeater.README) - opis mechanizmu opóźniania, obserwacji duplikatów i rezygnacji z transmisji przez fill-in.
- [APRX, *aprx(8)*](https://manpages.debian.org/testing/aprx/aprx.8.en.html) - tryby `directonly` i `viscous-delay` oraz ich parametry.
- [Argent Data Systems, *Digipeater Setup*](https://argentdata.com/support/digipeater_setup/) - obsługa aliasów, eliminowanie duplikatów i preemptive digipeating na przykładzie konkretnej implementacji.
- [APRS-IS, *IGate Details*](https://www.aprs-is.net/IGateDetails.aspx) - zasady bramkowania, `NOGATE`, `RFONLY` i q-construct.
- [AMSAT, *Live Digipeater Satellites*](https://www.amsat.org/live-digipeater-satellites/) - adresy i parametry satelitarnych digipeaterów; dane operacyjne należy sprawdzać ponownie przed użyciem.
- [APRS-AX.25](https://wiki.sral.fi/wiki/APRS-AX.25.en) - pola adresowe i bity H stosowane w ramkach radiowych APRS.
