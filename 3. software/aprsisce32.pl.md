---
title: APRSISCE/32
description: Rozbudowany klient APRS dla Windows i Windows Mobile, obsługujący mapy, RF, APRS-IS, wiadomości, obiekty, MultiLine, NWS-WARN, direction finding, iGate i wiele innych elementów protokołu APRS.
template: doc
tableOfContents: true
---

APRSISCE/32 to jeden z najbardziej rozbudowanych klasycznych klientów APRS.

Program łączy w jednej aplikacji:

- mapę APRS,
- połączenie z APRS-IS,
- obsługę radia i TNC,
- wiadomości,
- obiekty i itemy,
- telemetrię,
- pogodę,
- iGate,
- beaconing,
- śledzenie stacji,
- Direction Finding,
- obiekty MultiLine i obszary,
- obsługę NWS-WARN,
- zaawansowane filtrowanie APRS-IS.

Projekt został stworzony przez **Lynna Deffenbaugh KJ4ERJ**.

Dokumentacja projektu:

https://aprsisce.wikidot.com/

Pobieranie:

https://aprsisce.wikidot.com/downloads

Grupa użytkowników i wsparcia:

https://groups.io/g/APRSISCE

APRSISCE/32 jest szczególnie ciekawy dlatego, że implementuje znacznie więcej elementów protokołu APRS niż tylko pozycje stacji i proste wiadomości.

## APRSISCE i APRSIS32

Nazwa projektu obejmuje dwie główne wersje programu.

### APRSISCE

APRSISCE był przeznaczony dla urządzeń wykorzystujących:

- Windows Mobile 5,
- Windows Mobile 6,
- Windows Mobile 6.1,
- Windows Mobile 6.5,
- Windows CE.

Pozwalało to korzystać z pełnego klienta APRS na palmtopach, PDA i przemysłowych terminalach mobilnych.

### APRSIS32

APRSIS32 jest wersją przeznaczoną dla komputerów z Windows.

Pomimo nazwy działa również na 64-bitowych wersjach systemu.

Aktualna strona pobierania projektu wskazuje działającą wersję dla Windows 32 i 64 bit, w tym Windows 10 i Windows 11.

Wersje dla Windows Mobile i Windows CE mają obecnie przede wszystkim znaczenie historyczne.

## Pełny terminal APRS

Najprostszy wariant pracy wygląda tak:

```text
APRS-IS
   |
   |
APRSIS32
   |
  mapa
```

Jednak pełne możliwości programu pojawiają się po podłączeniu radia:

```text
Radio
  |
TNC / modem
  |
APRSIS32
  |
APRS-IS
```

W takim układzie jedna aplikacja może jednocześnie:

- odbierać stacje z RF,
- wyświetlać je na mapie,
- wysyłać własne beacony,
- obsługiwać wiadomości,
- przekazywać pakiety do APRS-IS,
- kierować wybrany ruch z APRS-IS na RF,
- tworzyć i wysyłać obiekty.

## Mapy

Jedną z centralnych funkcji APRSISCE/32 jest mapa.

Program wykorzystuje mapy kafelkowe, przede wszystkim OpenStreetMap.

Możliwe jest również definiowanie innych źródeł map.

Kafelki są przechowywane lokalnie, dzięki czemu wcześniej pobrany obszar może być dostępny również wtedy, gdy połączenie internetowe jest ograniczone.

Ma to duże znaczenie podczas:

- pracy terenowej,
- imprez krótkofalarskich,
- ćwiczeń,
- działań poszukiwawczych,
- pracy mobilnej.

## Stacje na mapie

APRSISCE/32 nie ogranicza się do pokazania symbolu i znaku.

W zależności od informacji zawartych w ramce może wyświetlać między innymi:

- znak,
- symbol APRS,
- komentarz,
- wysokość,
- prędkość,
- kierunek,
- pogodę,
- ścieżkę ruchu,
- częstotliwość pracy,
- informacje o wiadomościach,
- dokładność pozycji.

