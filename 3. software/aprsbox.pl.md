---
title: APRSBox
description: Nowoczesna konsola APRS dla Linux i Raspberry Pi, łącząca Packet Routing, DIGI, iGate, APRS-IS, wiadomości, mapę, ostrzeżenia, statystyki i analizę propagacji.
template: doc
tableOfContents: true
---

**APRSBox** to otwartoźródłowe oprogramowanie do budowy kompletnej stacji APRS pracującej pod systemem Linux. Projekt rozwijany jest przez **SQ9MDD** i udostępniany na licencji **GPL-3.0**.

Nie jest to wyłącznie program typu iGate albo digipeater. APRSBox łączy w jednej aplikacji funkcje stacji domowej APRS, digipeatera, iGate, klienta APRS-IS, monitora ruchu, systemu wiadomości, mapy, ostrzeżeń, statystyk i narzędzi diagnostycznych.

Projekt jest przygotowany przede wszystkim do pracy całodobowej na niewielkich komputerach, takich jak Raspberry Pi, ale może działać również na innych systemach Linux.

Oficjalne repozytorium:

https://github.com/SQ9MDD/APRSBox

## Charakter projektu

APRSBox można traktować jako współczesną, zarządzaną przez przeglądarkę konsolę APRS. Jedna instalacja może realizować jednocześnie funkcje, które w klasycznej stacji często wymagają kilku niezależnych programów.

APRSBox może pracować między innymi jako:

- domowa stacja APRS,
- digipeater,
- fill-in digipeater,
- RX iGate,
- kontrolowany TX iGate,
- messaging iGate,
- klient APRS-IS,
- stacja pogodowa,
- monitor lokalnego kanału APRS,
- terminal ostrzeżeń,
- analizator propagacji,
- węzeł z wieloma interfejsami radiowymi.

APRSBox nie zastępuje modemu radiowego. Do komunikacji z radiem korzysta z zewnętrznego TNC lub modemu udostępniającego KISS, na przykład Dire Wolf, VP-Digi albo innego zgodnego urządzenia.

## Architektura

Aplikacja jest napisana w Pythonie i wykorzystuje FastAPI.

System jest rozdzielony na dwa podstawowe procesy:

- `app.main` - interfejs WWW, konfiguracja i administracja,
- `app.core_main` - proces APRS Core odpowiedzialny za pracę runtime.

APRS Core zajmuje się między innymi odbiorem i nadawaniem ramek, dekodowaniem AX.25 i APRS, Packet Routing, DIGI, APRS-IS, wiadomościami, schedulerami, obiektami, biuletynami, pogodą, statystykami i innymi zadaniami czasu rzeczywistego.

Konfiguracja i dane operacyjne przechowywane są w SQLite. Rozdzielenie GUI od APRS Core ogranicza wpływ obsługi strony WWW na krytyczną ścieżkę przetwarzania ramek.

## Interfejsy i źródła danych

APRSBox może korzystać równocześnie z wielu interfejsów.

Obsługiwane są między innymi:

- KISS TCP,
- KISS Serial,
- APRS-IS,
- OpenWebRX przez MQTT.

Jedna instalacja może więc odbierać dane z kilku źródeł, używać kilku nadajników i kierować ruch pomiędzy nimi zgodnie z regułami Packet Routing.

## KISS TCP i KISS Serial

KISS TCP pozwala współpracować z modemami i TNC udostępniającymi KISS przez sieć IP. Typowym przykładem jest Dire Wolf, VP-Digi albo zdalny sprzętowy TNC.

KISS Serial pozwala podłączyć TNC bezpośrednio przez port szeregowy. Konfiguracja obejmuje ścieżkę urządzenia i prędkość portu, a runtime obsługuje mechanizmy potrzebne do stabilnej pracy ciągłej.

Interfejs KISS może być używany zarówno jako źródło RX, jak i cel TX.

## OpenWebRX MQTT

APRSBox posiada interfejs odbiorczy wykorzystujący MQTT z OpenWebRX.

Poza klasycznymi ramkami APRS może odbierać dane z innych dekoderów dostępnych w OpenWebRX, w tym:

- SONDE,
- ADS-B.

Dane radiosond i ADS-B mogą być reprezentowane w systemie jako obiekty APRS i wyświetlane w interfejsie.

OpenWebRX MQTT jest źródłem odbiorczym i nie jest bezpośrednim nadajnikiem RF.

## APRS-IS

APRS-IS jest pełnoprawnym elementem architektury APRSBox.

Konfiguracja obejmuje między innymi:

- serwer,
- port,
- login,
- passcode,
- filtr APRS-IS,
- diagnostykę połączenia.

APRS-IS może być źródłem odbieranych danych oraz celem Packet Routing.

## Packet Routing

Jednym z najważniejszych elementów APRSBox jest **Packet Routing**.

Administrator tworzy przepływy według modelu:

```text
źródło -> filtry -> akcja / cel
```

Pozwala to realizować między innymi:

```text
RF -> RF
RF -> APRS-IS
APRS-IS -> RF
Local TX -> APRS-IS
```

Odpowiada to odpowiednio pracy DIGI, RX iGate, kontrolowanego TX iGate oraz przekazywaniu lokalnie generowanych ramek do APRS-IS.

Reguła może także zakończyć się zapisaniem zdarzenia do logu albo świadomym odrzuceniem ramki.

## Filtry DIGI i Packet Routing

Przepływy mogą korzystać z wielu kolejnych filtrów.

Dostępne są mechanizmy związane między innymi z:

- wykrywaniem duplikatów,
- viscous delay,
- analizą ścieżki,
- trybem strict,
- odbiorem direct-only,
- ochroną DIGI,
- znakiem wywoławczym,
- typem pakietu,
- symbolem APRS,
- odległością,
- rate limitingiem.

Kolejność wybranych filtrów jest kontrolowana, aby ograniczyć możliwość stworzenia konfiguracji powodującej błędną lub nadmierną retransmisję.

## Duplicate Filter i Viscous Delay

APRSBox posiada mechanizm wykrywania duplikatów wykorzystywany podczas pracy DIGI.

Może on współpracować z **viscous delay**, czyli kontrolowanym opóźnieniem retransmisji. Fill-in digipeater może poczekać przed nadaniem i zrezygnować z retransmisji, jeżeli w tym czasie usłyszy tę samą ramkę powtórzoną przez inną stację.

Zmniejsza to liczbę zbędnych transmisji i zajętość kanału.

## Ochrona DIGI i świeżość ramek

APRSBox zawiera zabezpieczenia przed retransmisją ramek, których digipeater nie powinien powtarzać.

Kontrolowane mogą być między innymi:

- nieprawidłowe elementy ścieżki,
- ramki już powtórzone przez lokalną stację,
- pakiety third-party,
- lokalne wiadomości i zapytania APRS,
- sytuacje, w których dalsza retransmisja nie powinna nastąpić.

System pilnuje również wieku ramek oczekujących w kolejkach. Pakiet, który z powodu przeciążenia albo problemu transportowego czekał zbyt długo, może zostać odrzucony zamiast wysłany jako nieaktualna ramka.

## RX iGate i TX iGate

W kierunku **RF -> APRS-IS** APRSBox może pracować jako klasyczny RX iGate. Ścieżka ta została zaprojektowana z naciskiem na małe opóźnienie.

W kierunku **APRS-IS -> RF** działa kontrolowany TX iGate. Szczególne znaczenie ma obsługa wiadomości APRS i informacja, czy adresat był słyszany lokalnie.

APRS-IS -> RF nie jest bezwarunkowym forwardingiem całego ruchu internetowego. Reguły i filtry mają chronić lokalny kanał radiowy przed niepotrzebnym obciążeniem.

## Local TX i Internal TX

Ramki generowane przez APRSBox są traktowane jako logiczne źródło **Local TX**.

Dotyczy to między innymi:

- beaconu,
- statusu,
- WX,
- obiektów,
- itemów,
- biuletynów,
- wiadomości.

Local TX może być kierowany do APRS-IS niezależnie od fizycznej transmisji RF.

