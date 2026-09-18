---
title: APRX
description: Lekki daemon APRS dla systemów POSIX, łączący funkcje iGate, digipeatera, wielu portów radiowych, telemetrii i routingu APRS-IS.
template: doc
tableOfContents: true
---

APRX to wyspecjalizowane oprogramowanie infrastrukturalne dla APRS, przeznaczone przede wszystkim do pracy jako **iGate, digipeater albo połączenie obu tych funkcji**.

Program działa jako daemon na systemach zgodnych z POSIX, przede wszystkim Linux, BSD i innych systemach uniksowych.

Oficjalne repozytorium:

https://github.com/PhirePhly/aprx/

Strona projektu:

https://thelifeofkenneth.com/aprx/

APRX został napisany w języku C i od początku projektowano go jako lekkie oprogramowanie przeznaczone do ciągłej pracy infrastrukturalnej.

Nie jest modemem dźwiękowym.

Do komunikacji z radiem wykorzystuje zewnętrzny TNC, modem KISS, interfejs AX.25 systemu operacyjnego lub inne obsługiwane źródło ramek.

Typowa instalacja może wyglądać tak:

```text
Radio
  |
TNC / modem KISS
  |
APRX
  |
APRS-IS
```

albo jako digipeater:

```text
Radio
  |
TNC / modem
  |
APRX
  |
Radio
```

W bardziej rozbudowanych instalacjach APRX może jednocześnie obsługiwać wiele odbiorników, nadajników i kanałów.

## Autorzy i historia projektu

Autorem pierwszej i drugiej generacji APRX był **Matti Aarnio OH2MQK**.

Kod rozwijany przez Mattiego pochodzi z okresu:

```text
2007-2014
```

W nagłówkach źródeł APRX opisany jest jako:

```text
2nd generation APRS iGate and digi
```

Od 2014 roku opiekę nad projektem i dalszy rozwój przejął **Kenneth W. Finnegan W6KWF**.

Obecne repozytorium projektu:

https://github.com/PhirePhly/aprx/

zawiera kod obu etapów rozwoju.

APRX jest projektem dojrzałym. Tempo zmian jest obecnie znacznie mniejsze niż w przypadku nowych projektów, ale oprogramowanie nadal znajduje się w dystrybucjach Linuksa i jest wykorzystywane w działających instalacjach APRS.

Pakiet APRX 2.9.1 znajduje się między innymi w Debianie.

## Filozofia APRX

Jednym z podstawowych założeń APRX było stworzenie programu infrastrukturalnego o małych wymaganiach.

Kod źródłowy opisuje projekt jako rozwiązanie mające możliwie niewiele zależności poza standardową biblioteką systemową UNIX.

Dzięki temu APRX dobrze nadaje się do pracy na:

- niewielkich komputerach,
- routerach,
- urządzeniach embedded z Linuxem,
- Raspberry Pi,
- starszych komputerach PC,
- serwerach działających całodobowo.

Program nie wymaga środowiska graficznego.

Typowa praca wygląda po prostu tak:

```text
system
  |
aprx daemon
  |
/etc/aprx.conf
```

## APRX nie jest modemem

To ważne rozróżnienie.

APRX nie dekoduje AFSK z karty dźwiękowej.

Potrzebuje źródła gotowych ramek AX.25.

Może nim być na przykład:

```text
Radio
  |
VP-Digi
  |
KISS
  |
APRX
```

albo:

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
```

lub klasyczny sprzętowy TNC.

APRX odpowiada za:

- routing ramek,
- digipeating,
- iGate,
- APRS-IS,
- filtrowanie,
- beacony,
- telemetrię,
- logikę wielu interfejsów.

Modem odpowiada natomiast za warstwę radiową.

## RX iGate

Jednym z najczęstszych zastosowań APRX jest **receive-only iGate**.

W takim układzie:

```text
RF
 |
Radio
 |
TNC
 |
APRX
 |
