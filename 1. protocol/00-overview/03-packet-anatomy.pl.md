---
title: Anatomia pakietu APRS
description: Szczegółowe omówienie tekstowej reprezentacji pakietu APRS, pól AX.25, ścieżki oraz danych APRS.
template: doc
tableOfContents: true
---

Pakiet APRS jest bardzo często przedstawiany w czytelnej postaci tekstowej:

```text
SOURCE>DEST,PATH:DATA
```

Przykład:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Na pierwszy rzut oka wygląda to jak jeden format.

W rzeczywistości zapis ten łączy w jednym wierszu informacje pochodzące z kilku elementów ramki AX.25 oraz właściwe dane APRS.

W uproszczeniu:

```text
SQ9MDD-7 > APRS , WIDE1-1 : !5012.34N/01956.78E>
^^^^^^^^   ^^^^   ^^^^^^^   ^^^^^^^^^^^^^^^^^^^^^
 SOURCE    DEST     PATH           DATA
  AX.25    AX.25    AX.25          APRS
```

Zrozumienie tego podziału jest podstawą dalszej analizy protokołu.

## Ogólna postać pakietu

Najczęściej spotykana tekstowa reprezentacja pakietu APRS ma postać:

```text
SOURCE>DEST,PATH:INFORMATION
```

Nie wszystkie elementy muszą być zawsze obecne.

Pakiet bez ścieżki może wyglądać tak:

```text
SOURCE>DEST:INFORMATION
```

Przykład:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

Natomiast pakiet ze ścieżką:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:INFORMATION
```

Przykład:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:>QRV 145.550 MHz
```

Logicznie można go więc podzielić na:

```text
SOURCE
DEST
PATH
INFORMATION
```

Pierwsze trzy elementy związane są z adresowaniem AX.25.

Pole `INFORMATION` zawiera właściwą informację APRS.

## Przykład krok po kroku

Rozważmy pakiet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Możemy rozłożyć go na następujące części:

```text
SQ9MDD-7
```

źródło pakietu,

```text
>
```

separator używany w reprezentacji tekstowej,

```text
APRS
```

destination address,

```text
,
```

separator przed ścieżką,

```text
WIDE1-1
```

ścieżka digipeatera,

```text
:
```

separator pomiędzy nagłówkiem i polem informacji,

```text
!
```

Data Type Identifier,

```text
5012.34N/01956.78E>
```

właściwe dane APRS.

W jednym wierszu widzimy więc jednocześnie informacje z warstwy AX.25 i dane protokołu APRS.

## SOURCE

Pierwszym elementem jest adres źródłowy:

```text
SOURCE
```

Przykład:

```text
SQ9MDD-7
```

W typowym APRS jest to znak wywoławczy stacji wraz z opcjonalnym SSID.

Może więc występować na przykład jako:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

SOURCE identyfikuje stację, która utworzyła ramkę AX.25.

Jest to element adresowania AX.25, a nie część pola informacji APRS.

## Znak wywoławczy i SSID

Adres AX.25 może zawierać znak oraz SSID.

Przykład:

```text
SQ9MDD-7
```

można logicznie odczytać jako:

```text
callsign: SQ9MDD
SSID:     7
```

SSID pozwala temu samemu znakowi używać kilku logicznych stacji.

Przykładowo operator może posiadać osobne SSID dla:

- stacji bazowej,
- stacji mobilnej,
- radiotelefonu ręcznego,
- digipeatera,
- IGate,
- trackera.

Istnieją historyczne i praktyczne konwencje dotyczące znaczenia poszczególnych SSID, ale nie należy traktować ich jako bezwzględnej części protokołu określającej funkcję urządzenia.

Szczegóły dotyczące znaków i SSID są opisane osobno.

## Separator `>`

Po adresie źródłowym w reprezentacji tekstowej pojawia się znak:

```text
>
```

Na przykład:

```text
SQ9MDD-7>APRS
```

Oddziela on SOURCE od DEST.

Jest to element tekstowej reprezentacji pakietu.

Nie należy traktować znaku `>` jako osobnego bajtu znajdującego się w tym miejscu surowej ramki AX.25 transmitowanej przez radio.

## DEST

Po znaku `>` znajduje się destination address.

Przykład:

```text
APRS
```

Całość:

```text
SQ9MDD-7>APRS
```

W AX.25 jest to adres destination.

W klasycznym packet radio może on oznaczać stację docelową.

W APRS jego znaczenie jest często inne.

## Destination w APRS