Program obsługuje również **position ambiguity**.

Jeżeli stacja celowo wysyła pozycję z ograniczoną dokładnością, APRSISCE/32 może wyświetlić obszar, w którym rzeczywiście znajduje się stacja.

## Śledzenie stacji

Wybraną stację można śledzić na mapie.

Program może automatycznie przesuwać mapę wraz z ruchem stacji.

Dostępny jest również mechanizm:

```text
MultiTrack
```

pozwalający otworzyć niezależne okno śledzenia konkretnej stacji.

## Historia ruchu

APRSISCE/32 zapisuje kolejne pozycje stacji i może tworzyć z nich ślad.

Ślady mogą być również zapisywane jako:

```text
GPX
```

Program posiada funkcję automatycznego zapisywania własnej trasy oraz możliwość zapisania historii ruchu innej obserwowanej stacji.

## Scroller

Bardzo charakterystycznym elementem APRSIS32 jest **Scroller** znajdujący się po lewej stronie głównego okna.

Nie jest to tylko lista znaków.

Scroller daje szybki obraz tego, co aktualnie dzieje się w sieci.

Oznaczenia pozwalają między innymi odróżnić:

```text
*  pakiet odebrany bezpośrednio przez RF
#  pakiet związany z transmisją APRS-IS -> RF
@  pakiet powtórzony przez lokalny digipeater
```

Dodatkowe kolory i oznaczenia pomagają rozróżniać rodzaj oraz pochodzenie ruchu.

Dzięki temu APRSIS32 może służyć również jako bardzo wygodne narzędzie diagnostyczne podczas obserwowania kanału APRS.

## Obsługa radia

APRSISCE/32 może współpracować z wieloma urządzeniami i modemami.

Dokumentacja projektu wymienia między innymi:

- klasyczne TNC,
- KISS TNC,
- Kenwood TM-D700,
- Kenwood TM-D710,
- Kenwood TH-D7,
- Kenwood TH-D72,
- Kenwood TH-D74,
- Argent OT-USB,
- TinyTrak4,
- AGWPE,
- UZ7HO SoundModem,
- Dire Wolf.

Obsługiwane są zarówno porty szeregowe, jak i połączenia sieciowe.

## KISS

Program potrafi bezpośrednio współpracować z urządzeniami KISS.

Dostępne są między innymi tryby:

```text
KISS
Simply KISS
```

`Simply KISS` przeznaczony jest dla urządzeń, które po uruchomieniu są od razu gotowe do pracy w KISS.

Klasyczny tryb KISS pozwala dodatkowo wysyłać do TNC komendy potrzebne do przełączenia go w odpowiedni tryb.

## AGW

APRSIS32 może korzystać również z interfejsu AGW.

Pozwala to współpracować z programowymi modemami, takimi jak:

- UZ7HO SoundModem,
- Dire Wolf.

Przykład:

```text
Radio
  |
karta dźwiękowa
  |
Dire Wolf
  |
AGW
  |
APRSIS32
```

lub:

```text
Radio
  |
karta dźwiękowa
  |
UZ7HO SoundModem
  |
AGW
  |
APRSIS32
```

Takie rozwiązanie pozwala wykorzystać APRSIS32 jako pełny interfejs użytkownika, a dekodowanie AFSK pozostawić osobnemu modemowi programowemu.

## Kenwood TM-D710

Szczególnie rozbudowana jest współpraca z radiami Kenwood wyposażonymi w sprzętową obsługę APRS.

W przypadku TM-D710 APRSISCE/32 może wyświetlać nie tylko informacje widoczne na ekranie samego radia.

Program potrafi zdekodować również typy danych, których D710 sam nie prezentuje, między innymi:

- raporty Direction Finding,
- obiekty MultiLine,
- pełną historię śladów stacji.