APRS-IS
```

ramki odebrane drogą radiową są przekazywane do APRS-IS.

Minimalna konfiguracja wymaga przede wszystkim:

- własnego znaku,
- passcode APRS-IS,
- serwera APRS-IS,
- interfejsu radiowego.

Przykładowy blok APRS-IS:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

APRX może więc realizować prosty iGate bez funkcji nadawania na RF.

## TX iGate

APRX obsługuje również transmisję z APRS-IS na RF.

Pozwala to zbudować pełny, dwukierunkowy iGate.

Schemat:

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

TX-iGate nie powinien jednak retransmitować na radio całego ruchu z APRS-IS.

APRX umożliwia ograniczanie i filtrowanie takiego ruchu.

Źródło `APRSIS` może być zdefiniowane w sekcji digipeatera jako osobne źródło z typem:

```text
relay-type third-party
```

Można na nim zastosować również:

- filtrowanie,
- rate limiting,
- viscous delay.

To pozwala bardziej precyzyjnie kontrolować ruch kierowany z Internetu na kanał radiowy.

## Digipeater

APRX posiada wbudowany rozbudowany digipeater.

Obsługuje między innymi klasyczny mechanizm:

```text
WIDEn-N
```

oraz zwykłe aliasy AX.25.

Jedna sekcja digipeatera posiada jeden nadajnik, ale może przyjmować pakiety z wielu źródeł.

Przykład architektury:

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

Pozwala to budować systemy z:

- kilkoma odbiornikami,
- receiver diversity,
- osobnymi antenami kierunkowymi,
- różnymi kanałami,
- wspólnym nadajnikiem.

## New-N

APRX obsługuje współczesny mechanizm APRS New-N.

Może realizować między innymi ścieżki:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

Konfiguracja pozwala określić maksymalną liczbę żądanych oraz wykonanych przeskoków.

Można również definiować własne klucze aliasów.

## Viscous digipeating

Jedną z bardziej charakterystycznych funkcji APRX jest **viscous digipeating**.

Digipeater nie musi retransmitować pakietu natychmiast. Najpierw czeka przez krótki czas.

Jeżeli w tym czasie usłyszy, że pakiet został już retransmitowany przez inną stację, może anulować własną transmisję.

```text
odbiór pakietu
     |
     v
krótkie oczekiwanie
     |
     +---- pakiet usłyszany ponownie ---> DROP
     |
     +---- brak powtórzenia -----------> TX
```

Pozwala to ograniczyć liczbę duplikatów w obszarach, gdzie zasięgi kilku digipeaterów nakładają się na siebie.

## Wiele odbiorników

APRX został zaprojektowany również z myślą o rozbudowanych węzłach.

Może odbierać ramki jednocześnie z wielu interfejsów.

```text
Radio RX North ---> TNC ---\
                            \
Radio RX South ---> TNC ----> APRX ---> Radio TX
                            /
Radio RX Local ---> TNC ---/
```

W konfiguracji można również grupować interfejsy za pomocą:

```text
igate-group
```

## Wiele kanałów

APRX nie jest ograniczony do jednego kanału radiowego.

Można skonfigurować wiele interfejsów i wiele sekcji digipeatera.

Przykładowo:

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
inne RF     ---> lokalny kanał
```

## Interfejsy KISS

APRX może współpracować z klasycznymi TNC po portach szeregowych.

Obsługiwany jest standardowy:

```text
KISS
```

oraz kilka rozszerzeń:

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

## TNC2 monitor mode

APRX może również odbierać dane w formacie:

```text
TNC2
```

czyli tekstowym formacie monitorowym znanym z klasycznych TNC.

## Linux AX.25

Na systemach Linux APRX potrafi również korzystać bezpośrednio z interfejsów AX.25 jądra systemu.

Konfiguracja może używać:

```text
ax25-device
```

## DPRS

APRX posiada również obsługę **D-PRS**.

Może odbierać dane DPRS i przekształcać je do APRS:

```text
D-PRS -> APRS
```

## APRS-IS

APRX posiada własnego klienta APRS-IS.

Może łączyć się z serwerami Tier2 i obsługiwać wiele definicji serwerów.

Przykładowo:

```text
server rotate.aprs2.net
```

Możliwe jest również używanie filtrów APRS-IS.

```text
filter "m/100"
```

czyli ruch w promieniu 100 km od pozycji stacji.

## Filtrowanie ruchu

APRX posiada rozbudowane mechanizmy filtrowania.

Można filtrować między innymi:

- źródło,
- destination,
- ścieżkę,
- payload,
- obszar geograficzny.

Przykład:

```text
filter -b/CALL
```

## Rate limiting

APRX posiada mechanizmy ograniczania liczby retransmitowanych ramek.

Globalny limit:

```text
ratelimit
```

limit dla poszczególnych znaków źródłowych:

```text
srcratelimit
```

## Beacony

APRX posiada własny generator beaconów.

Może generować między innymi:

- pozycje,
- itemy,
- obiekty,
- surowe ramki APRS,
- dane wczytywane z pliku.

Przykład:

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

APRX celowo rozkłada czas transmisji beaconów i lekko losuje interwały, aby zmniejszyć prawdopodobieństwo kolizji.

## Telemetria