W wielu pakietach APRS pole destination nie wskazuje rzeczywistego odbiorcy informacji.

Może być wykorzystane między innymi do identyfikacji:

- rodzaju urządzenia,
- producenta,
- oprogramowania,
- rodziny aplikacji,
- sposobu wygenerowania pakietu.

W tym celu APRS wykorzystuje między innymi system **TOCALL**.

Przykładowe wartości mogą wyglądać jak:

```text
APRS
APDWxx
APRSxx
```

Ich dokładne znaczenie zależy od przydziału TOCALL.

Dlatego pakiet:

```text
SQ9MDD-7>APRS:...
```

nie oznacza:

```text
SQ9MDD-7 wysyła dane do stacji APRS
```

Pole destination jest tutaj przede wszystkim częścią nagłówka AX.25 wykorzystywaną przez APRS również do dodatkowej identyfikacji.

## Destination nie jest adresatem wiadomości APRS

Jest to jedna z najważniejszych rzeczy do zapamiętania.

Rozważmy:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Czesc
```

AX.25 destination wynosi:

```text
APRS
```

ale adresatem wiadomości jest:

```text
SP9XYZ
```

Adresat znajduje się wewnątrz danych APRS.

Mamy więc dwa różne pojęcia:

```text
AX.25 destination
```

oraz:

```text
APRS message addressee
```

Nie należy ich ze sobą utożsamiać.

## PATH

Po destination może pojawić się ścieżka:

```text
SOURCE>DEST,PATH:DATA
```

Przykład:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

Ścieżka może zawierać jeden lub więcej elementów:

```text
WIDE1-1
WIDE2-1
```

W reprezentacji tekstowej oddzielane są przecinkami.

## PATH jest elementem AX.25

Ścieżka nie znajduje się wewnątrz payloadu APRS.

Jest reprezentacją listy adresów digipeaterów AX.25.

Logicznie:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:DATA
```

można rozumieć jako:

```text
AX.25:
    SOURCE
    DEST
    DIGI 1
    DIGI 2

APRS:
    DATA
```

Ma to znaczenie podczas analizy pakietów.

`WIDE1-1` nie jest poleceniem zapisanym wewnątrz danych APRS.

Jest częścią adresowania ramki AX.25.

## Wiele elementów ścieżki

Ścieżka może zawierać kilka adresów.

Przykład:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

lub po przejściu przez infrastrukturę:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:...
```

Znak:

```text
*
```

widoczny w reprezentacji monitorowej może wskazywać, że dany element ścieżki został już użyty.

Szczegóły mechanizmu H-bit, aliasów `WIDE`, New-N Paradigm oraz przetwarzania ścieżek są opisane w osobnym artykule.

## Separator `:`

Najważniejszą granicą w tekstowej reprezentacji pakietu jest:

```text
:
```

Przykład:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Wszystko przed pierwszym separatorem `:` tworzy tekstową reprezentację informacji związanych z nagłówkiem.

To, co znajduje się po nim, jest polem informacji.

Możemy więc zapisać:

```text
SOURCE>DEST,PATH : INFORMATION
^^^^^^^^^^^^^^^^   ^^^^^^^^^^^
     header           data
