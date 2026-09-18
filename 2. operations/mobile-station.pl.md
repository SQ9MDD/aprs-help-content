---
title: Mobilna stacja APRS
description: Jak dobrać i zmontować sprzęt do mobilnej stacji APRS.
template: doc
tableOfContents: true
---

Mobilna stacja APRS pokazuje pozycję pojazdu podczas jazdy i może odbierać stacje oraz wiadomości APRS. Może być prostym trackerem wysyłającym tylko pozycję albo pełnym terminalem radiowym z wyświetlaczem.

Najważniejszy jest dobrze dobrany i poprawnie zamontowany sprzęt. Konfiguracja ramek, ścieżek i beaconowania zależy od lokalnej sieci — te zagadnienia opisuje dział protokołu.

W Polsce podstawowym kanałem APRS na paśmie 2 m jest **144,800 MHz FM**.

## Z czego składa się stacja?

Najprostszy zestaw ma cztery elementy:

1. **Radio lub nadajnik**
2. **Tracker APRS albo TNC**
3. **Odbiornik GPS**
4. **Antena i zasilanie**

Może wyglądać tak:

```text
GPS
 |
Tracker / TNC
 |             \
 |              \ audio + PTT
Radio 144.800 MHz
 |
Antena na pojeździe
```

Wiele współczesnych radiotelefonów APRS łączy tracker, TNC i GPS w jednej obudowie. Wtedy do kompletnej stacji wystarczają radio, antena i zasilanie.

## Trzy podstawowe warianty

### Tracker i zwykłe radio

To najprostsza konfiguracja, gdy stacja ma przede wszystkim nadawać pozycję. Tracker odbiera dane z GPS, tworzy pakiet APRS i przekazuje sygnał audio do wejścia mikrofonowego radia, jednocześnie załączając PTT.

```text
GPS → tracker → radio → antena
```

Taki zestaw jest niewielki, zużywa mało energii i może działać bez komputera. Jego ograniczeniem jest zwykle brak wygodnego odbioru oraz obsługi wiadomości APRS.

### Radio z wbudowanym APRS

Radiotelefon z własnym TNC i GPS jest najwygodniejszym rozwiązaniem dla osoby, która chce zarówno nadawać pozycję, jak i korzystać z APRS bez dodatkowego komputera. Oprócz własnej pozycji może pokazywać na ekranie odebrane stacje mobilne i stałe, ich odległość, kierunek oraz ostatnio odebrane dane.

W praktyce taki radiotelefon ułatwia nawiązanie łączności. Można zauważyć stację jadącą w pobliżu, sprawdzić jej znak i kierunek, a następnie wywołać ją głosowo na częstotliwości podanej w ramce APRS lub informacji o obiekcie. W podobny sposób można korzystać z obiektów APRS, na przykład informacji o lokalnym przemienniku, jego częstotliwości czy tonie dostępu.

Wbudowany terminal APRS pozwala zwykle także:

- odbierać i przeglądać wiadomości APRS,
- nadać krótką wiadomość do wybranej stacji,
- odebrać lokalny biuletyn APRS,
- przeglądać listę odebranych stacji i obiektów,
- wyświetlić podstawowe dane pozycyjne bez uruchamiania telefonu.

Zakres funkcji i sposób prezentacji danych zależą od modelu. Radio z wyświetlaczem nie zastępuje pełnej mapy, ale jest bardzo wygodne do szybkiego sprawdzenia, kto pracuje w okolicy i do krótkiej wymiany wiadomości podczas postoju.

Przed zakupem warto sprawdzić, czy radio ma:

- wbudowany GPS lub wejście dla zewnętrznego odbiornika,
- modem AFSK 1200 baud i TNC,
- obsługę packet data na paśmie 2 m,
- złącze danych albo możliwość użycia zewnętrznego TNC,
- czytelny interfejs, który da się bezpiecznie obsłużyć w pojeździe.

Przykładami radiotelefonów z obsługą APRS są przenośny **Kenwood TH-D75E/D75A** oraz mobilne **Kenwood TM-D710G**, **Yaesu FTM-300DE/DR** i **Yaesu FTM-500DE/DR**. To przykłady klas urządzeń, a nie lista zakupowa: część modeli może być wycofana, a dostępność i wariant pasmowy zależą od rynku.

