---
title: Warstwy protokołu APRS
description: Podział funkcjonalny transmisji APRS przez radio i APRS-IS oraz rola aplikacji, AX.25, modemu i kanału radiowego.
template: doc
tableOfContents: true
---

APRS określa sposób zapisu i interpretacji informacji wymienianych przez stacje, ale nie definiuje całego toru transmisyjnego. W typowej sieci radiowej wykorzystuje ramki AX.25, modem i radiotelefon. W Internecie informacje APRS są przesyłane w postaci tekstowej przez APRS-IS, z wykorzystaniem TCP/IP.

Poniższe schematy przedstawiają **praktyczny podział funkcji**, a nie formalne odwzorowanie modelu OSI. Poszczególne funkcje mogą być realizowane przez oddzielne urządzenia albo zintegrowane w jednym radiotelefonie czy programie komputerowym.

## Transmisja radiowa

![Podział funkcjonalny transmisji APRS przez radio](./_img/diagram01.png)

W klasycznej transmisji VHF informacje przygotowane przez aplikację są umieszczane w ramce AX.25. Modem przekształca dane cyfrowe w sygnał odpowiedni dla toru radiowego, a radiotelefon nadaje go na wybranej częstotliwości.

### Aplikacja użytkownika

Aplikacja tworzy informacje przeznaczone do wysłania lub interpretuje dane odebrane od innych stacji. Może obsługiwać raporty pozycji, wiadomości, obiekty, telemetrię i informacje pogodowe. Może działać jako osobny program, ale również jako funkcja radiotelefonu lub trackera.

### Dane APRS

APRS definiuje formaty informacji i zasady ich interpretacji. Określa między innymi, jak zapisać raport pozycji, wiadomość czy dane telemetryczne oraz jak rozpoznać rodzaj informacji.

W typowej ramce APRS zasadnicze dane znajdują się w polu *Information* ramki AX.25. Nie oznacza to jednak, że pozostałe pola nie mają znaczenia dla APRS. Protokół wykorzystuje także określone elementy adresowania AX.25, a format Mic-E koduje część informacji w polu adresu docelowego.

APRS i AX.25 pełnią więc różne, lecz współpracujące funkcje: AX.25 określa strukturę ramki radiowej, natomiast APRS definiuje sposób zapisu i interpretacji przenoszonych informacji oraz wykorzystania wybranych pól tej ramki.

### AX.25

AX.25 jest protokołem warstwy łącza danych stosowanym w packet radio. Definiuje ramkę zawierającą między innymi adresy źródłowy i docelowy, opcjonalną listę adresów digipeaterów, pole sterujące, identyfikator protokołu PID, pole *Information* i sumę kontrolną FCS.

Typowy ruch APRS wykorzystuje ramki **UI** (*Unnumbered Information*), które nie wymagają wcześniejszego zestawienia połączenia AX.25. Dzięki temu jedna transmisja może zostać odebrana przez wiele stacji znajdujących się w zasięgu. Samo nadanie ramki UI nie zapewnia jednak potwierdzenia jej odbioru. Ewentualne potwierdzenia wiadomości APRS są odrębnym mechanizmem.

### Modem i modulacja

Modem zamienia dane cyfrowe na sygnał odpowiedni dla toru nadawczo-odbiorczego, a podczas odbioru wykonuje operację odwrotną. W klasycznym APRS na VHF powszechnie stosuje się **AFSK 1200**, oparte na Bell 202, z szybkością transmisji 1200 bit/s i tonami audio 1200 oraz 2200 Hz.

Modem może być samodzielnym urządzeniem, częścią TNC, układem wbudowanym w radiotelefon albo programem wykorzystującym kartę dźwiękową. AFSK jest jednym ze sposobów przesyłania ramek, a nie formatem danych APRS.

### Radio i kanał RF

Radiotelefon nadaje i odbiera sygnał radiowy. Kanał RF jest wspólnym medium, z którego korzystają stacje pracujące na danej częstotliwości. Na skuteczność transmisji wpływają między innymi anteny, moc nadawcza, propagacja, zakłócenia i obciążenie kanału.

W europejskich sieciach APRS na VHF powszechnie używana jest częstotliwość **144,800 MHz**, ale ani ta częstotliwość, ani AFSK 1200 nie stanowią definicji samego APRS. Informacje APRS mogą być przesyłane także innymi metodami i na innych pasmach.

## Jak warstwy współpracują: przykład pakietu

W logach i aplikacjach pakiet APRS często jest przedstawiany w czytelnej postaci tekstowej:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

W tym zapisie:

| Element | Znaczenie |
| --- | --- |
| `SQ9MDD-7` | Adres źródłowy AX.25. |
| `APRS` | Adres docelowy AX.25, tutaj użyty zgodnie z konwencją APRS, a nie jako adres konkretnego odbiorcy. |
| `WIDE1-1` | Element ścieżki digipeaterów zapisanej w polach adresowych AX.25. |
| `:` | Separator nagłówka i pola informacji w reprezentacji tekstowej. |
| `!5012.34N/01956.78E>` | Zawartość pola *Information*, tutaj nieskompresowany raport pozycji APRS. Znak `!` jest identyfikatorem typu danych, a końcowy `>` oznacza symbol stacji. |

