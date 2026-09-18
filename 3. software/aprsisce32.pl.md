---
title: APRSISCE/32
description: Rozbudowany klient APRS dla Windows i Windows Mobile, obsługujący mapy, RF, APRS-IS, wiadomości, obiekty, MultiLine, NWS-WARN, direction finding, iGate i wiele innych elementów protokołu APRS.
template: doc
tableOfContents: true
---

APRSISCE/32 to jeden z najbardziej rozbudowanych klasycznych klientów APRS.

Program łączy w jednej aplikacji mapę APRS, połączenie z APRS-IS, obsługę radia i TNC, wiadomości, obiekty i itemy, telemetrię, pogodę, iGate, beaconing, śledzenie stacji, Direction Finding, MultiLine, obiekty obszarowe, NWS-WARN i zaawansowane filtrowanie APRS-IS.

Projekt został stworzony przez **Lynna Deffenbaugh KJ4ERJ**.

Dokumentacja projektu:

https://aprsisce.wikidot.com/

Pobieranie:

https://aprsisce.wikidot.com/downloads

Grupa użytkowników i wsparcia:

https://groups.io/g/APRSISCE

## APRSISCE i APRSIS32

APRSISCE był przeznaczony dla urządzeń z Windows Mobile i Windows CE. APRSIS32 jest wersją dla komputerów z Windows i działa również na 64-bitowych wersjach systemu.

## Pełny terminal APRS

```text
Radio
  |
TNC / modem
  |
APRSIS32
  |
APRS-IS
```

Jedna aplikacja może jednocześnie odbierać RF, wyświetlać stacje na mapie, wysyłać własne beacony, obsługiwać wiadomości, przekazywać ruch do APRS-IS, kierować wybrany ruch z APRS-IS na RF oraz tworzyć i wysyłać obiekty.

## Mapy i śledzenie

Program wykorzystuje mapy kafelkowe, przede wszystkim OpenStreetMap, z lokalnym cache. Obsługuje śledzenie stacji, MultiTrack, historię ruchu i zapis śladów GPX.

## Scroller

Charakterystycznym elementem APRSIS32 jest Scroller po lewej stronie głównego okna. Pozwala szybko rozróżnić pochodzenie i sposób obsługi ramek, w tym ruch bezpośrednio z RF, pakiety związane z transmisją APRS-IS -> RF i ramki powtórzone przez lokalny digipeater.

## Obsługa radia i modemów

APRSISCE/32 współpracuje między innymi z klasycznymi TNC, KISS TNC, radiami Kenwood z APRS, AGWPE, UZ7HO SoundModem i Dire Wolf. Obsługiwane są porty szeregowe i połączenia sieciowe.

## KISS i AGW

Program może pracować bezpośrednio z urządzeniami KISS, a także korzystać z interfejsu AGW.

```text
Radio
  |
Dire Wolf / SoundModem
  |
AGW
  |
APRSIS32
```

Pozwala to wykorzystać APRSIS32 jako pełny interfejs użytkownika, a dekodowanie AFSK pozostawić osobnemu modemowi programowemu.

## APRS-IS i zaawansowane filtry

Program może jednocześnie odbierać dane z RF i APRS-IS i składać je w jeden obraz sytuacji.

Obsługuje zaawansowane filtry APRS-IS według obszaru, promienia, znaku, prefiksu, digipeatera, iGate, obiektów, symbolu, typu pakietu i wiadomości grupowych.

```text
m/50
```

oznacza ruch w promieniu 50 km od własnej pozycji.

## iGate

APRSIS32 może pracować jako iGate, zarówno w kierunku RF -> APRS-IS, jak i z kontrolowanym ruchem APRS-IS -> RF.

Dla poszczególnych portów można określić między innymi RF to IS, IS to RF, Messages, Bulletins/Objects, Beacon, Telemetry i transmit enable.

## Digipeater

APRSIS32 posiada także funkcję digipeatera. Można definiować przekształcenia ścieżek, na przykład:

```text
WIDE1-1=WIDE1*
```

## Wiadomości APRS

Program obsługuje wiadomości tekstowe, identyfikatory wiadomości, ACK, retransmisje oraz komunikację przechodzącą pomiędzy RF i APRS-IS.

## Obiekty i itemy