APRX posiada własny system telemetrii infrastruktury.

Monitorowane są między innymi:

```text
RX
DROP
TX
```

oraz liczba pakietów i bajtów.

Dane mogą być publikowane jako telemetria APRS.

## Erlang monitor

Ciekawym elementem APRX jest wbudowany **erlang monitor**.

APRX mierzy rzeczywisty ruch na interfejsach i zbiera statystyki w przedziałach:

```text
1 minuta
10 minut
20 minut
```

Dzięki temu można obserwować obciążenie kanału radiowego.

## Logowanie

APRX może prowadzić osobne logi między innymi dla:

- odebranych ramek RF,
- połączeń APRS-IS,
- DPRS,
- statystyk erlang.

Przykładowe pliki:

```text
aprx-rf.log
aprx.log
erlang.log
```

## Konfiguracja

Głównym plikiem konfiguracji jest zwykle:

```text
/etc/aprx.conf
```

Składnia przypomina strukturę konfiguracji Apache.

Przykład:

```text
mycall SQ9ABC-1

<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>

<interface>
    serial-device /dev/ttyUSB0 9600 8n1 KISS
</interface>
```

Sekcje konfiguracji obejmują między innymi:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

## APRX z Dire Wolf

Bardzo praktycznym połączeniem jest użycie APRX jako logiki infrastruktury, a zewnętrznego modemu jako warstwy radiowej.

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
  |
APRS-IS
```

Dire Wolf zajmuje się demodulacją i modulacją AFSK.

APRX odpowiada za:

- digipeating,
- iGate,
- filtry,
- wiele źródeł,
- telemetrię,
- routing.

## APRX ze sprzętowym TNC

Analogicznie można wykorzystać sprzętowe urządzenie KISS:

```text
Radio
  |
VP-Digi / TNC-X / inne TNC
  |
KISS
  |
APRX
```

## Niewielkie wymagania

APRX napisany jest w C i nie posiada interfejsu graficznego.

Dobrze nadaje się do pracy:

- na Raspberry Pi,
- na małych komputerach x86,
- na starszym sprzęcie,
- na urządzeniach embedded z Linuxem,
- w maszynach wirtualnych.

## Stabilność i dojrzałość

APRX jest projektem o długiej historii.

Obecna linia 2.9.1 znajduje się nadal w pakietach Debiana.

Projekt należy obecnie traktować przede wszystkim jako **dojrzałe oprogramowanie infrastrukturalne**, a nie szybko rozwijającą się aplikację.

## Czego APRX nie robi?

APRX nie jest:

- modemem dźwiękowym,
- graficznym klientem APRS,
- mapą APRS,
- programem do śledzenia stacji,
- aplikacją operatorską do codziennej obsługi wiadomości.

Jego miejsce jest przede wszystkim w infrastrukturze.

```text
radio
  |
modem / TNC
  |
APRX
  |
logika infrastruktury APRS
  |
APRS-IS / RF
```

## Dla kogo jest APRX?

APRX dobrze sprawdzi się, gdy potrzebny jest:

- RX iGate,
- TX iGate,
- digipeater,
- połączony digi + iGate,
- węzeł z wieloma odbiornikami,
- instalacja z kilkoma interfejsami radiowymi,
- routing pomiędzy portami,
- filtrowanie ruchu,
- telemetria infrastruktury,
- bardzo lekki daemon działający 24/7.

## Licencja

APRX jest oprogramowaniem open source.

Repozytorium projektu udostępniane jest na licencji:

```text
BSD 3-Clause
```

## Podsumowanie

APRX jest jednym z klasycznych programów infrastrukturalnych świata APRS.

Jego największą siłą jest możliwość zbudowania z jednego procesu całego węzła:

```text
wiele odbiorników
      |
      v
    APRX
   /    \
 digi   iGate
   \    /
    RF / APRS-IS
```

Program łączy:

- RX iGate,
- TX iGate,
- New-N digipeating,
- viscous digipeating,
- wiele interfejsów,
- KISS i Linux AX.25,
- filtrowanie,
- rate limiting,
- beacony,
- telemetrię,
- monitoring obciążenia kanału,
- DPRS,
- APRS-IS.

Do tego pozostaje niewielkim daemonem napisanym w C, który nie wymaga środowiska graficznego ani rozbudowanego systemu.

## Dokumentacja

Najważniejsze źródła:

- repozytorium: https://github.com/PhirePhly/aprx/
- strona projektu: https://thelifeofkenneth.com/aprx/
- przykładowa konfiguracja: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- rozbudowana konfiguracja: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
