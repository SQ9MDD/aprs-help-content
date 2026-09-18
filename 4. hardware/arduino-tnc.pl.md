---
title: Arduino TNC i Arduino TNC Plus
description: Otwarty modem KISS TNC dla APRS rozwijany od projektu KI4MCW, przez eksperymenty M1GEO, po wersję Arduino TNC Plus rozwijaną przez SQ9MDD i SQ5RWU.
template: doc
tableOfContents: true
---

Arduino TNC to otwarty projekt sprzętowego **KISS TNC dla AFSK 1200**, którego historia obejmuje kilka kolejnych etapów rozwoju i prac wielu krótkofalowców.

Projekt rozpoczął Robert Marshall **KI4MCW**, który stworzył programowy modem AFSK działający bez dedykowanego układu modemowego.

Sygnał audio był próbkowany przez przetwornik ADC mikrokontrolera AVR, a demodulacja oraz dekodowanie AX.25 realizowane były programowo.

Pierwotny projekt KI4MCW:

https://sites.google.com/site/ki4mcw/Home/arduino-tnc

Eksperymenty i rozwój projektu przez George'a Smarta M1GEO:

https://www.george-smart.co.uk/arduino/arduino_tnc/

Polska wersja konstrukcji:

https://hamspirit.pl/SQ9MDD/?p=337

Arduino TNC Plus:

https://hamspirit.pl/SQ9MDD/?p=541

Kod rozwijany przez SQ9MDD i SQ5RWU:

https://github.com/SQ9MDD/arduino_tnc

## Idea projektu

Założenie było proste: wykorzystać popularny mikrokontroler AVR jako modem AFSK bez stosowania specjalizowanego układu modemowego.

Schemat funkcjonalny:

```text
Radio
  |
audio + PTT
  |
Arduino
  |
AFSK / AX.25
  |
KISS
  |
komputer / aplikacja APRS
```

Arduino realizuje:

- próbkowanie audio,
- demodulację AFSK,
- dekodowanie AX.25,
- generowanie sygnału nadawczego,
- sterowanie PTT,
- komunikację KISS.

Zewnętrzna aplikacja realizuje natomiast wyższe funkcje APRS lub Packet Radio.

## Początki: KI4MCW

Robert Marshall KI4MCW rozpoczął prace nad Arduino TNC około 2010 roku.

Projekt był eksperymentalnym programowym modemem APRS przeznaczonym głównie dla platform Arduino opartych na AVR.

Jednym z najważniejszych założeń było wykonywanie możliwie dużej części pracy bezpośrednio w kodzie mikrokontrolera.

Zamiast stosować układ taki jak TCM3105 lub MX614, wejście audio trafiało bezpośrednio do ADC procesora.

```text
Radio
  |
audio
  |
ADC
  |
algorytm demodulacji
  |
AX.25
```

W kolejnych wersjach pojawiły się między innymi:

- odbiór AFSK 1200,
- KISS,
- DCD,
- automatyczna korekcja biasu ADC,
- transmisja pakietów,
- obsługa UART.

Kod był intensywnie optymalizowany pod kątem ograniczonych zasobów AVR.

## Demodulacja programowa

Najciekawszym elementem oryginalnego projektu była rezygnacja z dedykowanego modemu analogowego.

Arduino próbkowało sygnał audio i programowo rozpoznawało tony:

```text
1200 Hz
2200 Hz
```

czyli klasyczne AFSK Bell 202 używane w APRS.

Takie podejście pozwalało zbudować kompletny modem przy bardzo niewielkiej liczbie elementów.

Jednocześnie wymagało dokładnego dopracowania kodu, ponieważ ATmega328P dysponuje ograniczoną mocą obliczeniową.

## Eksperymenty M1GEO

George Smart M1GEO szczegółowo testował projekt KI4MCW i eksperymentował z własnym torem wejściowym.

Prace obejmowały między innymi:

- filtrowanie wejścia audio,
- stabilizację napięcia biasującego ADC,
- ograniczenie zakłóceń pochodzących z USB,
- analizę częstotliwości próbkowania,
- testy różnych wersji kodu KI4MCW.