Dobrze pokazuje to różnicę pomiędzy samym radiem APRS a rozbudowanym klientem interpretującym pełniejszy zestaw danych protokołu.

## APRS-IS

APRSISCE/32 posiada pełne połączenie z APRS-IS.

Program może jednocześnie odbierać dane:

```text
RF + APRS-IS
```

i składać je w jeden wspólny obraz sytuacji.

Operator może dzięki temu widzieć zarówno stacje odbierane lokalnie drogą radiową, jak i ruch pochodzący z sieci APRS-IS.

## Zaawansowane filtry APRS-IS

Program obsługuje filtry APRS-IS zgodne z serwerami javAPRSSrvr.

Można filtrować między innymi według:

- obszaru,
- promienia,
- znaku,
- prefiksu,
- digipeatera,
- iGate,
- obiektów,
- symbolu,
- typu pakietu,
- wiadomości grupowych.

Przykład:

```text
m/50
```

oznacza ruch w promieniu 50 km od własnej pozycji.

Można również na przykład śledzić konkretny obiekt albo wszystkie stacje odebrane przez określony iGate.

## iGate

APRSIS32 może pracować jako iGate.

Pakiety odebrane lokalnie na RF mogą być przekazywane do APRS-IS.

```text
RF
 |
Radio
 |
APRSIS32
 |
APRS-IS
```

Możliwa jest również transmisja wybranego ruchu z APRS-IS na RF.

Konfiguracja poszczególnych portów określa między innymi:

- RF to IS,
- IS to RF,
- Messages,
- Bulletins/Objects,
- Beacon,
- Telemetry,
- transmit enable.

Dzięki temu dla każdego interfejsu można stosunkowo dokładnie określić jego rolę.

## Digipeater

APRSIS32 posiada również funkcję digipeatera.

Można definiować przekształcenia ścieżek, na przykład:

```text
WIDE1-1=WIDE1*
```

i w ten sposób utworzyć lokalny lub tymczasowy digipeater.

Funkcja ta może być przydatna podczas imprez terenowych i w sytuacjach, gdy potrzebny jest szybko uruchamiany lokalny element infrastruktury.

## Wiadomości APRS

Program posiada rozbudowany interfejs wiadomości APRS.

Obsługiwane są:

- wiadomości tekstowe,
- identyfikatory wiadomości,
- ACK,
- retransmisje,
- rozmowy z wieloma stacjami.

APRSISCE/32 prawidłowo obsługuje również sytuację, w której wiadomość przechodzi trasą:

```text
RF
 |
iGate
 |
APRS-IS
 |
iGate
 |
RF
```

oraz drogę powrotną dla ACK.

## Message-able

Program potrafi określić, które stacje prawdopodobnie obsługują wiadomości APRS.

Funkcja:

```text
View Message-able
```

może wyróżniać takie stacje na podstawie:

- typu pakietów,
- identyfikatora aplikacji,
- wcześniejszej wymiany wiadomości.

Ułatwia to wybór stacji, z którą można rozpocząć komunikację.

## Obiekty

APRSISCE/32 posiada bardzo rozbudowaną obsługę obiektów APRS.

Można tworzyć obiekty reprezentujące między innymi:

- przemienniki,
- częstotliwości,
- miejsca spotkań,
- punkty pomocy,
- zagrożenia,
- stacje techniczne,
- miejsca prowadzenia aktywności.

Obiekt może zawierać:

- pozycję,
- symbol,
- komentarz,
- ścieżkę,
- interwał,
- częstotliwość,
- ton CTCSS,
- informacje potrzebne do QSY.

Program umożliwia grupowanie obiektów i zarządzanie nimi jako zestawami.

## Obiekty częstotliwości

APRSISCE/32 potrafi prawidłowo interpretować Frequency Specification APRS.

Przykładowy komentarz obiektu może wyglądać tak:

```text
145.650MHz T077 -060
```