Dostępny jest także logiczny **Internal TX**, który nie wykonuje fizycznej transmisji, ale pozwala wprowadzić lokalnie wygenerowaną ramkę do dalszego routingu.

## Własna stacja, beacon i Proportional Pathing

APRSBox może generować pozycję własnej stacji.

Konfigurowane są między innymi:

- znak i SSID,
- pozycja,
- symbol APRS,
- komentarz,
- ścieżka,
- interwał beaconu,
- docelowy interfejs TX.

Beacon można również wysłać ręcznie.

Dostępny jest **Proportional Pathing**, który pozwala częściej wysyłać ramki bezpośrednio i rzadziej używać szerszych ścieżek DIGI, na przykład według schematu:

```text
DIRECT -> krótka ścieżka -> pełna ścieżka
```

Pozwala to ograniczyć niepotrzebne obciążenie kanału.

## Status APRS

Status APRS może być wysyłany okresowo niezależnie od beaconu pozycji.

Pozwala to publikować dodatkowe informacje o stacji bez umieszczania ich w każdej ramce pozycyjnej.

## Wiadomości APRS

APRSBox posiada interfejs rozmów APRS.

Obsługiwane są między innymi:

- wiadomości numerowane,
- wiadomości bez numeru,
- ACK,
- REJ,
- retry wiadomości oczekujących na ACK,
- eliminowanie duplikatów,
- wiadomości odebrane przez RF i APRS-IS,
- rozmowy z pojedynczymi stacjami,
- grupy wiadomości.

Obsługiwane są również alfanumeryczne identyfikatory wiadomości APRS.

Powtórnie odebrana numerowana wiadomość nie musi tworzyć kolejnego wpisu w rozmowie, ale może ponownie otrzymać ACK.

## Wiadomości grupowe i APRS Queries

Można zdefiniować grupy APRS, których aplikacja ma słuchać, na przykład:

- `ALL`,
- `QST`,
- `CQ`.

Grupy RF i APRS-IS mogą być konfigurowane niezależnie.

APRSBox potrafi również reagować na wybrane standardowe zapytania APRS, między innymi:

- `?APRS`,
- `?APRSD`,
- `?DX`.

Pozwala to udostępniać podstawowe informacje o stacji i lokalnej aktywności przez sam protokół APRS.

## Obiekty, itemy, biuletyny i ogłoszenia

APRSBox może zarządzać obiektami i itemami APRS.

Możliwe jest między innymi:

- ustawienie pozycji i symbolu,
- komentarz,
- ręczne wysłanie,
- transmisja okresowa,
- ustawienie czasu ważności,
- zakończenie transmisji po wygaśnięciu,
- wysłanie informacji o usunięciu obiektu.

Dostępny jest również scheduler biuletynów i ogłoszeń. Transmisje mogą być rozkładane w czasie, aby wiele aktywnych elementów nie tworzyło jednego gwałtownego burstu na kanale.

## Pogoda

APRSBox potrafi generować i przetwarzać dane pogodowe APRS.

Dane WX są dekodowane i wykorzystywane w widokach stacji, na mapie i w filtrach.

Parser obsługuje klasyczne pola pogodowe oraz dodatkowe rozszerzenia wykorzystywane w APRS.

## Traffic Monitor

Traffic Monitor pokazuje na żywo ruch przechodzący przez APRSBox.

Może rozróżniać między innymi:

- RX,
- TX,
- ruch z różnych interfejsów,
- RF -> APRS-IS,
- APRS-IS -> RF,
- ramki generowane lokalnie.

Dostępne są filtry po kierunku, interfejsie i treści ramki, dlatego widok może służyć jako analizator lokalnego ruchu APRS.

## Lista stacji

Odebrane ramki są przetwarzane do aktualnego stanu stacji.

Widok może prezentować między innymi:

- znak,
- ostatnią aktywność,
- pozycję,
- dystans,
- symbol,
- źródło ramki,
- dane WX,
- prędkość i kurs,
- informacje Mic-E,
- rozpoznane urządzenie albo oprogramowanie.

Stacje można filtrować według typu i źródła.

