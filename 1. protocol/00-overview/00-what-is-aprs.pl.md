---
title: Czym jest APRS?
description: Wprowadzenie do idei APRS jako radiowego systemu wymiany krótkiej, aktualnej informacji.
template: doc
tableOfContents: true
---

**APRS, Automatic Packet Reporting System**, to krótkofalarski system cyfrowej wymiany informacji w czasie rzeczywistym. Został zaprojektowany tak, aby użytkownicy znajdujący się w danym obszarze mogli automatycznie wymieniać krótkie, aktualne informacje przydatne podczas prowadzenia łączności i działań w terenie.

APRS jest często kojarzony przede wszystkim z lokalizacją stacji na mapie. Jest to jednak tylko jedna z jego funkcji.

Pozycja stacji, pojazdu czy obiektu jest jednym z rodzajów informacji, które APRS może przenosić. System pozwala również przekazywać między innymi statusy, wiadomości, informacje o częstotliwości pracy, obiekty, dane pogodowe, telemetrię, biuletyny, zapytania oraz ostrzeżenia.

Najważniejszą cechą APRS nie jest więc samo raportowanie pozycji, ale **automatyczne udostępnianie aktualnej informacji innym użytkownikom sieci**.

> APRS należy traktować przede wszystkim jako radiowy system wymiany krótkiej, aktualnej informacji, która może pomóc w nawiązaniu łączności, orientacji w sytuacji lub podjęciu działania.

## Nie tylko pozycja

Jednym z najczęstszych nieporozumień dotyczących APRS jest utożsamianie go z systemem śledzenia GPS.

Tracker wysyłający swoją pozycję jest typowym urządzeniem APRS, ale wykorzystuje jedynie część możliwości protokołu.

W tej samej sieci mogą pojawiać się na przykład:

- pozycje stacji stałych i mobilnych,
- informacje o aktualnie monitorowanej częstotliwości,
- status operatora,
- krótkie wiadomości tekstowe,
- wiadomości grupowe i biuletyny,
- przemienniki i inne obiekty znajdujące się w terenie,
- miejsca zbiórek, wydarzeń lub działań terenowych,
- dane telemetryczne,
- dane stacji pogodowych,
- ostrzeżenia pogodowe i inne informacje obszarowe.

Przykładowa stacja mobilna może więc poinformować inne stacje nie tylko o tym, **gdzie się znajduje**, ale również na jakiej częstotliwości operator prowadzi nasłuch albo z jakiego przemiennika aktualnie korzysta.

W praktyce taka informacja może być znacznie bardziej użyteczna niż sama kropka na mapie.

## Informacja o tym, co dzieje się teraz

APRS został zaprojektowany z myślą o informacjach, które mają znaczenie **teraz**.

W przeciwieństwie do klasycznych systemów packet radio nastawionych na zestawienie połączenia pomiędzy dwiema stacjami i przesłanie większej ilości danych, typowy ruch APRS opiera się na krótkich transmisjach rozgłoszeniowych.

Stacja nadaje informację, którą mogą jednocześnie odebrać wszystkie znajdujące się w zasięgu stacje.

Dzięki temu APRS dobrze nadaje się do budowania lokalnej świadomości sytuacyjnej.

Użytkownik może dowiedzieć się na przykład:

- kto znajduje się w jego okolicy,
- gdzie znajdują się inne stacje,
- które stacje są mobilne,
- na jakich częstotliwościach pracują operatorzy,
- jakie przemienniki znajdują się w pobliżu,
- czy w okolicy znajduje się wydarzenie lub aktywność krótkofalarska,
- jakie warunki pogodowe raportują lokalne stacje,
- czy pojawiło się ostrzeżenie,
- jakie obiekty zostały opublikowane przez innych użytkowników sieci.

Nie oznacza to oczywiście, że każdy odbiornik APRS musi prezentować wszystkie te dane. Zakres informacji zależy od możliwości urządzenia lub używanego oprogramowania.

## APRS jest systemem radiowym

APRS może działać całkowicie bez dostępu do Internetu.

Najprostsza wymiana informacji może wyglądać tak:

```text
Stacja A
   |
   | RF
   v
Stacja B
```

Stacja B odbiera bezpośrednio transmisję stacji A i może wykorzystać zawarte w niej informacje.

Jeżeli zasięg bezpośredni jest niewystarczający, w sieci mogą pracować **digipeatery**, które retransmitują wybrane pakiety:

```text
Stacja A
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Stacja B
```

Internet nie jest więc wymagany do działania APRS.

To istotna cecha systemu. Informacja może być wymieniana lokalnie przez radio nawet wtedy, gdy żadna ze stacji nie posiada dostępu do infrastruktury internetowej.

## APRS-IS rozszerza sieć radiową

Współczesny APRS jest często połączony z siecią internetową **APRS-IS**.

Służą do tego stacje nazywane **IGate**, czyli Internet Gateway.

Typowa droga pakietu może wyglądać następująco:

```text
stacja
  |
  | RF
  v
digipeater
  |
  | RF
  v
IGate
  |
  | Internet
  v
APRS-IS
  |
  v
aplikacje i serwisy APRS
```

IGate może przekazać odebraną przez radio informację do APRS-IS. Dzięki temu dane mogą być dostępne dla aplikacji pracujących poza lokalnym zasięgiem radiowym.

Właśnie w ten sposób pakiety odebrane przez lokalną infrastrukturę mogą później pojawić się w internetowych serwisach APRS.

Trzeba jednak zachować właściwą kolejność pojęć:

**APRS-IS jest elementem infrastruktury APRS, ale nie jest samym APRS.**

Podstawowa wymiana informacji nadal może odbywać się bezpośrednio przez radio.

## APRS to nie APRS.fi

Serwisy internetowe takie jak APRS.fi są bardzo użytecznym sposobem prezentowania danych z sieci APRS, ale nie są samą siecią.

Widoczna w serwisie mapa jest jedynie wizualizacją części informacji, które zostały przekazane do APRS-IS.

Pakiet może spełnić swoje zadanie w sieci radiowej i nigdy nie trafić do Internetu.

Przykładowo stacja mobilna może wysłać swoją pozycję i informację o monitorowanej częstotliwości. Pakiet odbierze znajdujący się kilka kilometrów dalej operator, który dzięki temu nawiąże łączność.

Jeżeli w okolicy nie działa żaden IGate, pakiet nie pojawi się w internetowym serwisie.

Nie oznacza to, że APRS nie zadziałał.

Wręcz przeciwnie: informacja dotarła do użytkownika, dla którego była przydatna.

Dlatego:

**pojawienie się stacji na internetowej mapie nie powinno być traktowane jako jedyne kryterium poprawnego działania APRS.**

## Lokalne znaczenie informacji

APRS został zaprojektowany przede wszystkim jako system informacji taktycznej.

W tym kontekście słowo *taktyczny* nie oznacza zastosowań wojskowych. Chodzi o informację przydatną użytkownikowi w jego aktualnej sytuacji i otoczeniu.

Dla operatora poruszającego się samochodem bardziej użyteczna może być informacja:

```text
SP9XYZ
145.550 MHz
```

niż dokładna historia przemieszczania się tej stacji w ciągu ostatnich kilku godzin.

Podobnie obiekt informujący o lokalnym przemienniku, miejscu spotkania, punkcie kontrolnym czy aktywności terenowej ma największą wartość dla osób znajdujących się w jego pobliżu.

Z tego powodu APRS nie powinien być traktowany jako system służący do przesyłania każdej możliwej informacji do każdego użytkownika.

Celem jest dostarczenie **odpowiedniej informacji, odpowiednim użytkownikom, w odpowiednim czasie**.

## Jeden wspólny kanał

Klasyczny APRS na paśmie VHF najczęściej pracuje z szybkością **1200 bodów**.

Jest to kanał radiowy współdzielony przez wszystkie znajdujące się w zasięgu stacje.

Każda transmisja zajmuje fragment dostępnego czasu antenowego. Pakiet może zostać dodatkowo powtórzony przez jeden lub więcej digipeaterów.

Z tego powodu APRS wymaga rozsądnego gospodarowania dostępną przepustowością.

Więcej transmisji nie zawsze oznacza więcej informacji.

Zbyt częste beaconowanie, nadmiernie długie ścieżki digipeaterów lub niepotrzebnie długie pakiety mogą ograniczać możliwość korzystania z kanału przez pozostałych użytkowników.

W dobrze zaprojektowanej sieci transmisja powinna odbywać się **tak często, jak jest to potrzebne, ale nie częściej**.

Szczegółowe zasady dotyczące ścieżek, digipeaterów, kolizji i wykorzystania kanału opisane są w dalszych częściach dokumentacji.

## Broadcast zamiast klasycznego połączenia

Większość transmisji APRS wykorzystuje ramki AX.25 typu UI, czyli *Unnumbered Information*.

Nie jest więc wcześniej zestawiane klasyczne połączenie pomiędzy nadawcą i odbiorcą.

Stacja nadaje pakiet, a wszystkie stacje znajdujące się w zasięgu mogą go odebrać.

Takie podejście bardzo dobrze odpowiada charakterowi APRS.

Informacja:

```text
jestem tutaj
```

```text
nasłuchuję na tej częstotliwości
```

```text
w tym miejscu znajduje się przemiennik
```

```text
pojawiło się ostrzeżenie pogodowe
```

może być interesująca jednocześnie dla wielu użytkowników.

Nie oznacza to jednak, że APRS nie obsługuje komunikacji skierowanej do konkretnej stacji.

Protokół posiada system krótkich wiadomości tekstowych oraz mechanizmy potwierdzania ich odbioru. Są one jednak zbudowane ponad rozgłoszeniowym charakterem podstawowej sieci.

## Co może być stacją APRS?

Węzłem APRS nie musi być wyłącznie samochodowy tracker GPS.

W sieci mogą pracować między innymi:

- radiotelefony ręczne,
- radiotelefony mobilne,
- stacje bazowe,
- trackery,
- komputery z modemem programowym,
- digipeatery,
- IGate,
- stacje pogodowe,
- urządzenia telemetryczne,
- balony,
- stacje przenośne,
- specjalizowane urządzenia APRS.

Część urządzeń jedynie nadaje dane. Inne potrafią je również odbierać, analizować i prezentować operatorowi.

Najpełniejszy model wykorzystania APRS powstaje wtedy, gdy operator nie tylko wysyła własną pozycję, ale również korzysta z informacji generowanych przez pozostałych użytkowników sieci.

## Krótka historia

APRS został opracowany przez **Boba Bruningę, WB4APR**.

Korzenie systemu sięgają lat 80. XX wieku. Początkowo jednym z jego podstawowych zastosowań było prezentowanie położenia i statusu uczestników działań terenowych.

Pierwotnie skrót APRS rozwijano jako:

**Automatic Position Reporting System**

Wraz z rozwojem systemu coraz wyraźniejsze stawało się jednak, że pozycja jest tylko jednym z wielu rodzajów przekazywanej informacji.

Nazwa zaczęła więc być rozwijana jako:

**Automatic Packet Reporting System**

Zmiana bardzo dobrze oddaje sposób, w jaki należy rozumieć APRS.

Nie jest to wyłącznie system raportowania pozycji.

Jest to system raportowania **informacji**.

## APRS jako narzędzie wspomagające łączność

Jednym z najciekawszych zastosowań APRS jest wspomaganie tradycyjnej łączności głosowej.

Stacja może wraz z pozycją przekazywać informację o aktualnie monitorowanej częstotliwości.

Operator znajdujący się w pobliżu widzi wtedy nie tylko znak stacji, ale również informację pozwalającą bezpośrednio rozpocząć łączność.

W podobny sposób APRS może informować o lokalnych przemiennikach, wydarzeniach, aktywnościach czy innych stacjach znajdujących się w okolicy.

W tym sensie APRS nie jest systemem istniejącym obok klasycznej łączności krótkofalarskiej.

Może być warstwą informacyjną, która pomaga tę łączność rozpocząć.

## Jak myśleć o APRS?

Najprościej potraktować APRS jako wspólny radiowy kanał krótkich komunikatów o aktualnej sytuacji.

Każda stacja może dołożyć do tego obrazu niewielki fragment informacji:

```text
Jestem tutaj.
```

```text
Nasłuchuję tutaj.
```

```text
Tutaj znajduje się przemiennik.
```

```text
Tutaj odbywa się wydarzenie.
```

```text
Takie są aktualne warunki pogodowe.
```

```text
To urządzenie ma taki stan.
```

```text
Dla tego obszaru obowiązuje ostrzeżenie.
```

Połączenie tych informacji tworzy aktualny obraz tego, co dzieje się w lokalnej sieci.

I właśnie to jest istotą APRS.

## Najważniejsze do zapamiętania

**APRS nie jest wyłącznie systemem śledzenia pozycji.**

Pozycja jest tylko jednym z wielu rodzajów danych.

**APRS może działać bez Internetu.**

Podstawowym medium może być lokalna sieć radiowa.

**APRS-IS rozszerza możliwości systemu, ale nie jest jego istotą.**

Internetowe serwisy wykorzystują informacje pochodzące z sieci APRS.

**APRS.fi nie jest APRS.**

Jest jednym ze sposobów prezentowania danych z APRS-IS.

**APRS służy do przekazywania krótkich, aktualnych informacji.**

Ich wartość wynika przede wszystkim z tego, czy są użyteczne dla innych użytkowników sieci.

**APRS może wspomagać tradycyjną łączność radiową.**

Informacja o pozycji, statusie i monitorowanej częstotliwości może bezpośrednio pomóc w nawiązaniu kontaktu.

## Dalej

Aby dokładniej zrozumieć sposób działania systemu, warto następnie poznać:

- drogę pakietu APRS pomiędzy stacjami,
- zależność pomiędzy APRS i AX.25,
- strukturę pakietu APRS,
- podstawowe typy danych APRS,
- rolę digipeaterów,
- rolę IGate i APRS-IS,
- ograniczenia radiowego kanału APRS.

Te zagadnienia opisują kolejne artykuły działu **Przegląd protokołu APRS**.

## Źródła i dalsza lektura

Przy opracowaniu artykułu wykorzystano przede wszystkim:

- materiały i dokumentację APRS opracowaną przez Boba Bruningę, WB4APR,
- *APRS Protocol Reference*,
- dokumentację projektu APRS Specification opracowywaną przez Johna Langnera, WB2OSZ,
- *What is APRS?*,
- *Understanding APRS Packets*,
- materiały dotyczące współczesnej specyfikacji APRS 1.2.

Materiały referencyjne używane w projekcie APRSBox znajdują się również w katalogu `APRS-SPEC` repozytorium:

`https://github.com/SQ9MDD/APRSBox/tree/main/APRS-SPEC`