Przykład pokazuje, dlaczego nie należy utożsamiać całego widocznego zapisu z samym polem danych APRS. Nagłówek wykorzystuje pola AX.25, którym APRS może nadawać dodatkowe znaczenie, natomiast pole *Information* zawiera dane zapisane w formacie APRS.

**Zapis tekstowy nie jest dosłowną kopią ramki transmitowanej przez radio.** Rzeczywista ramka AX.25 zawiera również binarnie zakodowane pola, których nie widać w powyższej postaci, w tym pole sterujące, PID i FCS. Separator `:` należy do reprezentacji tekstowej, a nie do struktury ramki radiowej.

Szczegółową budowę ramki omawia artykuł [„Anatomia pakietu APRS”](../03-packet-anatomy/).

## Transmisja przez Internet

![Podział funkcjonalny transmisji APRS przez Internet](./_img/diagram02.png)

Po stronie internetowej aplikacja nadal tworzy lub odczytuje informacje APRS, ale do ich przesyłania nie potrzebuje radiowego modemu ani ramki AX.25 w postaci transmitowanej na RF. Klient komunikuje się z serwerami **APRS-IS** za pośrednictwem połączenia **TCP/IP**.

APRS-IS wykorzystuje tekstową reprezentację pakietów, obejmującą nagłówek i pole informacji. Serwery APRS-IS odbierają pakiety i rozprowadzają je do odpowiednich połączonych klientów zgodnie z zasadami działania sieci, między innymi zastosowanymi filtrami.

**APRS-IS nie jest internetowym tunelem przenoszącym surowe ramki AX.25.** Pozwala natomiast dystrybuować informacje APRS za pomocą innego mechanizmu transmisji. Serwery APRS-IS są elementami infrastruktury, a nie odrębną warstwą modelu OSI.

## IGate: połączenie obu środowisk

IGate łączy sieć radiową z APRS-IS. Po odebraniu pakietu z RF może przekazać go do sieci internetowej w odpowiedniej reprezentacji tekstowej. Przy takim przekazaniu mogą zostać dodane informacje właściwe dla APRS-IS, na przykład *q-construct*. Nie oznacza to, że były one częścią oryginalnej ramki nadanej przez stację radiową.

Ruch w przeciwnym kierunku podlega odrębnym regułom. IGate nie powinien traktować dowolnego pakietu otrzymanego z APRS-IS jako ramki gotowej do bezpośredniego nadania na RF. Szczegółowe zasady przekazywania pakietów, w tym użycie formatu *third-party traffic*, należą do opisu działania IGate.

Dzięki temu rozróżnieniu łatwiej zrozumieć, dlaczego pakiet widoczny w APRS-IS może zawierać dodatkowe elementy, których nie było w jego transmisji radiowej.

## Podsumowanie

| Element | Główna funkcja |
| --- | --- |
| Aplikacja | Tworzenie, odbieranie i prezentacja informacji. |
| APRS | Format i interpretacja informacji, również z wykorzystaniem wybranych pól adresowych. |
| AX.25 | Struktura ramki radiowej, adresowanie, ścieżka i kontrola błędów. |
| Modem | Zamiana danych cyfrowych na sygnał używany w danym sposobie transmisji i odwrotnie. |
| Radio i kanał RF | Fizyczne przesłanie sygnału pomiędzy stacjami. |
| APRS-IS | Internetowa wymiana i dystrybucja pakietów w reprezentacji tekstowej. |
| TCP/IP | Transport danych pomiędzy klientami i serwerami APRS-IS. |
| IGate | Kontrolowane przekazywanie pakietów między siecią radiową a APRS-IS. |

Najważniejsze rozróżnienie dotyczy **znaczenia informacji** i **sposobu ich przenoszenia**. APRS określa, co oznaczają dane, korzystając przy tym z określonych mechanizmów AX.25. Na drodze radiowej informacje są przenoszone w ramkach AX.25, natomiast w APRS-IS są dystrybuowane w reprezentacji tekstowej przez TCP/IP.

## Źródła

- [*APRS Protocol Reference*, wersja 1.0.1](https://www.aprs.org/doc/APRS101.PDF), rozdziały 3–5: wykorzystanie AX.25 oraz formaty danych APRS.
- [*AX.25 Link Access Protocol for Amateur Packet Radio*, wersja 2.2](https://tarpn.net/t/faq/files/AX25.2.2-Sep%2017-1-10Sep17.pdf): struktura ramki i ramki UI.
- [*Connecting to APRS-IS*](https://www.aprs-is.net/connecting.aspx) i [*Server Design*](https://www.aprs-is.net/ServerDesign.aspx): połączenia klientów, reprezentacja tekstowa i dystrybucja pakietów.