M1GEO używał wersji 0.14, uzyskując dobrą pracę jako KISS TNC.

Eksperymentował również z przekazywaniem danych przez Bluetooth do telefonu oraz przygotował własną wersję płytki.

Pokazuje to jedną z ważnych cech Arduino TNC: projekt był na tyle otwarty i prosty, że łatwo było go adaptować do własnych zastosowań.

## Polska wersja Arduino TNC

Na bazie wcześniejszych prac powstała wersja konstrukcji opisana przez SQ9MDD.

Celem było przygotowanie prostego i taniego interfejsu TNC, który można łatwo zbudować z dostępnych elementów.

Typowa konfiguracja:

```text
Radio
  |
Arduino TNC
  |
USB / KISS
  |
Raspberry Pi / PC
  |
oprogramowanie APRS
```

Konstrukcja została przetestowana zarówno z materiałem testowym WA8LMF, jak i na rzeczywistym kanale APRS.

Projekt dobrze sprawdzał się jako niewielki modem dla Raspberry Pi, iGate lub stacji domowej.

## Rozwój oprogramowania przez SQ5RWU

Istotny wkład w rozwój późniejszych wersji kodu wniósł Łukasz **SQ5RWU**.

Historia kodu obejmuje między innymi:

- sprawdzanie CRC odebranych ramek,
- poprawki stabilności,
- poprawki obsługi odbioru,
- usprawnienia transmisji,
- porządkowanie kodu,
- poprawki wymagane do stabilnej pracy.

Wersja:

```text
0.15.3
```

zawiera duży zestaw poprawek i usprawnień SQ5RWU.

Następnie została przygotowana wersja:

```text
0.15.4
```

oznaczona jako stabilne wydanie projektu.

Przy uruchomieniu urządzenie przedstawia się komunikatem:

```text
Arduino TNC v.0.15.4
```

## KISS

Arduino TNC pracuje jako klasyczny **KISS TNC**.

Typowy układ wygląda tak:

```text
Radio
  |
Arduino TNC
  |
KISS
  |
APRSBox / Xastir / Linux AX.25 / inne oprogramowanie
```

KISS jest prostym standardowym interfejsem pomiędzy TNC a aplikacją.

Dzięki temu urządzenie nie jest związane z jednym konkretnym programem.

## AFSK 1200

Podstawowym trybem pracy jest:

```text
AFSK 1200 Bd
1200 / 2200 Hz
Bell 202
```

czyli klasyczna modulacja APRS używana na VHF.

Arduino generuje sygnał nadawczy programowo i steruje PTT.

## Arduino TNC Plus

Kolejnym etapem projektu było **Arduino TNC Plus**, w skrócie ATNC+.

Powstał jako rozwinięcie wcześniejszej konstrukcji.

Podstawowa funkcjonalność została zachowana, ale tor radiowy został rozbudowany.

Najważniejszą zmianą było dodanie układu:

```text
TCM3105
```

pracującego jako regeneracja sygnału według rozwiązania Adama SP5RZP.

Schemat funkcjonalny:

```text
Radio
  |
tor audio
  |
TCM3105
  |
Arduino
  |
KISS
  |
komputer
```

Celem było poprawienie jakości sygnału dostarczanego do części cyfrowej i zwiększenie skuteczności dekodowania w rzeczywistych warunkach radiowych.

## Zmiany sprzętowe w ATNC+

Oprócz TCM3105 wprowadzono również inne poprawki.

Dodano między innymi:

- kondensatory odcinające składową stałą na wejściu transformatorów,
- poprawiony tor audio,
- możliwość sterowania radiotelefonami ręcznymi,
- dodatkową zworkę konfiguracyjną,
- rezystor 2,2 kΩ dla określonych sposobów sterowania PTT.

Płytka ATNC+ jest większa od pierwotnej wersji ze względu na dodatkowy układ modemowy.

## Stabilne oprogramowanie ATNC+

