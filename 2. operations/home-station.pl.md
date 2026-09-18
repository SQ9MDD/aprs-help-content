---
title: Stacja domowa APRS
description: Jak zbudować prostą domową stację APRS, od odbioru ramek po iGate i digipeater.
template: doc
tableOfContents: true
---

Domowa stacja APRS to jeden z najprostszych sposobów, żeby zacząć realnie korzystać z APRS.

Może tylko odbierać ruch z okolicy, może przekazywać odebrane pakiety do APRS-IS jako iGate, a jeżeli lokalna sieć tego potrzebuje, może również pracować jako digipeater.

## Co jest potrzebne?

Najprostsza stacja domowa składa się z czterech elementów:

1. **Radiotelefon na pasmo 2 m**
2. **Antena**
3. **Modem lub TNC**
4. **Komputer z oprogramowaniem APRS**

W Polsce podstawowym kanałem APRS na paśmie 2 m jest **144,800 MHz FM**.

Typowy zestaw może wyglądać tak:

```text
Antena
  |
Radio 144.800 MHz
  |
TNC / modem
  |
Komputer
  |
Internet
```

Komputerem nie musi być klasyczny PC. Bardzo dobrze sprawdza się Raspberry Pi, mały komputer x86 albo inny energooszczędny komputer pracujący przez całą dobę.

## Radio

Do odbioru APRS nie jest potrzebny specjalny radiotelefon z obsługą APRS.

Wystarczy zwykłe radio FM pracujące na 144,800 MHz.

Najlepiej, jeżeli urządzenie pozwala wyprowadzić:

- sygnał audio z odbiornika,
- sygnał audio do nadajnika,
- sterowanie PTT.

W wielu radiotelefonach można wykorzystać złącze DATA, PACKET lub złącze akcesoriów.

Możliwe jest również użycie wyjścia głośnikowego i wejścia mikrofonowego, ale zwykle wymaga to większej uwagi przy ustawianiu poziomów audio.

## Modem lub TNC

Pakiety APRS na 2 m są najczęściej przesyłane za pomocą AFSK 1200 baud.

Do ich dekodowania potrzebny jest modem.

Może to być klasyczny sprzętowy TNC, ale obecnie bardzo często wykorzystuje się modem programowy.

Popularnym rozwiązaniem jest **Dire Wolf**.

Dire Wolf wykorzystuje kartę dźwiękową komputera jako modem AFSK i może jednocześnie udostępniać interfejs KISS TCP dla innych programów.

```text
Radio
  |
Karta dźwiękowa
  |
Dire Wolf
  |
KISS TCP
  |
Oprogramowanie APRS
```

## Antena

W przypadku stacji domowej antena często ma większe znaczenie niż moc nadajnika.

Do odbioru lokalnego ruchu wystarczy prosta antena na 2 m.

Jeżeli stacja ma pracować jako iGate albo digipeater, warto zastosować antenę zamontowaną możliwie wysoko i mającą dobry widok na okolicę.

Nie zawsze jednak największy możliwy zasięg jest zaletą.

W APRS chodzi o budowanie użytecznej sieci radiowej, a nie o odbieranie maksymalnej liczby stacji z bardzo dużych odległości.

## Komputer

Oprogramowanie APRS może pracować na:

- Raspberry Pi,
- komputerze PC,
- małym komputerze x86,
- serwerze domowym,
- innym komputerze z Linuxem lub Windowsem.

Dla stacji działającej całodobowo dobrze sprawdza się niewielki komputer z Linuxem.

Przykładowa konfiguracja:

```text
Raspberry Pi
   |
Dire Wolf
   |
KISS TCP
   |
APRSBox
```

## Pierwszy krok: tylko odbiór

Najlepiej zacząć od konfiguracji odbiorczej.

Ustaw radio na:

```text
144.800 MHz
FM
bez CTCSS
bez DCS
```

Podłącz audio radia do komputera i uruchom modem.

Po poprawnym ustawieniu poziomu audio powinny zacząć pojawiać się odebrane ramki APRS, na przykład:

```text
SQ9ABC-9>APRS,WIDE1-1,WIDE2-1:...
```

Na tym etapie niczego jeszcze nie musisz nadawać.

Możesz spokojnie obserwować ruch i sprawdzić:

- jakie stacje są słyszalne,
- jakie digipeatery pracują w okolicy,
- jak duży jest lokalny ruch,
- czy poziom audio jest ustawiony poprawnie.

## Dodanie iGate

Kolejnym krokiem może być uruchomienie iGate.

iGate odbiera pakiety z radia i przekazuje je do APRS-IS.

```text
RF 144.800 MHz
      |
    Radio
      |
    Modem
      |
  APRSBox
      |
   APRS-IS
```

Dzięki temu stacje odebrane przez naszą instalację pojawią się również w globalnej sieci APRS.

Przy konfiguracji potrzebne są przede wszystkim:

- własny znak,
- połączenie z APRS-IS,
- poprawnie wyliczony APRS-IS passcode,
- skonfigurowany port KISS lub połączenie z TNC.

## A co z nadawaniem?

Nie trzeba od razu uruchamiać nadajnika.

Dobra kolejność jest następująca:

```text
Odbiór
  |
Obserwacja lokalnej sieci
  |
iGate RX
  |
Własny beacon
  |
Ewentualnie iGate TX
  |
Ewentualnie digipeater
```

Pozwala to najpierw poznać lokalną sieć APRS i dopiero później zdecydować, jakie funkcje są rzeczywiście potrzebne.

## Własny beacon

Jeżeli stacja ma być widoczna w sieci, może okresowo nadawać własną pozycję.

Dla stacji domowej nie ma potrzeby robić tego często, ponieważ pozycja się nie zmienia.

Beacon może zawierać również dodatkową informację, na przykład:

```text
PHG...
QRV 145.500 MHz
```

Może też zawierać informację o lokalnym przemienniku, stronie internetowej lub innej usłudze.

## iGate w kierunku RF

Niektóre stacje potrafią również przekazywać wybrane pakiety z APRS-IS na radio.

Najczęściej dotyczy to wiadomości adresowanych do lokalnych stacji.

Ta funkcja wymaga większej ostrożności.

Nie należy traktować APRS-IS jako źródła ruchu, który można bez ograniczeń retransmitować na RF.

Kanał APRS ma ograniczoną przepustowość, dlatego ruch kierowany z Internetu na radio powinien być filtrowany i ograniczony do rzeczywiście potrzebnych pakietów.

## Digipeater

Stacja domowa może również retransmitować pakiety innych stacji.

Nie oznacza to jednak, że każdy iGate powinien być jednocześnie digipeaterem.

Najpierw warto sprawdzić:

- czy w okolicy są już digipeatery,
- jakie mają zasięgi,
- czy istnieją rzeczywiste obszary bez pokrycia,
- jak duże jest obciążenie kanału.

Dodatkowy digipeater w miejscu, gdzie pokrycie już istnieje, może pogorszyć sytuację zamiast ją poprawić.

## Przykładowa kompletna stacja

Jedna z możliwych konfiguracji wygląda tak:

```text
Antena 2 m
    |
Radiotelefon
    |
Interfejs audio + PTT
    |
Dire Wolf
    |
KISS TCP
    |
APRSBox
    |
APRS-IS
```

APRSBox może wtedy realizować między innymi:

- odbiór pakietów,
- prezentację lokalnego ruchu,
- iGate,
- własne beacony,
- wiadomości APRS,
- digipeater,
- obserwację statystyk sieci.

Nie jest to jedyny możliwy zestaw.

Zamiast Dire Wolfa można użyć sprzętowego TNC, VP-Digi lub innego urządzenia oferującego interfejs KISS.

## Od czego najlepiej zacząć?

Najprościej:

```text
1. Antena
2. Radio na 144.800 MHz
3. Dire Wolf
4. Odbiór ramek
5. APRSBox lub inne oprogramowanie APRS
6. iGate RX
```

Dopiero kiedy wszystko działa poprawnie, warto uruchamiać nadawanie.

Taka kolejność pozwala poznać lokalny ruch APRS bez dokładania do kanału kolejnych pakietów.

Domowa stacja APRS nie musi od pierwszego dnia być rozbudowanym węzłem sieci. Może zacząć jako zwykły odbiornik, a później stopniowo rozwijać się wraz z doświadczeniem operatora i potrzebami lokalnej sieci.