Klient potrafi z takiego obiektu odczytać:

- częstotliwość,
- ton,
- kierunek offsetu,
- wartość offsetu.

Poprawne formatowanie takich obiektów umożliwia również współpracę z radiami obsługującymi funkcje automatycznego QSY.

## QRU, czyli APRS Info-kiosk

Jedną z wyjątkowo ciekawych funkcji APRSIS32 jest **QRU**.

Pozwala ona przechowywać dużą liczbę obiektów lokalnie i wysyłać je dopiero wtedy, gdy ktoś ich potrzebuje.

Schemat:

```text
stacja mobilna
     |
     | INFO
     v
serwer QRU
     |
     | lista grup
     v
stacja mobilna
```

Następnie użytkownik może poprosić na przykład o:

```text
FUEL
FOOD
HOSP
RP2M
```

i otrzymać obiekty znajdujące się w określonym promieniu od swojej pozycji.

Pozwala to ograniczyć niepotrzebny ruch radiowy.

Zamiast cyklicznie wysyłać setki obiektów, transmitowane są tylko te, o które użytkownik rzeczywiście poprosi.

## MultiLine

APRSISCE/32 obsługuje rozszerzenie **MultiLine**.

Pozwala ono przesyłać w APRS nie tylko punkt, ale również:

- linię,
- trasę,
- granicę,
- wielokąt,
- obszar.

Program potrafi takie dane zarówno odbierać, jak i tworzyć.

Można na przykład przesuwać własny obiekt przez kolejne punkty na mapie, tworząc ślad, a następnie przekształcić go w MultiLine.

Dostępne są również współrzędne skompresowane, pozwalające uzyskać większą dokładność przy ograniczonej długości ramki.

## Obiekty obszarowe

MultiLine pozwala tworzyć obiekty, które nie są tylko punktem.

Przykładowo można oznaczyć:

- teren działań,
- strefę zamkniętą,
- obszar poszukiwań,
- teren imprezy,
- obszar zagrożenia,
- trasę.

Przykład ideowy:

```text
+-------------------+
|                   |
|   STREFA DZIAŁAŃ  |
|                   |
+-------------------+
```

APRSIS32 pozwala narysować taki obszar bezpośrednio na mapie, a następnie zakodować go w komentarzu obiektu APRS.

Dzięki temu ten sam obszar może zostać przesłany drogą radiową do innych użytkowników zgodnych klientów APRS.

## NWS-WARN

Jedną z najbardziej zaawansowanych funkcji APRSISCE/32 jest obsługa ostrzeżeń **National Weather Service**.

NWS-WARN wykorzystuje APRS do dystrybucji informacji o ostrzeżeniach pogodowych i obszarach, których dotyczą.

APRSISCE/32 potrafi:

- odbierać obiekty NWS,
- identyfikować obszar ostrzeżenia,
- wyświetlać ostrzeżenia na mapie,
- korzystać z lokalnych plików shapefile,
- dopasowywać komunikat do rzeczywistych granic administracyjnych lub meteorologicznych.

Program wykorzystuje zestawy:

```text
SHP
SHX
DBF
```

i na ich podstawie może rysować dokładne granice alertu na mapie.

Możliwe jest skonfigurowanie konkretnych biur NWS albo odbieranie szerszego zestawu alertów.

## Dlaczego NWS-WARN jest ważny?

Ta funkcja dobrze pokazuje, czym APRS może być poza śledzeniem pozycji.

Ramka radiowa może nieść identyfikator ostrzeżenia, natomiast klient posiadający lokalną bazę granic może zamienić niewielką ilość danych w pełną wizualizację:

```text
krótka informacja APRS
        |
        v
identyfikator obszaru
        |
        v
lokalny shapefile
        |
        v
obszar ostrzeżenia na mapie
```

To bardzo efektywny sposób przesyłania informacji obszarowej przy niewielkiej przepustowości kanału APRS.

