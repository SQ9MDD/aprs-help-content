---
title: Dire Wolf
description: Programowy modem AX.25 i TNC do APRS, obsługujący AFSK, KISS, FX.25, digipeating i IGate.
template: doc
tableOfContents: true
---

**Dire Wolf** to programowy modem pakietowy, TNC oraz dekoder i enkoder AX.25 wykorzystujący kartę dźwiękową komputera i cyfrowe przetwarzanie sygnału.

Pozwala odbierać i nadawać ramki AX.25 bez konieczności używania klasycznego sprzętowego TNC. Może działać samodzielnie albo jako warstwa radiowa dla innych aplikacji APRS.

Oficjalne repozytorium projektu:

https://github.com/wb2osz/direwolf

## Do czego służy Dire Wolf

W najprostszym zastosowaniu Dire Wolf zamienia komputer wyposażony w interfejs audio w modem packet radio.

```text
Radio <-> audio/PTT <-> Dire Wolf <-> KISS/AGW <-> aplikacja APRS
```

Program odbiera sygnał audio z radia, demoduluje transmisję i dekoduje ramki AX.25. W przeciwnym kierunku generuje odpowiedni sygnał audio oraz może sterować PTT radiotelefonu.

Dire Wolf może pracować między innymi jako:

- programowy TNC,
- dekoder i enkoder AX.25,
- dekoder APRS,
- modem KISS dla innych aplikacji,
- digipeater APRS,
- IGate,
- tracker i generator beaconów,
- bramka APRStt.

## Wysoka skuteczność dekodowania

Jedną z najważniejszych zalet Dire Wolfa jest bardzo dobra skuteczność dekodowania ramek AX.25.

Program nie jest jedynie prostym programowym odpowiednikiem starego sprzętowego TNC. Wykorzystuje cyfrowe przetwarzanie sygnału oraz kilka technik poprawiających odbiór w rzeczywistych warunkach radiowych.

W zależności od konfiguracji może wykorzystywać między innymi:

- różne profile demodulatora,
- filtrowanie cyfrowe,
- automatyczne wyrównywanie poziomów mark i space,
- kilka równoległych demodulatorów,
- kilka punktów decyzyjnych demodulatora,
- równoległe próby dekodowania tego samego sygnału różnymi metodami.

Ma to znaczenie przy słabych sygnałach, nieidealnym torze audio, różnych charakterystykach radiotelefonów oraz pracy na HF.

Dire Wolf był również porównywany z klasycznymi TNC przy użyciu zestawów testowych takich jak WA8LMF TNC Test CD. W praktyce potrafi poprawnie zdekodować część ramek, z którymi prostsze lub starsze rozwiązania TNC mają problem.

Nie oznacza to oczywiście, że jakość toru radiowego przestaje mieć znaczenie. Prawidłowe poziomy audio, szerokość pasma, odstęp sygnał/szum i jakość odbiornika nadal są bardzo ważne.

## 1200 bit/s AFSK

Najczęściej używanym trybem APRS na VHF i UHF jest:

```text
MODEM 1200
```

Typowe częstotliwości tonów AFSK to 1200 Hz i 2200 Hz.

Jest to podstawowy tryb dla klasycznego APRS na paśmie 2 m i zwykle działa z typowym połączeniem audio pomiędzy radiem i komputerem.

## 300 bit/s AFSK na HF

Dire Wolf obsługuje również 300 bit/s AFSK, używane przede wszystkim w packet radio i APRS na HF SSB.

```text
MODEM 300
```

Domyślne tony to 1600 Hz i 1800 Hz.

Na HF szczególnie przydatna jest możliwość wykorzystania kilku demodulatorów pracujących równolegle na lekko przesuniętych częstotliwościach. Pomaga to kompensować niewielkie różnice strojenia pomiędzy stacjami oraz zmiany występujące w torze SSB.

Przykład bardziej zaawansowanej konfiguracji:

```text
MODEM 300 1600:1800 7@30 /4
```

Dobór parametrów należy dostosować do konkretnego toru radiowego i używanej wersji Dire Wolfa.

## 2400 i 4800 bit/s PSK

Dire Wolf obsługuje również tryby PSK 2400 i 4800 bit/s.

Są one spotykane znacznie rzadziej niż klasyczne 1200 AFSK, ale mogą być używane w bardziej specjalistycznych zastosowaniach packet radio.

## 9600 bit/s