```

Dla analizy APRS to właśnie po prawej stronie tego separatora rozpoczyna się właściwa treść APRS.

## INFORMATION

Pole po separatorze `:` zawiera dane przenoszone w polu Information ramki AX.25.

Przykład:

```text
!5012.34N/01956.78E>
```

To właśnie tutaj znajduje się właściwy protokół danych APRS.

Zawartość może reprezentować między innymi:

- pozycję,
- wiadomość,
- status,
- obiekt,
- item,
- pogodę,
- telemetrię,
- zapytanie,
- możliwości stacji,
- dane specjalne.

Interpretacja pola zależy przede wszystkim od jego początku.

## Data Type Identifier

Pierwszy znak pola APRS Information bardzo często jest **Data Type Identifier**, w skrócie DTI.

Przykład:

```text
!5012.34N/01956.78E>
^
|
DTI
```

DTI określa sposób interpretacji dalszej części danych.

Przykładowe DTI to:

| DTI | Ogólne znaczenie |
|---|---|
| `!` | pozycja bez czasu |
| `=` | pozycja bez czasu, z obsługą messaging |
| `/` | pozycja z czasem |
| `@` | pozycja z czasem, z obsługą messaging |
| `:` | wiadomość |
| `;` | obiekt |
| `)` | item |
| `>` | status |
| `?` | query |
| `_` | dane pogodowe bez pozycji |
| `T` | telemetria |
| `}` | third-party traffic |

Nie jest to pełna tabela wszystkich możliwych wartości.

Pełny przegląd DTI znajduje się w artykule poświęconym typom ramek APRS.

## DTI nie zawsze wystarcza do pełnej interpretacji

Rozpoznanie pierwszego znaku jest dopiero początkiem parsowania pakietu.

Przykład:

```text
!5012.34N/01956.78E>
```

DTI:

```text
!
```

informuje, że mamy do czynienia z pozycją bez znacznika czasu.

Dopiero dalsze znaki określają:

- szerokość geograficzną,
- tabelę symboli,
- długość geograficzną,
- symbol,
- ewentualne rozszerzenia,
- komentarz.

Parser APRS musi więc najpierw rozpoznać typ danych, a następnie zastosować reguły właściwe dla tego typu.

## Przykład pozycji

Pakiet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

możemy rozłożyć następująco:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

```text
!
```

DTI pozycji bez czasu,

```text
5012.34N
```

szerokość geograficzna,

```text
/
```

identyfikator tabeli symboli,

```text
01956.78E
```

długość geograficzna,

```text
>
```

kod symbolu.

W tym przykładzie cały pakiet można więc zobaczyć jako:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E >
SOURCE      DEST    PATH      DTI LATITUDE   LONGITUDE  SYMBOL
```

## Symbol jest częścią formatu pozycji

W pakiecie pozycyjnym APRS symbol nie jest przesyłany jako nazwa:

```text
car
house
repeater
```

Zamiast tego określają go znaki znajdujące się w konkretnych miejscach formatu pozycji.

W klasycznej pozycji nieskompresowanej istotne są:

- znak tabeli symboli,
- kod symbolu.

Dlatego:

```text
/
```

w środku pakietu pozycyjnego nie jest separatorem tekstowym podobnym do `>` czy `:`.

Jest częścią danych APRS i ma znaczenie podczas interpretacji symbolu.

## Przykład wiadomości

Rozważmy:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

Podział wygląda następująco:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

a pole informacji:

```text
:SP9XYZ   :Test
```

zaczyna się od:

```text
:
```

czyli DTI wiadomości.

Następnie występuje pole adresata:

```text
SP9XYZ
```

oraz treść:

```text
Test
```

Warto zauważyć dwa różne dwukropki:

```text
...WIDE1-1::SP9XYZ   :Test
           ^^
```

Pierwszy:

```text
:
```

oddziela nagłówek od Information.

Drugi:

```text
:
```

jest DTI wiadomości APRS.

Nie jest to przypadkowe powtórzenie.

## Przykład statusu

Pakiet:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

zawiera:

```text
>
```

jako pierwszy znak pola informacji.

W tym miejscu `>` jest DTI statusu.

To pokazuje, dlaczego ten sam znak może mieć inne znaczenie zależnie od miejsca, w którym występuje.

W nagłówku:

```text
SOURCE>DEST
```

`>` jest separatorem reprezentacji tekstowej.

W Information:

```text
>QRV 145.550 MHz
```

`>` jest DTI APRS.

Parser zawsze musi uwzględniać kontekst.

## Przykład obiektu

Pakiet obiektu rozpoczyna się w Information od:

```text
;
```

Schematycznie:

```text
SOURCE>DEST,PATH:;OBJECTNAM*...
                 ^
                 |
                DTI
```

Po DTI znajdują się pola specyficzne dla obiektu, między innymi jego nazwa, stan, czas i pozycja.

Szczegółowa składnia obiektów jest opisana w osobnej części dokumentacji.

## Komentarz

Wiele formatów APRS pozwala po danych podstawowych umieścić dodatkowy komentarz.

Przykładowo pakiet pozycji może zawierać:

```text
!5012.34N/01956.78E>QRV 145.550 MHz
```

Pierwsza część określa pozycję i symbol.

Dalsza:

```text
QRV 145.550 MHz
```

jest komentarzem.

Komentarz może również zawierać określone rozszerzenia danych APRS.

Dlatego nie zawsze należy traktować wszystko po pozycji jako zwykły dowolny tekst.

## Rozszerzenia danych

Po podstawowej pozycji APRS mogą występować dodatkowe informacje.

W zależności od formatu i kontekstu mogą one opisywać między innymi:

- kurs i prędkość,
- wysokość,
- PHG,
- zasięg,
- częstotliwość,
- tone,
- offset,
- DAO,
- inne rozszerzenia.