### Radio, TNC i aplikacja

Można też połączyć zwykłe radio z zewnętrznym TNC, tabletem, telefonem lub małym komputerem. Aplikacja pełni wtedy rolę terminala APRS, a TNC zamienia dane cyfrowe na audio AFSK i steruje PTT.

```text
GPS / telefon
      |
Aplikacja APRS
      |
     TNC
      |
Radio → antena
```

To rozwiązanie zapewnia w zasadzie tę samą podstawową funkcjonalność APRS co radio z wbudowanym terminalem: pozycję, listę stacji, wiadomości, biuletyny i obiekty. Na większym ekranie dochodzi jednak mapa, która pozwala od razu zobaczyć położenie własnej stacji, innych użytkowników, przemienników i pozostałych obiektów APRS.

To rozwiązanie daje dużą swobodę, ale ma więcej przewodów, połączeń i punktów możliwej awarii. Najlepiej sprawdza się wtedy, gdy potrzebna jest mapa, większy ekran albo wygodniejsze pisanie wiadomości na postoju.

## Radio

Do mobilnego APRS potrzebne jest radio FM pracujące na paśmie 2 m. Nie musi mieć fabrycznej obsługi APRS, jeśli współpracuje z trackerem lub TNC.

Przy wyborze radia zwróć uwagę przede wszystkim na:

- dostęp do wejścia mikrofonowego, wyjścia audio i PTT,
- złącze DATA, PACKET lub akcesoriów — upraszcza połączenie z trackerem,
- możliwość ustawienia odpowiedniej mocy nadawania,
- stabilne zasilanie 12 V,
- wygodne mocowanie i obsługę bez odrywania uwagi od drogi.

Najbardziej praktyczne są radia mobilne montowane na stałe, ale do prostego trackera można użyć także radiotelefonu ręcznego. W takim przypadku trzeba szczególnie zadbać o przewód, przejściówkę do złącza akcesoriów i chłodzenie radia podczas nadawania.

## Tracker i TNC

**Tracker** jest urządzeniem wyspecjalizowanym w automatycznym wysyłaniu pozycji. Zwykle ma wejście GPS, wyjście audio, sterowanie PTT oraz pamięć ustawień. Nie każdy tracker odbiera i dekoduje ruch APRS — przed zakupem warto wyraźnie sprawdzić, czy jest to urządzenie tylko nadawcze, czy również odbiorcze.

**TNC** jest modemem packet radio. Może współpracować z aplikacją APRS albo z radiem i komputerem. Oprócz nadawania pozwala zwykle na odbiór i dekodowanie ramek, ale zakres funkcji zależy od konkretnego modelu i programu.

W urządzeniach mobilnych spotkasz najczęściej:

- sprzętowy tracker z GPS lub wejściem NMEA,
- zewnętrzny TNC podłączany przez USB, Bluetooth albo Wi-Fi,
- TNC wbudowane w radio,
- modem programowy działający na telefonie lub komputerze z interfejsem audio.

Na paśmie 2 m APRS wykorzystuje zwykle AFSK 1200 baud. Zewnętrzny tracker lub TNC musi więc być zgodny z tym trybem i z poziomami audio używanymi przez radio.

## GPS

GPS może być wbudowany w radio albo tracker, podłączony jako osobny odbiornik lub udostępniony przez telefon.

Najpewniejszy jest odbiornik, który przekazuje aktualne dane pozycyjne bezpośrednio do urządzenia APRS. W starszych trackerach spotyka się komunikację NMEA przez port szeregowy. Przed montażem sprawdź zgodność złącza, napięcia zasilania i formatu danych.

Odbiornik powinien mieć możliwie dobry widok na niebo. W samochodzie warto umieścić go pod przednią szybą albo użyć anteny GPS w miejscu zalecanym przez producenta. Nie zakrywaj go metalowymi elementami ani nie chowaj głęboko pod deską rozdzielczą.

## Antena

