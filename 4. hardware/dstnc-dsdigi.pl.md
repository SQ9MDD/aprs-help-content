---
title: dsTNC i dsDIGI
description: Sprzętowy modem KISS TNC i digipeater APRS oparty na procesorze DSP dsPIC, z bardzo skutecznym demodulatorem AFSK 1200.
template: doc
tableOfContents: true
---

dsTNC to sprzętowy modem **AFSK 1200 i kontroler KISS TNC** zaprojektowany przez Tomka SP9UOB.

Na tym samym sprzęcie może również pracować firmware **dsDIGI**, który zmienia urządzenie w samodzielny digipeater APRS.

Strona projektu dsTNC:

https://sp9uob.verox.pl/dstnc.html

Opis dsDIGI:

https://sp9uob.verox.pl/dsdigi.html

Projekt jest interesującym przykładem wykorzystania cyfrowego przetwarzania sygnałów w sprzętowym modemie APRS.

Zamiast klasycznego analogowego układu modemowego dekodowanie AFSK realizuje procesor **dsPIC30F4013**.

## Dwa zastosowania tego samego sprzętu

Hardware dsTNC może pracować w dwóch podstawowych rolach.

### KISS TNC

```text
Radio
  |
audio + PTT
  |
dsTNC
  |
KISS
  |
komputer / aplikacja APRS
```

W tym wariancie urządzenie realizuje modem AFSK i warstwę AX.25, a zewnętrzne oprogramowanie zajmuje się dalszą obsługą pakietów.

### Samodzielny digipeater

Po wgraniu firmware dsDIGI układ może pracować bez komputera:

```text
Radio
  |
audio + PTT
  |
dsDIGI
  |
digipeater APRS
```

Ta sama płytka może więc pełnić rolę sprzętowego modemu albo autonomicznego węzła APRS.

## DSP zamiast klasycznego modemu analogowego

Jedną z najciekawszych cech dsTNC jest sposób demodulacji sygnału.

Projekt wykorzystuje **cyfrowe przetwarzanie sygnałów DSP**.

Sygnał audio z odbiornika trafia przez filtr dolnoprzepustowy na wejście przetwornika ADC procesora.

Dalsza obróbka odbywa się programowo.

Uproszczony tor odbiorczy:

```text
Radio
  |
audio
  |
filtr
  |
ADC
  |
DSP
  |
AX.25
```

Takie podejście pozwala realizować bardziej zaawansowane algorytmy demodulacji bez używania klasycznego układu modemowego.

## Bardzo skuteczny demodulator AFSK 1200

To jeden z najmocniejszych punktów tej konstrukcji.

Autor zastosował algorytm inspirowany rozwiązaniem opisanym przez Thomasa Sailera HB9JNX.

Podczas testów z popularnym zestawem nagrań **WA8LMF APRS Test CD** modem dsTNC poprawnie zdekodował na drugiej ścieżce:

```text
900 ramek
```

Dla porównania w ramach tego samego testu klasyczny modem oparty na układzie **TCM3105** zdekodował:

```text
747 ramek
```

Wynik dobrze pokazuje skuteczność zastosowanego algorytmu DSP, szczególnie przy trudniejszych, zaszumionych i zniekształconych sygnałach.

## AFSK 1200

Podstawowym trybem pracy dsTNC jest klasyczny modem APRS:

```text
AFSK 1200 Bd
1200 Hz / 2200 Hz
Bell 202
```

Jest to standardowo używana modulacja klasycznego APRS na paśmie 2 m.

Sygnał odbierany jest przez przetwornik ADC procesora, natomiast sygnał nadawczy generowany jest cyfrowo metodą PWM i po filtracji trafia do wejścia nadajnika.

## KISS TNC

Firmware dsTNC realizuje funkcję **KISS TNC**.

Port szeregowy może pracować z prędkością:

```text
57600 bit/s
```

Urządzenie może dzięki temu współpracować między innymi z:

- aplikacjami APRS,
- Xastir,
- UI-View,
- stosem AX.25 w Linuxie,
- innym oprogramowaniem obsługującym KISS.

KISS zapewnia prosty i powszechnie stosowany interfejs pomiędzy modemem/TNC a aplikacją.

Dzięki temu dsTNC nie jest związany z jednym konkretnym programem.

## dsDIGI

Alternatywnym firmware dla tego samego sprzętu jest **dsDIGI**.

Po jego uruchomieniu urządzenie może pracować jako autonomiczny digipeater APRS.

Podstawowa konfiguracja obejmuje między innymi:

- znak stacji,
- alias digipeatera,
- pozycję,
- wysokość,
- symbol APRS,
- beacon,
- częstotliwość beaconu,
- ścieżkę beaconu,
- TXDELAY,
- TXTAIL,
- prędkość portu szeregowego.

Konfiguracja odbywa się przez prosty terminal tekstowy.

## WIDE1-1