Przykład może więc wyglądać znacznie bardziej rozbudowanie niż prosta pozycja:

```text
SOURCE>DEST,PATH:POSITION + EXTENSION + COMMENT
```

Dokładne reguły są zależne od konkretnego typu danych i opisane w odpowiednich działach dokumentacji.

## Długość pola informacji ma znaczenie

APRS pracuje w środowisku o ograniczonej przepustowości, szczególnie na klasycznym kanale VHF 1200 baud.

Każdy dodatkowy znak zwiększa długość transmisji.

Dlatego dobrze zaprojektowany pakiet powinien przekazywać potrzebną informację bez niepotrzebnego rozbudowywania treści.

Dotyczy to szczególnie:

- komentarzy,
- beaconów pozycyjnych,
- obiektów,
- statusów,
- wiadomości.

Długi pakiet zajmuje kanał dłużej niż krótki pakiet.

## Tekst widoczny w monitorze to TNC2 format

Postać:

```text
SOURCE>DEST,PATH:INFORMATION
```

jest powszechnie określana jako format monitorowy TNC2 lub reprezentacja TNC2.

Jest ona używana między innymi przez:

- terminale,
- oprogramowanie APRS,
- logi,
- serwery APRS-IS,
- narzędzia diagnostyczne.

Dzięki niej ramkę AX.25 można przedstawić w formie łatwej do odczytania przez człowieka.

Nie oznacza to jednak, że dokładnie taki ciąg znaków został przesłany przez radio.

## Co naprawdę znajduje się w ramce AX.25?

Na RF adresy nie są przesyłane jako dosłowny tekst:

```text
SQ9MDD-7>APRS,WIDE1-1
```

Ramka AX.25 posiada własny binarny sposób kodowania adresów i pól sterujących.

Zawiera między innymi:

```text
Destination
Source
Digipeater addresses
Control
PID
Information
FCS
```

W przypadku typowego APRS:

```text
Control = UI
PID     = no layer 3
```

Natomiast pole Information zawiera dane APRS.

Reprezentacja TNC2 ukrywa wiele szczegółów warstwy AX.25, aby pakiet był łatwiejszy do analizy.

## FCS nie jest widoczny w TNC2

Rzeczywista ramka radiowa posiada mechanizm kontroli poprawności, FCS.

Nie zobaczymy go jednak w typowej linii:

```text
SOURCE>DEST,PATH:DATA
```

Odbiornik, TNC albo modem sprawdza poprawność ramki wcześniej.

Jeżeli ramka zostanie zaakceptowana i przekazana do aplikacji, reprezentacja monitorowa zazwyczaj nie zawiera FCS.

Jest to kolejny przykład różnicy pomiędzy rzeczywistą ramką AX.25 i jej tekstową reprezentacją.

## Pakiet odebrany bezpośrednio

Przykład pakietu widzianego bezpośrednio na RF:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Możemy interpretować go jako:

```text
SOURCE: SQ9MDD-7
DEST:   APRS
PATH:   WIDE1-1
DATA:   !5012.34N/01956.78E>
```

To jeden z najprostszych przypadków analizy.

## Pakiet po przejściu przez digipeater

Po retransmisji reprezentacja może zawierać informację o wykorzystanym elemencie ścieżki.

Przykładowo:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:!5012.34N/01956.78E>
```

W monitorze można wtedy zobaczyć, którędy pakiet został powtórzony.

Nie oznacza to jednak, że dane APRS:

```text
!5012.34N/01956.78E>
```

uległy zmianie.

Zmieniła się informacja związana z transportem AX.25.

## Pakiet w APRS-IS

Po przekazaniu przez IGate pakiet może wyglądać na przykład tak:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Widzimy tu elementy:

```text
qAR
SQ9MDD-4
```

które dotyczą APRS-IS.

Nie należy więc analizować całego ciągu pomiędzy destination i `:` jako wyłącznie radiowej ścieżki AX.25.

W APRS-IS reprezentacja pakietu może zawierać również informacje dodane przez infrastrukturę internetową.

## Granica RF i APRS-IS

Porównajmy:

### Pakiet RF

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

### Pakiet widoczny w APRS-IS

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Właściwa informacja APRS:

```text
!5012.34N/01956.78E>
```

pozostaje taka sama.

Zmieniają się informacje opisujące sposób, w jaki pakiet dotarł do danego środowiska.

## Third-party packet

APRS posiada również format pozwalający umieścić cały pakiet wewnątrz innego pakietu.

DTI:

```text
}
```

oznacza third-party traffic.

Schematycznie:

```text
SOURCE>DEST,PATH:}ORIGINAL>DEST,PATH:DATA
                 ^
                 |
          third-party DTI