## Direction Finding

APRS posiada także formaty przeznaczone do przekazywania informacji wykorzystywanych podczas radionamierzania.

APRSISCE/32 potrafi interpretować i wizualizować pakiety **Direction Finding**.

Program posiada między innymi możliwość wyświetlania:

```text
DF circles
```

na mapie.

W praktyce pozwala to nanosić wyniki pomiarów pochodzących z różnych punktów i wykorzystywać je do określenia prawdopodobnego położenia źródła sygnału.

## Triangulacja

Po wykonaniu pomiarów z kilku lokalizacji można zobaczyć ich przecięcia na jednej mapie.

Może to być użyteczne podczas:

- poszukiwania źródła zakłóceń,
- fox hunting,
- lokalizowania nadajnika,
- ćwiczeń terenowych.

## Pogoda

APRSISCE/32 obsługuje stacje pogodowe APRS.

Potrafi również samodzielnie generować pakiety pogodowe.

Jeżeli zewnętrzne oprogramowanie aktualizuje plik:

```text
wxnow.txt
```

APRSISCE/32 może:

1. odczytać jego zawartość,
2. utworzyć poprawną ramkę APRS Weather,
3. przesłać ją przez APRS-IS,
4. przesłać ją na RF,
5. albo użyć obu dróg jednocześnie.

## Telemetria

Program obsługuje standardową telemetrię APRS.

Może odbierać i prezentować parametry telemetryczne pochodzące ze stacji infrastrukturalnych, pogodowych i eksperymentalnych.

Poszczególne porty RF mogą również niezależnie decydować, czy telemetria ma być transmitowana przez dany interfejs.

## APRS Queries

APRSISCE/32 obsługuje również standardowe zapytania APRS.

Między innymi:

```text
?APRS?
?IGATE?
?WX?
```

Nie są to zwykłe wiadomości tekstowe, lecz osobny typ pakietu APRS.

Klient może odpowiedzieć odpowiednio:

- własną pozycją,
- informacją o możliwościach iGate,
- aktualnymi danymi pogodowymi.

## Item-In-Message

Program posiada również obsługę mechanizmu:

```text
Item-In-Message
```

pozwalającego przesłać niewielki item w wiadomości APRS.

Wspierane są także skompresowane współrzędne, co pozwala ograniczyć długość danych.

## Satelity

APRSISCE/32 posiada funkcje związane ze śledzeniem satelitów.

Może korzystać z danych TLE i wyliczać pozycję satelity.

Dla odpowiednich obiektów może również wyświetlać przewidywany obszar słyszalności jako obiekt MultiLine.

Dzięki temu użytkownik może zobaczyć, czy znajduje się w obszarze potencjalnego odbioru.

## Duplikaty ramek

APRSISCE/32 posiada mechanizmy rozpoznawania duplikatów.

Ma to szczególne znaczenie przy śledzeniu stacji mobilnych.

Jeżeli ta sama pozycja dociera różnymi drogami i w różnym czasie, klient potrafi rozpoznać takie punkty i nie uwzględniać ich w prawidłowej linii ruchu.

## Beaconing

Program może generować własne pozycje.

Źródłem pozycji może być między innymi:

- stała konfiguracja,
- GPS,
- NMEA z portu radiowego,
- odbiornik GPS przez USB,
- GPS Bluetooth.

Dla każdego portu można określić, czy własny beacon ma być przez niego wysyłany.

## GPS

APRSISCE/32 obsługuje standardowe dane:

```text
NMEA
```

z zewnętrznych odbiorników GPS.

Może to być:

- port szeregowy,
- USB,
- Bluetooth,
- passthrough z kompatybilnego radia.

## Windows, Linux i Wine

APRSIS32 jest aplikacją Windows.

Aktualna dokumentacja opisuje jednak również uruchamianie jej pod Linuxem za pomocą:

```text
Wine
```