dsDIGI zostało zaprojektowane przede wszystkim do pracy jako lokalny digipeater typu:

```text
WIDE1-1
```

Alias można zmienić, a implementacja obsługuje jeden alias typu `WIDEn-N`.

To prosta i czytelna konfiguracja odpowiednia dla lokalnego digipeatera.

## Beacon własnej stacji

dsDIGI może automatycznie generować własny beacon pozycyjny.

Konfigurowane są między innymi:

- znak,
- szerokość geograficzna,
- długość geograficzna,
- wysokość,
- symbol,
- komentarz,
- ścieżka,
- interwał.

Pozycja dodawana jest automatycznie do treści beaconu.

Dzięki temu digipeater może prawidłowo identyfikować się w sieci APRS.

## Telemetria

Ciekawą funkcją dsDIGI jest możliwość generowania telemetrii APRS.

Urządzenie może raportować:

- napięcie zasilania,
- temperaturę po podłączeniu czujnika **DS18B20**,
- liczbę odebranych pakietów,
- liczbę nadanych pakietów.

Dzięki temu można zdalnie obserwować podstawowy stan instalacji.

W przypadku digipeatera znajdującego się na trudno dostępnym obiekcie takie informacje mogą być bardzo użyteczne.

## Informacje diagnostyczne

dsDIGI nie ogranicza się tylko do retransmisji pakietów.

Na terminalu może pokazywać informacje o odebranych ramkach, w tym:

- źródło,
- adres docelowy,
- ścieżkę,
- zawartość,
- zdekodowaną pozycję,
- odległość od digipeatera,
- kierunek do odebranej stacji.

Przykładowo urządzenie może wyliczyć:

```text
Dist = 56 km
bearing = 78 degrees
```

Daje to wygodny sposób obserwowania lokalnego ruchu bez dodatkowego oprogramowania APRS.

## Status digipeatera

dsDIGI generuje również własny status.

Może w nim podawać między innymi:

- czas pracy od uruchomienia,
- liczbę odebranych ramek,
- liczbę powtórzonych pakietów.

Pozwala to w prosty sposób ocenić aktywność urządzenia.

## Regulacja toru nadawczego

Firmware posiada także funkcję generowania sygnałów testowych:

```text
1200 Hz
2200 Hz
```

Można ich użyć podczas ustawiania poziomu i dewiacji toru nadawczego.

To bardzo praktyczna funkcja podczas uruchamiania sprzętowego modemu.

## Prosta konstrukcja

Jedną z zalet dsTNC jest stosunkowo niewielka liczba elementów.

Większość funkcji modemowych realizowana jest programowo przez procesor dsPIC.

Według autora układ nie wymaga specjalnego strojenia.

Po zaprogramowaniu procesora najważniejszą czynnością podczas uruchamiania jest prawidłowe ustawienie poziomu sygnału audio.

## Projekt sprzętowy oparty na DSP

dsTNC i dsDIGI są ciekawym przykładem sprzętowej realizacji APRS z wykorzystaniem DSP.

Projekt łączy:

- cyfrową demodulację AFSK,
- KISS,
- autonomiczny digipeater,
- telemetrię APRS,
- analizę pozycji odebranych stacji,
- prostą diagnostykę przez terminal.

Dostępne na stronie firmware dsDIGI v1.25 pochodzi z:

```text
24 listopada 2013
```

Projekt pozostaje interesujący technicznie i pokazuje praktyczne wykorzystanie dsPIC jako modemu i sterownika APRS.

## dsTNC a dsDIGI

Najłatwiej zapamiętać różnicę w ten sposób.

### dsTNC

```text
radio
  |
modem AFSK
  |
KISS
  |
zewnętrzna aplikacja
```

To przede wszystkim sprzętowy modem i TNC.

### dsDIGI

```text
radio
  |
modem AFSK
  |
logika digipeatera
```

To autonomiczny digipeater wykorzystujący ten sam hardware.

O tym, jaką funkcję pełni urządzenie, decyduje wgrany firmware.

## Dla kogo jest ten projekt?

dsTNC i dsDIGI mogą być interesujące dla osób, które:

- chcą uruchomić sprzętowy KISS TNC,
- potrzebują prostego autonomicznego digipeatera,
- eksperymentują z AX.25 i AFSK 1200,
- interesują się cyfrową demodulacją DSP,
- chcą poznać sprzętową realizację modemu APRS,
- posiadają istniejące urządzenie dsTNC lub dsDIGI.

Szczególnie interesującym elementem projektu pozostaje **skuteczny demodulator DSP zastosowany w dsTNC**.

## Dokumentacja

Najważniejsze źródła:

- dsTNC: https://sp9uob.verox.pl/dstnc.html
- dsDIGI: https://sp9uob.verox.pl/dsdigi.html

Na stronie projektu dostępne są również:

- schemat ideowy,
- zestawienie elementów,
- schemat montażowy,
- rysunek PCB,
- firmware,
- historia zmian.