W mobilnym APRS antena jest często ważniejsza niż duża moc nadajnika. Dobrze zamontowana antena na 2 m poprawia zarówno zasięg nadawania, jak i odbiór stacji oraz digipeaterów.

Najlepszym, typowym miejscem jest dach samochodu. Metalowa karoseria tworzy wtedy przeciwwagę dla anteny. Antena na klapie bagażnika, uchwycie lub relingu może działać poprawnie, ale jej charakterystyka i zasięg mogą być inne.

Przy montażu zwróć uwagę na:

- stabilne mocowanie, odporne na drgania i warunki pogodowe,
- nieuszkodzony przewód koncentryczny,
- szczelne złącze na zewnątrz pojazdu,
- prowadzenie kabla bez przycinania go drzwiami,
- odstęp od innych anten i przeszkód metalowych.

Przed dłuższą trasą warto sprawdzić SWR. Wysoki SWR może oznaczać problem z anteną, przewodem lub złączem i niepotrzebnie obciążać nadajnik.

## Połączenie z radiem

Tracker albo TNC trzeba podłączyć do radia trzema podstawowymi sygnałami:

| Sygnał | Funkcja |
|---|---|
| Audio do radia | Przesyła modulację AFSK do wejścia mikrofonowego lub DATA |
| Audio z radia | Przekazuje odebrany sygnał do TNC, jeśli stacja ma odbierać |
| PTT | Włącza nadawanie w radiu |

Wiele radii oferuje dedykowane złącze DATA lub PACKET. Jest ono zwykle lepszym wyborem niż złącze mikrofonowe, ponieważ zapewnia właściwsze poziomy audio i pozwala uniknąć części dodatkowych ustawień.

Połączenie audio wymaga regulacji poziomów. Zbyt niski poziom może uniemożliwić dekodowanie lub odbiór pakietu, a zbyt wysoki przesteruje modulację. Ustawienie należy sprawdzić podczas rzeczywistego odbioru i transmisji testowej, zgodnie z instrukcją urządzenia.

## Zasilanie i montaż w pojeździe

Radio mobilne najlepiej zasilać z instalacji 12 V przez odpowiednio dobrane przewody i bezpiecznik umieszczony blisko źródła zasilania. Podczas nadawania radio pobiera znacznie więcej prądu niż w odbiorze, więc przewód zasilający i gniazdo akcesoriów muszą być dobrane do jego poboru.

Tracker, TNC i GPS mogą wymagać 5 V, USB albo osobnego stabilizatora. Nie zakładaj, że każde złącze w radiu zasili dodatkowe urządzenie — sprawdź w dokumentacji dostępne napięcie i dopuszczalny prąd.

Urządzenia oraz przewody powinny być zamontowane pewnie i poza strefą działania poduszek powietrznych. Ekran, mikrofon i przyciski nie mogą ograniczać widoczności ani utrudniać prowadzenia pojazdu.

## Przed pierwszą trasą

Przed stałym montażem uruchom zestaw na stole albo na postoju. Sprawdź kolejno:

1. czy radio pracuje na właściwym kanale APRS,
2. czy GPS przekazuje aktualną pozycję,
3. czy tracker lub TNC poprawnie załącza PTT,
4. czy poziom audio nie przesterowuje nadajnika,
5. czy antena i przewody są sprawne,
6. czy urządzenia pozostają stabilne po uruchomieniu silnika.

Sposób ustawienia znaku, symbolu, beaconowania i ścieżki zależy od zasad APRS oraz lokalnego pokrycia. Skonfiguruj je zgodnie z artykułami w dziale protokołu, a po pierwszych przejazdach sprawdź, czy odbierane pozycje odpowiadają rzeczywistej trasie.

## Od czego najlepiej zacząć?

Najprostsza droga do mobilnego APRS wygląda tak:

```text
1. Antena na 2 m
2. Radio na 144.800 MHz
3. Tracker z GPS lub radio z wbudowanym APRS
4. Pewne zasilanie i montaż
5. Test audio, PTT i odbioru
6. Konfiguracja APRS według zasad lokalnej sieci
```

Dopiero po udanym teście warto rozbudować stację o ekran z mapą, wiadomości APRS, dodatkowy TNC lub integrację z telefonem.
