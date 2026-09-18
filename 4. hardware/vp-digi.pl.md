---
title: VP-Digi
description: Nowoczesny, energooszczędny sterownik digipeatera APRS i modem KISS z obsługą AX.25, FX.25 i wielu trybów modulacji.
template: doc
tableOfContents: true
---

VP-Digi to samodzielny sterownik digipeatera AX.25 oraz modem TNC KISS przeznaczony między innymi do APRS.

Projekt powstał jako niewielkie, niedrogie i energooszczędne urządzenie oparte na mikrokontrolerze STM32. Łączy funkcje sprzętowego TNC, modemu Packet Radio i rozbudowanego digipeatera APRS.

Oficjalne repozytorium projektu:

https://github.com/sq8vps/vp-digi

Dokumentacja:

https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md

VP-Digi jest projektem open source i nadal jest rozwijany. Kolejne wersje przynoszą poprawki, nowe funkcje modemów, rozwój FX.25 i usprawnienia obsługi AX.25 oraz KISS.

## Po co VP-Digi?

VP-Digi jest ciekawą alternatywą dla klasycznych TNC i dużych komputerów używanych do obsługi stacji APRS.

Typowa instalacja może wyglądać tak:

```text
Radio
  |
audio + PTT
  |
VP-Digi
  |
KISS / USB / UART
  |
APRSBox / komputer / inna aplikacja
```

Urządzenie może również pracować całkowicie samodzielnie jako digipeater:

```text
Radio
  |
audio + PTT
  |
VP-Digi
```

Nie wymaga wtedy Raspberry Pi, komputera PC ani systemu operacyjnego.

## Nowoczesna konstrukcja

VP-Digi zostało zbudowane wokół mikrokontrolera **STM32F103**.

Taka architektura pozwala realizować cyfrowo między innymi:

- modulację i demodulację,
- wykrywanie zajętości kanału,
- kodowanie i dekodowanie AX.25,
- obsługę FX.25,
- filtrowanie duplikatów,
- funkcje digipeatera,
- KISS TNC,
- generowanie beaconów.

Nie jest to więc prosty tracker ani generator ramek, lecz pełny kontroler warstwy radiowej Packet Radio i APRS.

## Zgodność z AX.25 i APRS

Jedną z najważniejszych cech VP-Digi jest poprawna obsługa **AX.25**, czyli protokołu będącego podstawą klasycznego Packet Radio i APRS.

Urządzenie może pracować jako:

- modem AX.25,
- TNC KISS,
- digipeater APRS,
- generator beaconów,
- modem dla Packet Radio,
- warstwa radiowa dla zewnętrznego oprogramowania.

Dzięki interfejsowi KISS może współpracować z wieloma aplikacjami bez stosowania własnościowego protokołu producenta.

Przykład:

```text
VP-Digi
   |
KISS
   |
APRSBox
```

albo:

```text
VP-Digi
   |
KISS
   |
oprogramowanie Packet Radio
```

## Obsługiwane modemy

VP-Digi obsługuje kilka różnych modemów.

### 1200 Bd Bell 202

Klasyczny modem APRS na VHF:

```text
AFSK 1200 Bd
1200 / 2200 Hz
```

To podstawowy tryb używany przez klasyczny APRS na paśmie 2 m.

### 300 Bd Bell 103

Tryb przeznaczony głównie do pracy na HF:

```text
AFSK 300 Bd
1600 / 1800 Hz
```

### 9600 Bd G3RUH

VP-Digi obsługuje również:

```text
GFSK 9600 Bd
G3RUH
```

Ten tryb wymaga odpowiedniego toru radiowego i zwykle dostępu do sygnału typu flat audio.

Nie każde radio nadaje się do pracy 9600 Bd przez standardowe wejście mikrofonowe i wyjście głośnikowe.

### 1200 Bd V.23

Dostępny jest także alternatywny modem:

```text
AFSK 1200 Bd
1300 / 2100 Hz
```