## Mapa

APRSBox posiada rozbudowaną mapę opartą o Leaflet.

Na mapie mogą być wyświetlane:

- stacje,
- obiekty i itemy,
- ślady stacji mobilnych,
- pokrycie PHG,
- siatka Maidenhead,
- obszary NWS-WARN,
- obszary CAWF,
- PL-WARN,
- ES-WARN.

Widok może być filtrowany według interfejsów źródłowych.

## PHG, ślady i nakładające się stacje

Jeżeli stacja wysyła PHG, APRSBox może pokazać przybliżony obszar jej pokrycia radiowego.

Dla stacji mobilnych mogą być prezentowane ślady ruchu.

W miejscach, gdzie wiele stacji znajduje się na tej samej albo bardzo zbliżonej pozycji, APRSBox może grupować markery, a przy odpowiednim powiększeniu rozsuwać nakładające się symbole, aby możliwy był dostęp do każdej stacji.

## Siatka Maidenhead

Mapa może wyświetlać siatkę lokatorów Maidenhead.

Poziom szczegółowości zmienia się wraz z powiększeniem, dzięki czemu warstwa jest przydatna zarówno do ogólnej orientacji, jak i dokładniejszej pracy operatorskiej.

## APRS Emergency

APRSBox wykrywa ramki związane z **APRS Emergency** i prezentuje je w osobnej części interfejsu.

System może:

- grupować alarmy według pełnego znaku źródłowego,
- przechowywać historię powiązanych ramek,
- pokazywać liczbę kolejnych zdarzeń,
- wyświetlać globalne powiadomienia,
- odtwarzać sygnał dźwiękowy,
- pozwalać na czasowe lub bezterminowe wyciszenie konkretnego alarmu.

Usunięcie alarmu z listy nie usuwa koniecznie źródłowych ramek z Traffic Monitor, dlatego przebieg zdarzenia może nadal zostać przeanalizowany.

## NWS-WARN

APRSBox obsługuje **NWS-WARN**, mechanizm ostrzeżeń obszarowych używany w amerykańskim ekosystemie APRS i związany z komunikatami National Weather Service.

Ostrzeżenie może zawierać rodzaj zagrożenia, czas obowiązywania oraz informację o obszarze objętym alertem.

APRSBox może wykorzystać te dane do wizualizacji ostrzeżenia na mapie, zamiast ograniczać prezentację do samego tekstu komunikatu.

Obsługa NWS-WARN zapewnia zgodność z istniejącym ekosystemem APRS oraz aplikacjami, które od lat wykorzystują ten mechanizm.

## CAWF, Common APRS Warning Format

APRSBox obsługuje również **CAWF, Common APRS Warning Format**.

CAWF jest formatem przeznaczonym do dystrybucji ostrzeżeń obszarowych przez APRS w sposób niezależny od konkretnego krajowego systemu źródłowego.

Format pozwala przekazywać w ujednolicony sposób między innymi:

- rodzaj zagrożenia,
- poziom zagrożenia,
- obszar obowiązywania,
- czas obowiązywania,
- identyfikator ostrzeżenia,
- źródło danych.

Dane z krajowych systemów ostrzegania mogą zostać znormalizowane przez serwer warnHUB, przesłane przez APRS i jednakowo zinterpretowane przez klienta zgodnego z CAWF.

## PL-WARN

W Polsce CAWF jest wykorzystywany przez system **PL-WARN**.

warnHUB pobiera oficjalne ostrzeżenia, normalizuje je do CAWF i przekazuje do sieci APRS.

APRSBox posiada zaimplementowane obszary administracyjne Polski i potrafi powiązać identyfikator obszaru z odpowiednią geometrią na mapie.

Dzięki temu ostrzeżenie PL-WARN może zostać przedstawione jako zaznaczony obszar geograficzny, a nie wyłącznie tekst wiadomości.

## ES-WARN

Analogiczny mechanizm jest obsługiwany dla Hiszpanii przez **ES-WARN**.

APRSBox posiada zaimplementowane obszary Hiszpanii potrzebne do wizualizacji ostrzeżeń CAWF.