```

Po znaku `}` znajduje się reprezentacja oryginalnego pakietu.

Mamy więc sytuację, w której wewnątrz pola Information jednej ramki znajduje się kolejna reprezentacja pakietu.

Parser musi rozpoznawać ten przypadek i odpowiednio obsłużyć zagnieżdżoną informację.

## Pakiet może zawierać informacje zagnieżdżone

Third-party traffic pokazuje ważną cechę APRS.

Nie zawsze można przeanalizować pakiet prostym założeniem:

```text
znajdź pierwszy znak
odczytaj resztę jako dane
```

Niektóre typy danych tworzą dodatkową strukturę wewnętrzną.

Dlatego poprawny parser musi rozpoznawać format wynikający z DTI i dopiero wtedy interpretować dalszą zawartość.

## Znaczenie pozycji znaku

Podczas analizy APRS znak ma znaczenie nie tylko przez swoją wartość, ale również przez miejsce występowania.

Przykładowo znak:

```text
:
```

może być:

- separatorem nagłówka i Information w reprezentacji TNC2,
- DTI wiadomości,
- separatorem wewnątrz formatu wiadomości.

Znak:

```text
>
```

może być:

- separatorem SOURCE i DEST,
- DTI statusu,
- kodem symbolu w danych pozycyjnych.

Dlatego APRS nie powinien być analizowany przez wyszukiwanie pojedynczych znaków bez uwzględnienia struktury.

## Kolejność parsowania

Praktyczny parser pakietu może działać logicznie w następującej kolejności.

Najpierw należy oddzielić:

```text
HEADER:INFORMATION
```

Następnie z nagłówka rozpoznać:

```text
SOURCE
DEST
PATH
```

A później z pola Information:

```text
DTI
```

i na podstawie DTI wybrać odpowiedni parser danych.

Schemat:

```text
pakiet
  |
  +-> header
  |     |
  |     +-> source
  |     +-> destination
  |     +-> path
  |
  +-> information
        |
        +-> DTI
              |
              +-> parser pozycji
              +-> parser wiadomości
              +-> parser obiektu
              +-> parser pogody
              +-> ...
```

To znacznie bezpieczniejsze podejście niż próba interpretowania całego pakietu jako jednego ciągu o stałej strukturze.

## Nie każdy pakiet pozycyjny wygląda tak samo

Przykład:

```text
!5012.34N/01956.78E>
```

jest klasyczną pozycją nieskompresowaną.

APRS obsługuje jednak również inne sposoby kodowania pozycji, między innymi:

- pozycję ze znacznikiem czasu,
- pozycję skompresowaną,
- Mic-E,
- rozszerzenia dokładności.

Dlatego parser nie powinien zakładać, że pozycja zawsze zawiera czytelne współrzędne w postaci:

```text
DDMM.mmN
DDDMM.mmE
```

Szczegółowe formaty pozycji są opisane w osobnym dziale.

## Nie każde Information zaczyna się od prostego DTI

Większość typowych formatów można łatwo rozpoznać po pierwszym znaku.

Istnieją jednak formaty historyczne, specjalne oraz konstrukcje wymagające dalszej analizy.

Dlatego pełna implementacja APRS wymaga uwzględnienia nie tylko najbardziej popularnych:

```text
!
=
:
;
>
```

ale całego zestawu formatów zdefiniowanych przez specyfikację i późniejsze rozszerzenia.

## Co widzi użytkownik, a co widzi modem?

Użytkownik może zobaczyć:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Aplikacja APRS widzi logiczne pola:

```text
source
destination
path
information
```

Parser APRS widzi wewnątrz Information:

```text
DTI
payload
```

Warstwa AX.25 operuje rzeczywistymi polami ramki.

Modem operuje strumieniem bitów i sygnałem.

Radio ostatecznie przenosi sygnał RF.

Każda warstwa patrzy więc na ten sam pakiet z innej perspektywy.

## Najczęstsze błędy interpretacyjne

### Wszystko przed `:` jest APRS

Nie.

Znaczna część tej informacji opisuje nagłówek i ścieżkę AX.25.

### `WIDE1-1` jest częścią payloadu APRS

Nie.

To element ścieżki AX.25.

### Destination jest adresatem wiadomości

Nie zawsze.

Adresat wiadomości APRS znajduje się wewnątrz Information.

### Każdy znak `:` ma to samo znaczenie

Nie.

Znaczenie zależy od miejsca występowania.

### Tekstowa postać pakietu jest dokładną zawartością RF

Nie.

Jest reprezentacją monitorową rzeczywistej ramki AX.25.

### Wszystko między destination i `:` pochodzi z RF

Nie zawsze.

W APRS-IS mogą pojawić się elementy dodane przez infrastrukturę, na przykład q-constructs.

### Każda pozycja ma postać DDMM.mmN/DDDMM.mmE

Nie.

APRS posiada kilka formatów pozycji.

## Jak czytać pakiet APRS?

Dla pakietu:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>QRV 145.550
```