zgodny z V.23.

## FX.25

VP-Digi obsługuje także **FX.25**.

FX.25 jest rozszerzeniem AX.25 o korekcję błędów FEC opartą na kodach Reed-Solomona. Może zwiększyć prawdopodobieństwo poprawnego odbioru ramek w trudniejszych warunkach radiowych, pozostając kompatybilnym z klasycznym AX.25.

VP-Digi może:

- odbierać AX.25,
- odbierać FX.25,
- nadawać AX.25,
- nadawać FX.25.

Obsługę FX.25 można konfigurować niezależnie dla odbioru i nadawania.

## Wykrywanie zajętości kanału

VP-Digi nie opiera DCD wyłącznie na pojawieniu się poprawnie zdekodowanych danych.

Wykrywanie zajętości kanału bazuje na obecności prawidłowego sygnału modulującego.

Pozwala to wcześniej rozpoznać, że kanał jest zajęty i ograniczyć liczbę kolizji.

W praktyce radio współpracujące z VP-Digi powinno pracować z otwartą blokadą szumów.

## KISS TNC

Jedną z najważniejszych funkcji VP-Digi jest tryb **KISS**.

W tym układzie VP-Digi zajmuje się warstwą radiową:

```text
AX.25
modulacja
demodulacja
PTT
DCD
```

a zewnętrzna aplikacja obsługuje logikę wyższego poziomu.

Może to być na przykład:

- APRSBox,
- klient Packet Radio,
- aplikacja Winlink,
- własne oprogramowanie,
- narzędzia diagnostyczne.

Dzięki temu VP-Digi nie jest ograniczone wyłącznie do roli digipeatera.

## USB i dwa porty UART

Urządzenie oferuje:

- USB,
- UART1,
- UART2.

Każdy z portów może pracować niezależnie w trybie:

- KISS,
- monitora ramek,
- terminala konfiguracyjnego.

Pozwala to zbudować elastyczną instalację, na przykład jeden port przeznaczyć na KISS dla aplikacji APRS, a drugi na monitoring i diagnostykę.

## Digipeater APRS

VP-Digi posiada rozbudowany mechanizm digipeatera.

Można skonfigurować między innymi:

- 4 aliasy typu New-N, na przykład `WIDEn-N`,
- 4 aliasy proste,
- maksymalną liczbę przeskoków,
- tryb trace,
- tryb direct-only,
- viscous delay,
- filtrowanie ramek,
- bufor duplikatów.

To daje znacznie większą kontrolę niż w prostych sterownikach retransmisji.

## Viscous delay

VP-Digi obsługuje mechanizm **viscous delay**.

Zamiast retransmitować ramkę natychmiast, digipeater może chwilę odczekać i sprawdzić, czy pakiet nie został już powtórzony przez inną stację.

Jeżeli tak się stanie, własna retransmisja może zostać pominięta.

Pomaga to ograniczyć:

- niepotrzebne duplikaty,
- zajętość kanału,
- liczbę retransmisji.

Jest to szczególnie przydatne w obszarach z kilkoma nakładającymi się digipeaterami.

## Direct-only

Dla poszczególnych aliasów można również włączyć tryb **direct-only**.

W takim przypadku digipeater retransmituje tylko pakiety odebrane bezpośrednio od stacji źródłowej.

Pozwala to ograniczyć ponowne powtarzanie ramek, które przeszły już przez inne digipeatery.

## Filtrowanie stacji

VP-Digi umożliwia filtrowanie ramek według znaku wywoławczego.

Dostępne są dwa tryby:

- blacklist,
- whitelist.

Możliwe jest więc blokowanie wybranych stacji albo dopuszczenie tylko określonych znaków.

## Ochrona przed duplikatami

Digipeater posiada bufor wykrywający powtarzające się pakiety.

Zapobiega to wielokrotnej retransmisji tej samej ramki odebranej różnymi drogami.

Czas bufora można konfigurować.

## Beacony