PL-WARN i ES-WARN korzystają z tego samego modelu CAWF, dlatego aplikacja może prezentować ostrzeżenia z różnych krajów w spójny sposób.

## Ostrzeżenia obszarowe na mapie

NWS-WARN i CAWF mogą być przedstawiane jako warstwy mapy.

Użytkownik może w jednym widoku obserwować:

- stacje APRS,
- obiekty,
- ślady stacji mobilnych,
- lokalną aktywność,
- obszary objęte ostrzeżeniami.

Dla CAWF APRSBox posiada obecnie zaimplementowane obszary Polski i Hiszpanii, wykorzystywane odpowiednio przez PL-WARN i ES-WARN.

Dzięki temu APRSBox może pełnić rolę lokalnego terminala informacji ostrzegawczej odbieranej przez APRS-IS lub inne skonfigurowane źródła.

## Radar stacji, powiadomienia i webhooki

APRSBox posiada funkcję radaru stacji.

Można obserwować wybrane znaki, także z użyciem masek, i reagować na ich pojawienie się w określonym zasięgu.

Wybrane zdarzenia mogą być przekazywane poza GUI poprzez mechanizmy powiadomień i integracje, w tym webhooki oraz Telegram.

## Warunki pasma

APRSBox posiada funkcję automatycznej analizy warunków propagacyjnych na podstawie lokalnego ruchu APRS.

System buduje lokalną bazę odniesienia pokazującą, jakie stacje i odległości są zwykle słyszane z danej lokalizacji. Nietypowe pojawienie się dalszych stacji lub nowych obszarów geograficznych może wskazywać na poprawę propagacji.

Analiza może działać niezależnie dla poszczególnych interfejsów.

## Skala W0-W5 i historia propagacji

Warunki są prezentowane w uproszczonej skali **W0-W5**.

Ocena może uwzględniać między innymi:

- liczbę słyszanych stacji,
- typowy lokalny zasięg,
- odległości,
- powtarzalność dalekich odbiorów,
- pojawienie się nowych obszarów,
- dojrzałość zgromadzonych danych.

Model nie opiera się wyłącznie na pojedynczej najdalszej stacji.

APRSBox przechowuje także historię ocen propagacji oraz dane diagnostyczne pomagające zrozumieć podstawę aktualnej oceny.

## Statystyki i TOP

APRSBox prowadzi statystyki ruchu i aktywności.

Dostępne są między innymi dane dotyczące:

- typów ramek APRS,
- ruchu direct i całkowitego,
- kierunków routingu,
- aktywności radiowej,
- najaktywniejszych użytkowników,
- rozpoznanych urządzeń i aplikacji.

System może prezentować TOP użytkowników według `CALLSIGN-SSID` oraz TOP urządzeń rozpoznanych między innymi na podstawie TOCALL i Mic-E.

## Dashboard i diagnostyka

Dashboard pokazuje bieżący stan całej instalacji.

Widoczne mogą być między innymi:

- aktywność RF,
- ruch,
- stan usług,
- interfejsy,
- APRS-IS,
- skonfigurowane kierunki routingu,
- własna stacja.

Packet Routing posiada diagnostykę krok po kroku. Można sprawdzić, która reguła została uruchomiona, które filtry wykonano, gdzie pakiet został odrzucony, czy trafił do TX oraz jakie były czasy wykonania, kolejki i workerów.

## Wielointerfejsowość, kolejki i pacing

APRSBox nie zakłada, że stacja posiada tylko jeden modem.

Jedna instalacja może mieć wiele interfejsów KISS, a Packet Routing określa, który odbiornik może przekazywać ruch do którego nadajnika.

Kolejki TX są rozdzielone per interfejs, dlatego wolny albo problematyczny TNC nie musi blokować pozostałych.

APRSBox kontroluje także odstępy pomiędzy lokalnie generowanymi ramkami. Beacon, status, WX, obiekty, biuletyny i wiadomości mogą być rozkładane w czasie, aby nie tworzyć jednego gwałtownego burstu.

## Parser APRS