APRSISCE/32 posiada bardzo rozbudowaną obsługę obiektów APRS. Mogą one reprezentować przemienniki, częstotliwości, miejsca spotkań, punkty pomocy, zagrożenia, stacje techniczne i miejsca aktywności.

Obiekt może zawierać pozycję, symbol, komentarz, ścieżkę, interwał, częstotliwość, ton CTCSS i informacje potrzebne do QSY.

## Obiekty częstotliwości

Program interpretuje APRS Frequency Specification.

```text
145.650MHz T077 -060
```

Z takiego obiektu może odczytać częstotliwość, ton i offset.

## QRU, czyli APRS Info-kiosk

QRU pozwala przechowywać dużą liczbę obiektów lokalnie i wysyłać je dopiero wtedy, gdy użytkownik ich potrzebuje.

```text
FUEL
FOOD
HOSP
RP2M
```

Pozwala to ograniczyć niepotrzebny ruch radiowy.

## MultiLine i obiekty obszarowe

APRSISCE/32 obsługuje MultiLine, dzięki czemu w APRS można przesyłać nie tylko punkt, ale również linię, trasę, granicę, wielokąt i obszar.

Można w ten sposób oznaczyć teren działań, strefę zamkniętą, obszar poszukiwań, teren imprezy, obszar zagrożenia albo trasę.

## NWS-WARN

Jedną z najbardziej zaawansowanych funkcji APRSISCE/32 jest obsługa ostrzeżeń National Weather Service.

Program potrafi odbierać obiekty NWS, identyfikować obszar ostrzeżenia, wyświetlać alerty na mapie i korzystać z lokalnych plików shapefile.

Wykorzystywane są zestawy:

```text
SHP
SHX
DBF
```

Dzięki temu niewielka informacja przesłana przez APRS może zostać połączona z lokalną bazą granic i zamieniona w dokładny obszar ostrzeżenia na mapie.

## Direction Finding

APRSISCE/32 potrafi interpretować i wizualizować pakiety Direction Finding.

Program może wyświetlać:

```text
DF circles
```

co pozwala nanosić pomiary z wielu punktów i wykorzystywać je do lokalizowania źródła sygnału oraz triangulacji.

## Pogoda i telemetria

Program obsługuje stacje pogodowe APRS, standardową telemetrię oraz generowanie pakietów pogodowych z pliku:

```text
wxnow.txt
```

Dane mogą być wysyłane do APRS-IS, na RF albo obiema drogami.

## APRS Queries

Obsługiwane są standardowe zapytania APRS, między innymi:

```text
?APRS?
?IGATE?
?WX?
```

## Item-In-Message

Program obsługuje mechanizm Item-In-Message, pozwalający przesłać niewielki item wewnątrz wiadomości APRS.

## Satelity

APRSISCE/32 może korzystać z danych TLE, wyliczać pozycję satelity oraz prezentować obszar słyszalności jako MultiLine.

## Duplikaty ramek

Program posiada mechanizmy rozpoznawania duplikatów, co ma duże znaczenie podczas śledzenia stacji mobilnych, gdy ta sama pozycja dociera różnymi drogami.

## Beaconing i GPS

Źródłem własnej pozycji może być stała konfiguracja, GPS, NMEA z portu radiowego, odbiornik USB lub Bluetooth.

```text
NMEA
```

## Windows, Linux i Wine

APRSIS32 jest aplikacją Windows, ale dokumentacja opisuje również uruchamianie jej pod Linuxem za pomocą Wine.

## Aktualny stan projektu

APRSISCE/32 ma długą historię. Wersje Windows Mobile i Windows CE mają dziś przede wszystkim znaczenie historyczne, natomiast APRSIS32 nadal jest dostępny dla współczesnych wersji Windows.

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

Program może być interesujący dla operatora, który chce obserwować lokalny ruch APRS, korzystać z mapy, podłączyć własne radio, prowadzić korespondencję APRS, uruchomić iGate, tworzyć obiekty, obszary i trasy, eksperymentować z Direction Finding, analizować pogodę i alerty oraz poznawać bardziej zaawansowane elementy protokołu APRS.

## Podsumowanie

APRSISCE/32 należy do najbardziej kompletnych klasycznych klientów APRS.

Obsługuje zarówno typowe elementy APRS, jak i funkcje spotykane znacznie rzadziej:

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