VP-Digi umożliwia skonfigurowanie do **8 niezależnych beaconów**.

Dla każdego można ustawić między innymi:

- treść,
- interwał,
- opóźnienie,
- ścieżkę APRS,
- włączenie lub wyłączenie.

Przykładowy beacon:

```text
!5002.63N/02157.91E#VP-Digi
```

## Monitor ramek

VP-Digi może pracować jako monitor ruchu AX.25.

Na porcie szeregowym można obserwować odebrane pakiety, co jest przydatne podczas:

- uruchamiania stacji,
- kontroli konfiguracji,
- diagnostyki toru audio,
- analizy ruchu Packet Radio i APRS.

## Niskie zużycie energii

Jedną z największych zalet VP-Digi jest bardzo mały pobór prądu.

Według dokumentacji projektowej urządzenie pobiera około:

```text
40-50 mA
```

Ma to szczególne znaczenie w instalacjach:

- zasilanych z akumulatora,
- zasilanych solarnie,
- znajdujących się na obiektach bez stałego zasilania,
- pracujących jako autonomiczne digipeatery,
- działających przez całą dobę.

W takich zastosowaniach większą część energii zwykle zużywa radiotelefon, a nie sam sterownik.

## Otwarty i rozwijany projekt

VP-Digi jest projektem open source.

Repozytorium udostępnia:

- kod źródłowy,
- schemat,
- dokumentację,
- gotowe firmware,
- historię zmian.

Projekt jest rozwijany, a kolejne wersje wprowadzają poprawki i nowe funkcje.

To ważna cecha w przypadku urządzenia infrastrukturalnego, które ma pracować długo i zgodnie z protokołem.

## Dla kogo jest VP-Digi?

VP-Digi dobrze sprawdzi się, gdy potrzebny jest:

- autonomiczny digipeater APRS,
- energooszczędny węzeł pracujący 24/7,
- sprzętowy modem KISS,
- modem Packet Radio,
- interfejs radiowy dla APRSBox lub innego oprogramowania,
- urządzenie do eksperymentów z AX.25 i FX.25,
- niewielka stacja terenowa lub solarna.

## VP-Digi a modem programowy

VP-Digi i rozwiązania takie jak Dire Wolf reprezentują dwa różne podejścia.

### VP-Digi

```text
mikrokontroler
mały pobór energii
brak systemu operacyjnego
samodzielny digipeater
KISS
```

### Modem programowy

```text
komputer
karta dźwiękowa
większa moc obliczeniowa
większa elastyczność DSP
łatwiejsza integracja z usługami sieciowymi
```

W instalacji autonomicznej VP-Digi może być prostsze i bardziej energooszczędne.

W bardziej rozbudowanej stacji może natomiast pracować jako modem KISS dla zewnętrznego oprogramowania.

## Podsumowanie

VP-Digi jest dobrym przykładem współczesnego sprzętu APRS.

Łączy:

- zgodność z AX.25,
- obsługę FX.25,
- kilka rodzajów modemów,
- rozbudowany digipeater,
- KISS,
- USB i UART,
- bardzo mały pobór energii,
- otwarty kod źródłowy,
- aktywnie rozwijane oprogramowanie.

Dzięki temu może pracować zarówno jako autonomiczny digipeater, jak i jako modem dla większego systemu APRS.

To rozwiązanie szczególnie interesujące tam, gdzie liczą się **zgodność z protokołem, niezawodność, małe zużycie energii i możliwość pracy bez pełnego komputera**.

## Dokumentacja

Najważniejsze źródła:

- repozytorium projektu: https://github.com/sq8vps/vp-digi
- polska dokumentacja: https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md
- opis projektu SQ8L: https://sq8l.pzk.pl/index.php/vp-digi-tani-i-funkcjonalny-sterownik-digipeatera-aprs-wraz-z-modemem-kiss/
- rejestr zmian: https://github.com/sq8vps/vp-digi/blob/main/CHANGELOG.md