Podczas testów Arduino TNC Plus okazało się, że również oprogramowanie wymaga dalszych poprawek.

Dużą część prac wykonał SQ5RWU.

W rezultacie powstała stabilna wersja oprogramowania używana z ATNC+.

Kod dostępny jest tutaj:

https://github.com/SQ9MDD/arduino_tnc

Repozytorium należy traktować jako późniejszą, poprawioną linię rozwojową projektu, a nie oryginalną wersję KI4MCW.

## Parametry komunikacji

W stabilnej wersji Arduino TNC port szeregowy pracuje z prędkością:

```text
19200 bit/s
```

Jest to prędkość używana przez interfejs KISS.

## Izolacja toru audio

W konstrukcji stosowane są transformatory separujące.

Ich zadaniem jest elektryczne oddzielenie radia od części cyfrowej.

Pomaga to ograniczyć:

- pętle masy,
- zakłócenia z komputera,
- problemy z poziomami audio,
- zakłócenia powstające przez USB.

## Sterowanie PTT

Arduino steruje nadajnikiem automatycznie.

Tor PTT wykorzystuje element separujący i tranzystor.

```text
Arduino
  |
PTT
  |
separacja
  |
Radio
```

Pozwala to bezpiecznie współpracować z różnymi typami radiotelefonów.

## Zastosowania

Arduino TNC i ATNC+ mogą pracować jako modem dla:

- APRS iGate,
- stacji domowej APRS,
- Packet Radio,
- Raspberry Pi,
- komputerów Linux,
- aplikacji obsługujących KISS,
- własnych projektów eksperymentalnych.

Typowa instalacja może wyglądać tak:

```text
Radio
  |
Arduino TNC Plus
  |
USB
  |
Raspberry Pi
  |
APRSBox
```

## Projekt edukacyjny

Jedną z największych wartości Arduino TNC jest możliwość prześledzenia całej drogi pakietu.

Projekt pozwala zobaczyć w praktyce:

- jak audio trafia do ADC,
- jak rozpoznawane są tony AFSK,
- jak dekodowana jest ramka AX.25,
- jak działa CRC,
- jak działa KISS,
- jak generowany jest sygnał TX,
- jak sterowane jest PTT.

Jest to bardzo dobry projekt dla osób zainteresowanych zarówno APRS, jak i mikrokontrolerami AVR.

## Historia projektu

Rozwój Arduino TNC można przedstawić w uproszczeniu tak:

```text
KI4MCW
  |
oryginalny programowy Arduino TNC
  |
M1GEO
  |
testy i rozwój toru radiowego
  |
SQ9MDD
  |
polska wersja sprzętowa
  |
SQ5RWU
  |
poprawki kodu i stabilizacja
  |
Arduino TNC Plus
```

Nie jest to więc pojedyncza konstrukcja jednego autora, lecz efekt kolejnych eksperymentów i poprawek wykonywanych przez kilku krótkofalowców.

## Dla kogo jest ten projekt?

Arduino TNC może być interesujący dla osób, które:

- chcą zbudować własny KISS TNC,
- eksperymentują z Arduino i AVR,
- chcą poznać AFSK 1200 od strony praktycznej,
- potrzebują prostego modemu do Raspberry Pi,
- interesują się AX.25,
- chcą zrozumieć działanie programowego modemu.

Arduino TNC Plus będzie dodatkowo interesujący dla osób, które chcą wykorzystać rozbudowany tor radiowy z regeneracją sygnału.

## Dokumentacja

Najważniejsze źródła:

- oryginalny Arduino TNC KI4MCW: https://sites.google.com/site/ki4mcw/Home/arduino-tnc
- eksperymenty M1GEO: https://www.george-smart.co.uk/arduino/arduino_tnc/
- Arduino TNC SQ9MDD: https://hamspirit.pl/SQ9MDD/?p=337
- Arduino TNC Plus: https://hamspirit.pl/SQ9MDD/?p=541
- późniejszy kod SQ9MDD/SQ5RWU: https://github.com/SQ9MDD/arduino_tnc
