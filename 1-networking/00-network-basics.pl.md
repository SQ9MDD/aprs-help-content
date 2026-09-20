---
title: Rola i elementy sieci APRS, podstawy działania
description: Ogólny zarys budowy sieci APRS, ról jej elementów, przepływu informacji oraz zasad pracy urządzeń pośredniczących.
template: doc
tableOfContents: true
---

APRS tworzy rozproszoną sieć wymiany informacji. Dane mogą być odbierane bezpośrednio drogą radiową, retransmitowane przez digipeatery oraz przekazywane pomiędzy częścią radiową i APRS-IS.

Sieć nie wymaga centralnego elementu pośredniczącego. Dwie stacje znajdujące się w swoim zasięgu mogą wymieniać informacje bezpośrednio. Digipeatery rozszerzają użyteczny zasięg sieci radiowej, a iGate łączą segmenty RF z APRS-IS.

Poszczególne elementy sieci realizują różne funkcje. Jedno urządzenie może wykonywać kilka z nich jednocześnie, jednak każda rola ma własny zakres działania.

## Elementy sieci APRS

Podstawowe role występujące w sieci APRS to:

- stacja końcowa,
- digipeater,
- iGate,
- APRS-IS,
- aplikacje i usługi korzystające z danych APRS.

Role te opisują funkcję wykonywaną w sieci, a nie konkretny typ urządzenia.

Jedna instalacja może na przykład generować własne pakiety APRS, retransmitować ruch jako digipeater i przekazywać odebrane pakiety do APRS-IS jako iGate. Funkcje te pozostają jednak odrębne.

## Stacja końcowa

Stacja końcowa jest źródłem lub odbiorcą informacji APRS.

Może generować między innymi informacje o pozycji, statusie, pogodzie, telemetrii, obiektach lub wiadomościach. Może również odbierać i interpretować dane generowane przez inne stacje.

Komunikacja nie musi przebiegać przez infrastrukturę pośredniczącą. Jeżeli dwie stacje znajdują się w swoim bezpośrednim zasięgu radiowym, pakiet może zostać odebrany bez udziału digipeatera, iGate lub APRS-IS.

## Digipeater

Digipeater jest elementem radiowej części sieci APRS. Jego zadaniem jest retransmisja pakietów zgodnie z mechanizmem ścieżki APRS.

Dzięki temu pakiet może dotrzeć poza bezpośredni zasięg radiowy stacji źródłowej.

Podczas retransmisji digipeater wykonuje zmiany przewidziane dla ścieżki pakietu. Nie może wykorzystywać retransmisji do dowolnej zmiany pozostałych danych pakietu. Dane źródłowe pozostają danymi stacji, która pakiet wygenerowała.

Jeżeli urządzenie pracujące jako digipeater generuje własny beacon, status lub inny pakiet APRS, jest to nowy pakiet pochodzący od tej stacji, a nie uzupełnienie treści pakietu innego nadawcy.

Szczegóły działania ścieżek i algorytmów digipeatingu są opisane oddzielnie.

## iGate

iGate jest bramą pomiędzy radiową częścią sieci APRS i APRS-IS.

Podstawowym kierunkiem pracy jest:

```text
RF -> APRS-IS
```

iGate może również realizować kontrolowane przekazywanie określonego ruchu w kierunku:

```text
APRS-IS -> RF
```

Oba kierunki podlegają odrębnym regułom.

Podobnie jak digipeater, iGate nie może dowolnie zmieniać danych przekazywanego pakietu. Informacje związane z wprowadzeniem pakietu do APRS-IS są umieszczane w przeznaczonej do tego części ścieżki, między innymi z użyciem `q` construct. Nie są dopisywane do danych źródłowych stacji.

Szczegółowe zasady gatingu, `q` constructs oraz ruchu APRS-IS -> RF są opisane oddzielnie.

## APRS-IS

APRS-IS jest internetową częścią infrastruktury APRS. Łączy serwery, klientów, aplikacje i iGate, umożliwiając dystrybucję pakietów poza lokalny zasięg radiowy.

Pakiet odebrany lokalnie na RF może zostać wprowadzony przez iGate do APRS-IS i udostępniony systemom znajdującym się w innych lokalizacjach.

APRS-IS nie jest odrębnym systemem niezwiązanym z radiową częścią APRS. Jego działanie jest powiązane z ruchem RF, a dane pochodzące z APRS-IS mogą w określonych przypadkach ponownie trafić na radio.

## Aplikacje i usługi

Dane APRS mogą być odbierane i wykorzystywane przez aplikacje oraz wyspecjalizowane usługi.

Mogą one między innymi:

- prezentować bieżący stan sieci,
- wyświetlać pozycje i obiekty,
- obsługiwać wiadomości,
- rejestrować dane,
- przetwarzać pogodę i telemetrię,
- realizować inne funkcje oparte na danych APRS.

Nie każda aplikacja korzystająca z APRS-IS jest elementem infrastruktury transportującej pakiety. Należy odróżnić system korzystający z danych od digipeatera, iGate lub serwera APRS-IS uczestniczącego bezpośrednio w ich dystrybucji.

## Przepływ informacji

Najprostsza komunikacja odbywa się bezpośrednio:

```text
stacja A -> RF -> stacja B
```

Z użyciem digipeatera:

```text
stacja A -> RF -> digipeater -> RF -> stacja B
```

Pakiet odebrany przez iGate może zostać przekazany do APRS-IS:

```text
stacja A -> RF -> iGate -> APRS-IS
```

W APRS-IS ten sam pakiet może być dostępny dla wielu klientów i usług:

```text
                         -> klient
                        /
RF -> iGate -> APRS-IS ---> usługa
                        \
                         -> inny system APRS
```

W określonych przypadkach ruch może również zostać przekazany z APRS-IS na RF:

```text
APRS-IS -> iGate -> RF -> stacja
```

Nie każdy pakiet przechodzi przez wszystkie elementy sieci. Są to różne możliwe drogi dystrybucji informacji.

## Źródło informacji i elementy pośredniczące

Należy rozróżnić źródło informacji od elementów uczestniczących w jej przekazywaniu.

Pakiet może przejść przez digipeater, iGate i APRS-IS, ale elementy te nie stają się przez to źródłem danych zawartych w pakiecie.

Informacje potrzebne do działania sieci, na przykład związane z wykorzystaną ścieżką radiową lub punktem wejścia do APRS-IS, mają własne mechanizmy i przeznaczone do tego miejsca.

Element pośredniczący wykonuje wyłącznie zmiany wynikające z realizowanej funkcji. Nie należy przenosić jego własnych informacji technicznych do danych źródłowych innej stacji.

## Przekazywanie i generowanie pakietów

Przekazanie istniejącego pakietu i wygenerowanie nowego pakietu to dwie różne operacje.

```text
przekazanie istniejącego pakietu
```

nie jest tym samym co:

```text
wygenerowanie własnego pakietu
```

Digipeater retransmitujący pakiet lub iGate przekazujący go do APRS-IS działa jako element pośredniczący.

Jeżeli urządzenie generuje własną informację APRS, powstaje nowy pakiet pochodzący od tego urządzenia.

Rozróżnienie pozostaje istotne również wtedy, gdy kilka funkcji realizuje ten sam program, komputer lub urządzenie radiowe.

## Role i granice działania

Stacja końcowa, digipeater, iGate i APRS-IS wykonują różne funkcje.

Stacja końcowa generuje lub odbiera informacje APRS.

Digipeater retransmituje pakiety w sieci radiowej i wykonuje zmiany przewidziane dla ścieżki retransmisji.

iGate przekazuje pakiety pomiędzy RF i APRS-IS według reguł określonych dla tej funkcji. Informacje dotyczące transportu w APRS-IS są zapisywane w przeznaczonych do tego elementach ścieżki, między innymi przy użyciu `q` constructs.

APRS-IS dystrybuuje pakiety w internetowej części systemu.

Fakt, że jedno urządzenie może technicznie realizować kilka funkcji, nie łączy ich zasad działania. Dla każdego pakietu znaczenie ma rola, w której urządzenie go przetwarza.

W szczególności infrastruktura pośrednicząca nie może dowolnie zmieniać danych pochodzących od stacji źródłowej. Zmiany wykonywane podczas przekazywania pakietu są ograniczone do pól i mechanizmów przewidzianych dla danej funkcji sieciowej.

## Współdzielony kanał radiowy

Radiowa część APRS wykorzystuje współdzielone medium o ograniczonej przepustowości.

Każda transmisja zajmuje kanał i wpływa na możliwość nadawania oraz odbioru przez pozostałe stacje. Dotyczy to pakietów generowanych przez stacje, retransmisji wykonywanych przez digipeatery oraz ruchu wprowadzanego na RF przez iGate.

Konfiguracja poszczególnych elementów wpływa więc nie tylko na zasięg pojedynczego pakietu, ale również na działanie całej lokalnej sieci.

Szczegółowe mechanizmy ograniczania ruchu i zasady projektowania infrastruktury są opisane w dalszych artykułach działu **Sieć**.

## Sieć APRS jako całość

Jedną z możliwych dróg pakietu można przedstawić następująco:

```text
                     +-> stacja
                     |
stacja -> digipeater +-> iGate -> APRS-IS -> aplikacje i usługi
                     |
                     +-> inne stacje RF
```

Nie jest to wymagana topologia.

APRS może działać lokalnie wyłącznie na RF, może korzystać z jednego lub wielu digipeaterów, może być połączony z APRS-IS albo wykorzystywać kilka tych mechanizmów jednocześnie.

Podstawowy podział funkcji pozostaje taki:

- stacja tworzy lub odbiera informację,
- digipeater rozszerza jej dystrybucję na RF,
- iGate łączy RF z APRS-IS,
- APRS-IS rozpowszechnia informację w części internetowej,
- aplikacje i usługi wykorzystują dostępne dane.

Każdy z tych elementów wykonuje operacje przewidziane dla swojej roli. Szczegółowe zasady działania digipeaterów, iGate i APRS-IS są rozwinięte w kolejnych częściach działu **Sieć**.

## Źródła

- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
- [APRS-IS](https://www.aprs-is.net/)
- [APRS-IS IGate Details](https://www.aprs-is.net/IGateDetails.aspx)
