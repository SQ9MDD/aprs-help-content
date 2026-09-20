---
title: Destination Address, TOCALL i ALTNET w APRS
description: Jak APRS wykorzystuje pole Destination Address AX.25, czym jest TOCALL, jak identyfikuje oprogramowanie i urządzenia oraz do czego służy ALTNET.
template: doc
tableOfContents: true
---

W ramce APRS:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

element:

```text
APBOX0
```

jest **Destination Address**, czyli adresem docelowym pola AX.25.

Nazwa może być myląca. W klasycznym AX.25 Destination Address rzeczywiście wskazuje adres docelowy ramki, ale APRS wykorzystuje to pole również do przenoszenia własnych informacji.

W typowych współczesnych pakietach APRS znajduje się tam najczęściej **TOCALL**, czyli identyfikator programu, urządzenia lub firmware, które utworzyło pakiet.

Destination Address i TOCALL nie są jednak pojęciami równoważnymi. TOCALL jest tylko jednym ze sposobów wykorzystania pola Destination Address.

## Destination Address w APRS

Destination Address korzysta z tego samego formatu adresowego AX.25 co Source Address. Pole może zawierać do sześciu znaków oraz SSID.

APRS wykorzystuje je jednak na kilka sposobów. W zależności od rodzaju pakietu może ono zawierać między innymi:

- TOCALL identyfikujący oprogramowanie lub urządzenie,
- ogólny adres APRS,
- dane zakodowane przez Mic-E,
- nazwę sieci ALTNET,
- historyczne informacje związane z dodatkowymi mechanizmami APRS.

Dlatego podczas analizy ramki nie należy automatycznie traktować ciągu po znaku `>` jako znaku adresata.

## TOCALL

Najczęściej spotykanym obecnie zastosowaniem Destination Address jest identyfikacja programu lub urządzenia generującego pakiet.

Przykład:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

Tutaj:

```text
SQ9MDD-4
```

jest Source Address, natomiast:

```text
APBOX0
```

jest Destination Address użytym jako TOCALL.

`APBOX0` jest identyfikatorem APRSBox zarejestrowanym w aktualnej bazie identyfikatorów urządzeń APRS.

TOCALL pozwala programom odbierającym APRS rozpoznać, jaki program, firmware lub urządzenie wygenerowało dany pakiet. Nie oznacza adresata pakietu.

## Aktualny rejestr TOCALL

Obecne przydziały identyfikatorów APRS są utrzymywane w repozytorium:

[github.com/aprsorg/aprs-deviceid](https://github.com/aprsorg/aprs-deviceid)

Głównym plikiem źródłowym jest:

[tocalls.yaml](https://github.com/aprsorg/aprs-deviceid/blob/main/tocalls.yaml)

Repozytorium zawiera aktualną bazę TOCALL oraz identyfikatorów Mic-E. Plik YAML jest źródłem, z którego generowane są także wersje przeznaczone do automatycznego wykorzystania przez aplikacje.

Kod wyszukiwarki identyfikatorów jest utrzymywany osobno w repozytorium:

[github.com/aprsorg/aprs-deviceid-web](https://github.com/aprsorg/aprs-deviceid-web)

Przy tworzeniu nowego programu lub urządzenia APRS nie należy wybierać przypadkowego identyfikatora `APxxxx` i traktować go jako własnego. Przydział powinien zostać sprawdzony i zarejestrowany w aktualnej bazie.

## Jak wyglądają TOCALL-e?

Wiele współczesnych identyfikatorów zaczyna się od:

```text
AP
```

po czym następują znaki identyfikujące konkretny projekt, producenta lub rodzinę urządzeń.

Przykłady z aktualnej bazy mogą mieć postać:

```text
APBOX0
APDW??
APDR??
```

Nie wszystkie wpisy są pojedynczymi, dokładnymi wartościami. Rejestr obsługuje również wzorce z symbolami wieloznacznymi, dzięki czemu jeden wpis może opisywać rodzinę wersji lub urządzeń.

Przy rozpoznawaniu TOCALL najlepiej więc korzystać z aktualnej bazy zamiast utrzymywać własną, ręcznie przepisywaną listę.

## Ogólne Destination Address

Nie każdy poprawny Destination Address APRS jest TOCALL-em konkretnego programu.

Specyfikacja APRS przewiduje również ogólne wartości używane dla określonych rodzajów ruchu, na przykład:

```text
APRS
BEACON
CQ
QST
TEST
WX
```

Historyczna specyfikacja wymienia większą grupę takich adresów i dopuszcza w części z nich dodatkowe znaki.

Są one elementem sposobu, w jaki APRS wykorzystuje Destination Address, ale nie identyfikują konkretnej aplikacji tak jak współczesne TOCALL-e.

## Destination Address nie jest adresatem wiadomości APRS

To rozróżnienie jest szczególnie ważne dla wiadomości.

Przykład:

```text
SQ9MDD-7>APBOX0,WIDE1-1::SP9XYZ   :Test
```

zawiera:

```text
Source Address:       SQ9MDD-7
Destination Address: APBOX0
```

natomiast adresatem wiadomości APRS jest:

```text
SP9XYZ
```

Adresat znajduje się wewnątrz pola Information, czyli danych APRS.

`APBOX0` nadal pełni tutaj rolę TOCALL i identyfikuje oprogramowanie generujące pakiet.

## Mic-E

Mic-E jest jednym z najważniejszych wyjątków od prostego założenia:

```text
Destination Address = TOCALL
```

W pakietach Mic-E część informacji jest kodowana bezpośrednio w Destination Address.

Sześć znaków tego pola uczestniczy między innymi w kodowaniu pozycji i stanu Mic-E, dlatego ciąg wyglądający jak nietypowy adres docelowy może w rzeczywistości zawierać dane.

Rozpoznawanie TOCALL musi więc uwzględniać typ pakietu. Nie można bezwarunkowo porównywać każdego Destination Address z bazą `tocalls.yaml`.

## ALTNET

**ALTNET**, czyli Alternate Network, wykorzystuje Destination Address jako identyfikator wydzielonej sieci lub grupy ruchu APRS.

Przykładowo eksperymentalna sieć mogłaby używać:

```text
TEST
```

jako Destination Address.

W takim przypadku `TEST` nie jest TOCALL-em programu ani znakiem odbiorcy. Jest identyfikatorem logicznej sieci.

Mechanizm ALTNET pozwala korzystać z tej samej infrastruktury APRS, jednocześnie oddzielając określony ruch od standardowego obrazu sieci. Oprogramowanie może dzięki temu wyświetlać lub przetwarzać tylko pakiety należące do wybranego ALTNET-u.

ALTNET jest kolejnym powodem, dla którego Destination Address nie należy utożsamiać z TOCALL-em.

## Historyczne wykorzystanie Destination SSID do routingu

Oryginalna specyfikacja APRS przewidywała także wykorzystanie **SSID pola Destination Address** do wyboru ogólnej ścieżki digipeaterowej.

Dla SSID od `-1` do `-7` zdefiniowano historyczne ścieżki:

| Destination SSID | Historyczna ścieżka |
|---|---|
| `-0` | użyj normalnej ścieżki VIA |
| `-1` | `WIDE-1` |
| `-2` | `WIDE-2` |
| `-3` | `WIDE-3` |
| `-4` | `WIDE-4` |
| `-5` | `WIDE-5` |
| `-6` | `WIDE-6` |
| `-7` | `WIDE-7` |

Wartości `-8` do `-15` były przeznaczone dla wariantów kierunkowych, między innymi północ, południe, wschód i zachód.

Stąd można spotkać informację, że Destination SSID określało liczbę wymaganych retransmisji.

Nie należy jednak utożsamiać na przykład:

```text
APRS-2
```

ze współczesnym:

```text
WIDE2-2
```

To dwa różne mechanizmy.

Kodowanie ścieżki w Destination SSID powstało przed mechanizmem WIDEn-N i było przeznaczone między innymi dla bardzo prostych trackerów, w których kilka przełączników mogło bezpośrednio ustawiać Destination SSID.

W nowszej dokumentacji APRS mechanizm ten jest oznaczony jako **obsolete**. Współczesne implementacje powinny przekazywać ścieżkę w przeznaczonym do tego polu Digipeater Addresses AX.25.

## A co z bitami C?

W siódmym oktecie adresu AX.25, obok SSID, znajdują się również bity związane z mechanizmem Command/Response AX.25.

Nie są one częścią TOCALL ani znaczenia Destination Address w APRS.

W praktycznych implementacjach APRS spotyka się różne ustawienia tych bitów. Oprogramowanie APRS nie powinno więc próbować określać znaczenia TOCALL, ALTNET-u ani innych zastosowań Destination Address na podstawie kombinacji bitów C.

## Jak interpretować Destination Address?

Dla typowego współczesnego pakietu:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

możemy odczytać:

```text
SQ9MDD-4   Source Address
APBOX0     Destination Address użyty jako TOCALL
WIDE1-1    element ścieżki
```

Nie można jednak zastosować tej interpretacji mechanicznie do każdej ramki APRS.

Destination Address może pełnić różne funkcje:

```text
TOCALL
ogólny adres APRS
Mic-E
ALTNET
historyczne mechanizmy APRS
```

Poprawna interpretacja zależy więc od rodzaju pakietu i kontekstu protokołu.