warto czytać go etapami.

### 1. Kto nadał?

```text
SQ9MDD-7
```

### 2. Jakie jest destination?

```text
APRS
```

### 3. Jaka jest ścieżka?

```text
WIDE1-1
```

### 4. Gdzie zaczyna się Information?

Po:

```text
:
```

### 5. Jaki jest DTI?

```text
!
```

czyli pozycja bez znacznika czasu.

### 6. Jakie są dane właściwe?

```text
5012.34N/01956.78E>
```

pozycja oraz symbol.

### 7. Czy występują dalsze informacje?

```text
QRV 145.550
```

komentarz.

Takie podejście pozwala analizować nawet znacznie bardziej skomplikowane pakiety krok po kroku.

## Cały pakiet jako warstwy

Ten sam przykład można przedstawić następująco:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E > QRV 145.550
|          |      |          | |          |           | |
|          |      |          | |          |           | +-- komentarz
|          |      |          | |          |           +---- symbol
|          |      |          | |          +---------------- długość
|          |      |          | +--------------------------- szerokość
|          |      |          +----------------------------- DTI
|          |      +---------------------------------------- path
|          +----------------------------------------------- destination
+---------------------------------------------------------- source
```

Na poziomie warstw:

```text
AX.25
+---------------------------------------------------+
| SOURCE | DEST | PATH | INFORMATION               |
+---------------------------------------------------+
                       |
                       v
APRS
+---------------------------------------------------+
| DTI | dane typu pakietu | rozszerzenia / komentarz |
+---------------------------------------------------+
```

To jest najważniejszy model, który warto zapamiętać.

## Najważniejsze do zapamiętania

**Tekstowy pakiet APRS łączy informacje AX.25 i APRS.**

Postać:

```text
SOURCE>DEST,PATH:DATA
```

nie jest jednym płaskim formatem.

**SOURCE, DEST i PATH należą do warstwy adresowania AX.25.**

Nie są właściwym payloadem APRS.

**Pole Information rozpoczyna się po separatorze `:` w reprezentacji TNC2.**

To w nim znajdują się dane APRS.

**Pierwszy znak Information często jest DTI.**

Określa sposób interpretacji dalszej części pakietu.

**Destination nie musi być rzeczywistym adresatem.**

W APRS może identyfikować urządzenie lub oprogramowanie.

**Adresat wiadomości APRS znajduje się w Information.**

Jest czymś innym niż AX.25 destination.

**Ścieżka digipeaterów nie jest częścią danych APRS.**

Należy do AX.25.

**Reprezentacja TNC2 nie jest surową ramką radiową.**

Ukrywa część pól i binarnych szczegółów AX.25.

**Pakiet widoczny w APRS-IS może zawierać informacje, których nie było na RF.**

Przykładem są q-constructs.

**Znaczenie znaku zależy od jego miejsca w strukturze.**

Dlatego pakiet należy analizować warstwowo i zgodnie z formatem wynikającym z DTI.

## Dalej

Po poznaniu anatomii pojedynczego pakietu można dokładniej omówić poszczególne jego elementy.

Kolejne zagadnienia obejmują:

- source callsign,
- SSID,
- destination address,
- TOCALL,
- ścieżki AX.25,
- mechanizm WIDE,
- Data Type Identifier,
- pole Information,
- timestamp,
- symbole,
- rozszerzenia danych,
- formaty pozycji,
- Mic-E,
- wiadomości,
- obiekty,
- telemetrię,
- pogodę,
- third-party traffic,
- q-constructs.

Od tego momentu można już analizować poszczególne typy APRS bez mieszania informacji należących do AX.25, APRS i APRS-IS.