APRSBox posiada własną warstwę dekodowania danych APRS wykorzystywaną przez mapę, widoki stacji, wiadomości, pogodę i statystyki.

Obsługiwane są między innymi:

- klasyczne pozycje APRS,
- pozycje skompresowane,
- Mic-E,
- position ambiguity,
- obiekty,
- itemy,
- wiadomości,
- ACK i REJ,
- query,
- status,
- telemetria,
- pogoda,
- PHG.

## Symbole i interfejs WWW

Interfejs obsługuje symbole z podstawowej i alternatywnej tablicy APRS oraz ich opisy.

GUI posiada między innymi:

- jasny i ciemny motyw,
- responsywną nawigację,
- pomoc kontekstową,
- widoki diagnostyczne,
- dynamicznie aktualizowany ruch i stan stacji.

Interfejs jest tłumaczony między innymi na polski, angielski, hiszpański i niemiecki.

## Backup, aktualizacja, SQLite i HTTPS

Konfigurację APRSBox można eksportować i odtwarzać.

Instalacja natywna posiada mechanizm aktualizacji przez GUI, a przed aktualizacją może zostać wykonana kopia bazy.

Dostępna jest diagnostyka SQLite, obejmująca między innymi rozmiar bazy, WAL, liczbę stron i kontrolę spójności.

Dane runtime można czyścić bez usuwania całej konfiguracji.

Instalacja natywna może również zostać skonfigurowana do pracy przez HTTPS z własnym certyfikatem i kluczem prywatnym.

## Instalacja i Docker

Projekt posiada skrypty instalacyjne dla:

- Debian,
- Raspberry Pi OS i innych systemów zgodnych z Debianem,
- Alpine Linux.

Obsługiwane są zarówno `systemd`, jak i `OpenRC`.

APRSBox może również pracować w Dockerze. Dane i logi mogą być przechowywane w wolumenach. W kontenerze część operacji bezpośrednio zarządzających hostem jest wyłączona, a aktualizacja polega na wymianie obrazu przy zachowaniu danych.

## Raspberry Pi i słabszy sprzęt

Projekt rozwijany jest z myślą o niewielkich komputerach pracujących całodobowo.

W kodzie stosowane są między innymi:

- bounded queues,
- osobne workery transmisji,
- cache konfiguracji,
- przenoszenie cięższych operacji poza krytyczną ścieżkę RX,
- agregacja statystyk,
- ograniczanie wielokrotnego przetwarzania tych samych danych.

Dzięki temu APRSBox może działać także na sprzęcie znacznie słabszym od typowego współczesnego PC.

## Najważniejsze cechy

Do najbardziej charakterystycznych funkcji APRSBox należą:

- Packet Routing,
- obsługa wielu TNC,
- zaawansowane filtry DIGI,
- Duplicate Filter i Viscous Delay,
- RX iGate i kontrolowany TX iGate,
- wiadomości APRS,
- Proportional Pathing,
- mapa z PHG, śladami i siatką Maidenhead,
- NWS-WARN,
- CAWF,
- PL-WARN z obszarami Polski,
- ES-WARN z obszarami Hiszpanii,
- APRS Emergency,
- radar stacji,
- webhooki i powiadomienia,
- analiza lokalnych warunków propagacji,
- statystyki urządzeń i użytkowników,
- rozbudowana diagnostyka stacji.

Projekt jest aktywnie rozwijany, dlatego jego zakres funkcji może zmieniać się szybciej niż opisy w zewnętrznych dokumentacjach.

## Podstawowe informacje

**Nazwa:** APRSBox  
**Autor / główny maintainer:** SQ9MDD  
**Licencja:** GPL-3.0  
**System:** Linux  
**Interfejs:** WWW  
**Baza danych:** SQLite  
**Interfejs radiowy:** KISS TCP / KISS Serial  
**Dodatkowe źródło RX:** OpenWebRX MQTT  
**APRS-IS:** RX/TX  
**Instalacja:** Debian, Raspberry Pi OS, Alpine Linux, Docker  
**Repozytorium:** https://github.com/SQ9MDD/APRSBox