Możliwe jest również uruchomienie w maszynie wirtualnej z Windows.

## Aktualny stan projektu

APRSISCE/32 ma długą historię i większość najbardziej rozbudowanych funkcji powstała wiele lat temu.

Jednocześnie projekt nie jest całkowicie martwy.

Strona pobierania została zaktualizowana w 2025 roku i udostępnia jako aktualną wersję wcześniejszy build developerski APRSIS32.

Według strony projektu działa on na współczesnych wersjach Windows, w tym Windows 10 i Windows 11.

Wersje dla Windows Mobile i Windows CE pozostają przede wszystkim częścią historii projektu.

## Zgodność z protokołem APRS

Jedną z największych zalet APRSISCE/32 jest szeroki zakres obsługiwanych elementów APRS.

Program potrafi interpretować między innymi:

- pozycje,
- skompresowane pozycje,
- symbole i overlay,
- komentarze,
- wiadomości,
- ACK,
- statusy,
- telemetrię,
- pogodę,
- obiekty,
- itemy,
- częstotliwości,
- APRS Queries,
- NWS weather objects,
- MultiLine,
- obiekty obszarowe,
- dane Direction Finding,
- Item-In-Message.

Dzięki temu APRSISCE/32 bardzo dobrze pokazuje, że APRS jest znacznie szerszym protokołem niż tylko system raportowania pozycji.

## Dla kogo jest APRSISCE/32?

Program może być interesujący dla operatora, który chce:

- obserwować lokalny ruch APRS,
- korzystać z mapy,
- podłączyć własne radio,
- prowadzić korespondencję APRS,
- uruchomić iGate,
- tworzyć obiekty,
- tworzyć obszary i trasy,
- eksperymentować z Direction Finding,
- analizować pogodę i alerty,
- pracować z wieloma portami RF,
- poznać bardziej zaawansowane elementy protokołu APRS.

## Podsumowanie

APRSISCE/32 należy do najbardziej kompletnych klasycznych klientów APRS.

Łączy w jednym programie funkcje operatorskie, mapowe i infrastrukturalne.

Program obsługuje zarówno typowe elementy APRS, jak i funkcje spotykane znacznie rzadziej:

- obiekty obszarowe,
- MultiLine,
- Direction Finding,
- QRU,
- APRS Queries,
- NWS-WARN,
- shapefile,
- telemetrię,
- pogodę,
- satelity.

To sprawia, że APRSISCE/32 jest nie tylko użytecznym klientem, ale także bardzo dobrym narzędziem pokazującym, jak rozbudowany jest sam protokół APRS.

## Dokumentacja

Najważniejsze źródła:

- strona projektu: https://aprsisce.wikidot.com/
- pobieranie: https://aprsisce.wikidot.com/downloads
- dokumentacja funkcji: https://aprsisce.wikidot.com/en-contexthelp
- sprzęt i oprogramowanie: https://aprsisce.wikidot.com/doc:compatible-hardware-software
- filtry APRS-IS: https://aprsisce.wikidot.com/aprs-is-filters
- NWS: https://aprsisce.wikidot.com/en-nws
- grupa wsparcia: https://groups.io/g/APRSISCE

Praktyczne materiały w języku polskim:

- wyszukiwanie materiałów APRSIS32: https://hamspirit.pl/SQ9MDD/?s=aprsis
- pierwsze kroki i radio: https://hamspirit.pl/SQ9MDD/?p=1239
- tymczasowy digipeater: https://hamspirit.pl/SQ9MDD/?p=1202
- obiekty: https://hamspirit.pl/SQ9MDD/?p=1140
- obiekty obszarowe: https://hamspirit.pl/SQ9MDD/?p=1170
- QRU Info-kiosk: https://hamspirit.pl/SQ9MDD/?p=1384
- Direction Finding i triangulacja: https://hamspirit.pl/SQ9MDD/?p=1090