Dire Wolf posiada obsługę transmisji 9600 bit/s w stylu G3RUH.

Nie należy jednak traktować jej jako prostego odpowiednika 1200 AFSK. Typowe wyjście głośnikowe i wejście mikrofonowe radia zwykle nie zapewniają odpowiedniego toru dla 9600 bit/s ze względu na filtrację, preemfazę i deemfazę.

W praktyce wymagany jest odpowiednio szeroki, możliwie bezpośredni tor danych radia, na przykład wyjście typu 9600 packet, discriminator lub direct audio.

Dlatego dla typowej stacji APRS podłączonej do zwykłego wejścia i wyjścia audio najbardziej praktycznym rozwiązaniem pozostaje 1200 AFSK.

## FX.25

Dire Wolf obsługuje również **FX.25**, czyli rozszerzenie AX.25 wykorzystujące korekcję błędów FEC.

FX.25 dodaje do transmisji dodatkowe dane korekcyjne Reed-Solomon. Dzięki temu odbiornik obsługujący FX.25 może w określonych warunkach odtworzyć poprawną ramkę mimo części błędów powstałych podczas transmisji.

Istotną cechą FX.25 jest zachowanie zgodności z klasycznym AX.25.

Odbiór FX.25 w Dire Wolfie jest obsługiwany automatycznie. Transmisję FX.25 trzeba natomiast świadomie włączyć.

Przykład:

```text
FX25TX 1
```

W zależności od wersji można również określić liczbę bajtów parzystości, na przykład 16, 32 lub 64.

FX.25 zwiększa ilość danych przesyłanych drogą radiową, dlatego nie zawsze jest dobrym wyborem dla obciążonego kanału APRS. Jest jednak bardzo interesującym rozwiązaniem w packet radio i w łącznościach prowadzonych w trudniejszych warunkach.

## Bardzo ważne: FIX_BITS

Dire Wolf posiada mechanizm `FIX_BITS`, który próbuje odzyskać ramkę AX.25 odebraną z błędnym FCS.

Mechanizm może zmieniać bity w odebranej ramce i sprawdzać, czy po zmianie FCS stanie się poprawne.

W przypadku eksperymentów z packet radio może to być interesujące, ale w infrastrukturze APRS wymaga szczególnej ostrożności.

Jeżeli ramka miała błędny FCS, oznacza to, że została odebrana z błędem. Próba jej naprawienia może doprowadzić do przekazania dalej zmodyfikowanej treści, której odbiorca nie będzie już w stanie odróżnić od poprawnie odebranej ramki.

Zmiana pojedynczego bitu może dotyczyć między innymi:

- znaku stacji,
- SSID,
- ścieżki,
- pozycji,
- wiadomości,
- telemetrii,
- identyfikatora wiadomości,
- dowolnego fragmentu payloadu APRS.

Dlatego w typowej infrastrukturze APRS, szczególnie gdy Dire Wolf pracuje jako modem dla IGate lub digipeatera, zalecane jest świadome wyłączenie tego mechanizmu:

```text
FIX_BITS 0
```

Pozwala to zachować prostą zasadę: dalej przekazywane są wyłącznie ramki, które rzeczywiście zostały odebrane z prawidłowym FCS.

### FIX_BITS a FX.25

`FIX_BITS` i FX.25 nie są tym samym mechanizmem.

FX.25 korzysta z dodatkowych danych FEC świadomie wysłanych przez nadajnik. Odbiornik otrzymuje więc informację pozwalającą matematycznie korygować określoną liczbę błędów.

`FIX_BITS` próbuje natomiast znaleźć taką zmianę bitów w już uszkodzonej klasycznej ramce AX.25, aby uzyskać prawidłowe FCS.

Można więc jednocześnie używać:

```text
FIX_BITS 0
```

i nadal odbierać oraz korygować ramki FX.25.

## KISS i współpraca z innymi aplikacjami

Dire Wolf może działać jako wirtualny TNC i udostępniać odebrane ramki innym aplikacjom.

Obsługuje między innymi:

- KISS przez TCP,
- KISS przez port szeregowy lub pseudo-terminal,
- interfejs sieciowy AGW.

Typowa architektura może wyglądać tak:

```text
Radio
  |
  | audio + PTT
  v
Dire Wolf
  |
  | KISS TCP
  v
APRSBox / YAAC / Xastir / inna aplikacja
```

Dzięki temu Dire Wolf może zajmować się wyłącznie warstwą radiową i AX.25, a cała logika APRS może znajdować się w innym programie.

Domyślny port KISS TCP to zazwyczaj:

```text
8001
```

## Digipeater

Dire Wolf posiada własną obsługę digipeatingu APRS.

Może odbierać ramki z kanału radiowego, analizować ich ścieżkę i retransmitować pakiety zgodnie ze skonfigurowanymi zasadami.

Przed uruchomieniem digipeatera należy jednak dobrze rozumieć działanie ścieżek APRS, w szczególności `WIDE1-1`, `WIDE2-1` oraz zasady ograniczania niepotrzebnych retransmisji.

Źle skonfigurowany digipeater może znacząco zwiększyć zajętość kanału radiowego.

## IGate

Dire Wolf może również pracować jako bramka pomiędzy radiową siecią APRS i APRS-IS.

Najprostszy kierunek pracy wygląda tak:

```text
RF -> Dire Wolf -> APRS-IS
```

Dire Wolf posiada również funkcje związane z przekazywaniem wybranych danych z APRS-IS na RF.

Konfiguracja kierunku APRS-IS -> RF wymaga szczególnej ostrożności. Ruch internetowy nie powinien być bezwarunkowo retransmitowany na kanał radiowy.

## Sterowanie PTT

Do transmisji potrzebne jest sterowanie nadajnikiem.

Dire Wolf obsługuje kilka metod PTT, między innymi:

- RTS i DTR portu szeregowego,
- GPIO,
- interfejsy wykorzystujące układy CM108/CM119,
- Hamlib,
- wybrane dedykowane interfejsy radiowe.

Przykład sterowania linią RTS:

```text
PTT /dev/ttyUSB0 RTS
```

W zależności od użytego interfejsu może być potrzebna odwrotna polaryzacja.

## Interfejs audio i SDR

Dire Wolf korzysta ze standardowych urządzeń audio systemu operacyjnego.

Może współpracować między innymi z:

- wbudowanymi kartami dźwiękowymi,
- kartami USB,
- interfejsami radiowymi z własnym kodekiem audio,
- wirtualnymi urządzeniami audio,
- odbiornikami SDR.

Przykładowy tor odbiorczy z SDR:

```text
RTL-SDR
   |
   v
rtl_fm / inne oprogramowanie SDR
   |
   v
Dire Wolf
   |
   v
KISS / APRS
```

Pozwala to zbudować prosty odbiornik APRS lub RX-only IGate.

## Praca ciągła

Dire Wolf dobrze nadaje się do instalacji pracujących przez całą dobę.

Może działać na niewielkich komputerach, takich jak Raspberry Pi, jako modem, TNC, IGate, digipeater lub warstwa radiowa dla innej aplikacji.

Jest to osobna zaleta od wysokiej skuteczności jego demodulatorów.

## Przykładowa podstawowa konfiguracja

Bardzo uproszczona konfiguracja typowej stacji APRS 1200 może wyglądać tak:

```text
ADEVICE plughw:0,0

CHANNEL 0
MYCALL SQ9ABC

MODEM 1200

FIX_BITS 0
```

W rzeczywistej instalacji trzeba dodatkowo skonfigurować odpowiednie urządzenie audio, poziomy sygnału, PTT, interfejs KISS oraz funkcje właściwe dla danej stacji.

Przed uruchomieniem nadawania warto najpierw skonfigurować odbiór i sprawdzić jakość dekodowania ramek.

## Diagnostyka

Dire Wolf wyświetla w terminalu wiele informacji przydatnych podczas uruchamiania i diagnostyki stacji.

Można obserwować między innymi:

- odebrane ramki AX.25,
- zdekodowaną zawartość APRS,
- poziom sygnału audio,
- kanał odbiorczy,
- informacje o demodulatorze,
- komunikaty diagnostyczne.

Dzięki temu jest bardzo użytecznym narzędziem również podczas strojenia toru audio i diagnozowania problemów z odbiorem.

## Dokumentacja

Najważniejsze źródła:

- repozytorium projektu: https://github.com/wb2osz/direwolf
- dokumentacja projektu: https://github.com/wb2osz/direwolf/tree/master/doc
- dodatkowa dokumentacja: https://github.com/wb2osz/direwolf-doc
- przykładowa konfiguracja: https://github.com/wb2osz/direwolf/blob/master/conf/generic.conf

W przypadku konfiguracji konkretnej wersji programu należy korzystać z dokumentacji odpowiadającej tej wersji.
