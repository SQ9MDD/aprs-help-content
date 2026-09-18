---
title: Typy ramek APRS
description: Identyfikatory typu danych (DTI) w polu informacyjnym APRS.
---

Transmisja APRS zwykle korzysta z ramki AX.25 UI. Jej pole informacyjne zaczyna się od **identyfikatora typu danych** (DTI): znaku określającego sposób interpretacji dalszej części danych.

```text
ŹRÓDŁO>CEL,ŚCIEŻKA:DTI dane
```

Strona opisuje rodzaje danych APRS przenoszonych w tym polu. Nie zastępuje zasad adresowania AX.25, ścieżek, formatów pozycji ani szczegółowych pól pogody i telemetrii.

## Ważne zasady

- DTI jest zwykle pierwszym znakiem pola informacyjnego. Historyczny format pozycji `!` jest wyjątkiem: może wystąpić w dowolnym miejscu pierwszych 40 znaków, aby obsłużyć niektóre starsze digipeatery TNC.
- DTI określa **format**, a niekoniecznie odrębny typ ramki AX.25. Na przykład wiadomości, potwierdzenia, biuletyny i ogłoszenia używają `:`.
- Pozycja może być nieskompresowana, skompresowana, zakodowana Mic-E albo przesłana jako surowe zdanie GPS/NMEA. DTI wybiera rodzinę formatu; dalsze znaki określają wariant.
- Nie należy nadawać identyfikatorów oznaczonych jako przestarzałe, zarezerwowane lub „nie używać”. Odbiornik może jednak spotkać je w starym ruchu.

## Główne typy operacyjne

| DTI | Typ | Zawartość |
|---|---|---|
| `!` | Pozycja bez znacznika czasu, bez wiadomości | Pozycja stacji; także historyczny format stacji pogodowej Ultimeter 2000. |
| `=` | Pozycja bez znacznika czasu, z wiadomościami | Pozycja stacji obsługującej wiadomości APRS. |
| `/` | Pozycja ze znacznikiem czasu, bez wiadomości | Pozycja opatrzona czasem. |
| `@` | Pozycja ze znacznikiem czasu, z wiadomościami | Pozycja opatrzona czasem ze stacji obsługującej wiadomości. |
| `'` lub `` ` `` | Pozycja Mic-E | Zwięzła pozycja Mic-E; część danych jest zakodowana w adresie docelowym. Istnieją też starsze warianty; TM-D700 używa `'` dla bieżących danych Mic-E. |
| `$` | Surowe dane GPS/NMEA | Zdanie GPS przesłane bezpośrednio w APRS albo historyczny format Ultimeter 2000. |
| `;` | Obiekt | Nazwany obiekt mapy utworzony i kontrolowany przez stację; dane zawierają stan aktywny albo usunięty. |
| `)` | Element | Krótszy nazwany element mapy. Może być aktywny albo usunięty, lecz nie ma znacznika czasu ani pola właściciela obiektu. |
| `_` | Pogoda bez pozycji | Obserwacje pogodowe bez pozycji APRS. |
| `#` lub `*` | Pogoda Peet Bros U-II | Dane pogodowe w formatach Peet Bros Ultimeter-II. |
| `T` | Telemetria | Telemetria inna niż Mic-E: zwykle numer sekwencji, wartości analogowe i bity cyfrowe. |
| `:` | Rodzina wiadomości | Adresat oraz tekst; format potwierdzeń, odrzuceń, biuletynów, ogłoszeń i zapytań kierowanych. |
| `>` | Status | Jednowierszowy status lub opis zadania stacji, opcjonalnie poprzedzony czasem UTC. |
| `<` | Możliwości stacji | Tokeny możliwości, zwykle odpowiedź na zapytanie IGate. |
| `?` | Zapytanie ogólne | Jednorazowe zapytanie rozgłoszeniowe, np. `?APRS?`, `?WX?` lub `?IGATE?`. |
| `}` | Ruch strony trzeciej | Kapsułkowany pakiet APRS przekazany przez inną sieć, z pierwotnym nagłówkiem ścieżki i danymi. |
| `{` | Dane zdefiniowane przez użytkownika | Rozszerzenie przydzielone lub eksperymentalne: `{`, jednoliterowy identyfikator użytkownika, jednoliterowy typ pakietu i dane aplikacji. |
| `,` | Dane nieprawidłowe lub testowe | Dane niespełniające standardowego formatu APRS, np. nieprawidłowy GPS zgłoszony przez tracker. |
| `%` | Namierzanie kierunku | Format namierzania Agrelo DFJr/MicroFinder. |

## Raporty pozycji i położenia

Cztery zwykłe identyfikatory pozycji rozróżniają obecność czasu oraz obsługę wiadomości APRS. Poniższe prefiksy pokazują różnicę; składnię współrzędnych definiuje osobna część specyfikacji.

| Prefiks | Znaczenie |
|---|---|
| `!4903.50N/07201.75W...` | Pozycja bez czasu, bez wiadomości. |
| `=4903.50N/07201.75W...` | Pozycja bez czasu, z obsługą wiadomości. |
| `/092345z4903.50N/07201.75W...` | Pozycja z czasem, bez wiadomości. |
| `@092345z4903.50N/07201.75W...` | Pozycja z czasem, z obsługą wiadomości. |

Każdy z nich zawiera symbol i może mieć rozszerzenia pozycji lub komentarz. Pozycje skompresowane, kurs/prędkość, wysokość, zasięg radiowy, dane pogodowe i inne rozszerzenia nie są więc osobnymi DTI.

Mic-E również jest raportem pozycji, lecz jego dane są zwięźle zakodowane między adresem docelowym a polem informacyjnym. Dekoder musi rozpoznać Mic-E, a nie traktować danych jako zwykłego tekstu szerokości i długości geograficznej. Surowy GPS/NMEA zaczynający się od `$` to kolejny format związany z pozycją, wymagający parsera zdań NMEA.

## Obiekty i elementy mapy

`; ` oznacza **obiekt**. Zawiera nazwę obiektu, znacznik aktywny (`*`) lub usunięty (`_`), czas, pozycję i symbol. Obiekt może przedstawiać przemiennik, punkt wydarzenia, zdarzenie, stację pogodową lub usługę umieszczoną na mapie przez właściciela.

`) ` oznacza **element**. Zawiera nazwę elementu oraz znacznik aktywny (`!`) lub usunięty (`_`), po którym występują dane pozycji. Używa się go do krótkich obiektów mapy, gdy nie potrzeba właściciela ani czasu.

Obiekt lub element nie jest tym samym co stacja raportująca. Znak źródłowy identyfikuje nadającą stację, a nazwa w danych — wyświetlany byt.

## Pogoda i telemetria

Dane pogodowe mogą być nadawane na kilka sposobów:

- `_` rozpoczyna raport pogody bez pozycji.
- Do pozycji, obiektu lub elementu można dołączyć dane pogodowe; DTI pozostaje wtedy odpowiednio identyfikatorem pozycji, obiektu lub elementu, a nie `_`.
- `#` i `*` oznaczają historyczne formaty pogodowe Peet Bros U-II.

`T` rozpoczyna standardową telemetrię inną niż Mic-E. Podstawowy raport zaczyna się od `T#` i zawiera numer sekwencji, do pięciu kanałów analogowych oraz osiem bitów cyfrowych. Metadane, parametry, jednostki i informacja o sensie bitów korzystają z formatu wiadomości telemetrycznej, dlatego zaczynają się od `:`, a nie `T`.

## Wiadomości, biuletyny, zapytania i odpowiedzi

Rodzina wiadomości używa adresata o stałej szerokości:

```text
:ADRESAT  :tekst wiadomości{ID
```

Tekst może być wiadomością między stacjami, `ack`/`rej`, biuletynem (adresat typu `BLN...`), ogłoszeniem albo zapytaniem kierowanym. Identyfikator wiadomości służy do obsługi potwierdzeń; zapytania APRS są jednorazowe i nie powinny mieć identyfikatora ani być potwierdzane.

`? ` jest DTI dla ogólnego zapytania rozgłoszeniowego. Zapytania kierowane używają `:`, ponieważ są adresowane do jednej stacji. Odpowiedzi nie mają jednego osobnego DTI: zależnie od żądanych danych mogą być pozycją, obiektem, pogodą, statusem, wiadomością, śladem, raportem stacji słyszanych albo raportem możliwości.

## Kapsułkowanie i rozszerzenia

`} ` oznacza, że dane zawierają pakiet APRS, który przeszedł przez sieć strony trzeciej. Parser powinien odtworzyć zagnieżdżone źródło, cel, ścieżkę i wewnętrzne DTI przed dekodowaniem oryginalnych danych; nie jest to zwykły prefiks komentarza.

`{ ` służy dla udokumentowanego formatu użytkownika. Po DTI dwuznakowy nagłówek przypisuje dane autorowi/aplikacji i podtypowi pakietu. `{{` jest formą jawnie eksperymentalną. Nieobsługiwane formaty powinny być bezpiecznie ignorowane.

## Identyfikatory historyczne, zarezerwowane i zabronione

Są obecne w dostarczonej referencji APRS 1.0.1, lecz nie są normalnym wyborem dla nowych nadajników.

| Identyfikator | Status w referencji |
|---|---|
| `0x1C`, `0x1D` | Bieżące i stare dane Mic-E Rev-0 beta; historyczne, niedrukowalne identyfikatory. |
| `[` | Przestarzały beacon lokatora Maidenhead. |
| `&` | Zarezerwowane dla elementu mapy. |
| `+` | Zarezerwowane dla danych schronienia z czasem. |
| `.` | Zarezerwowane dla pogody kosmicznej. |
| `"`, `(`, `-`, `\`, `]`, `^` | Nieużywane. |
| `A`–`S`, `U`–`Z`, `a`–`z`, `0`–`9` | Nie używać jako DTI. |
| `|`, `~` | Nie używać: znaki przełączania strumienia TNC. |

## Podstawa opracowania

Przegląd oparto na dostarczonym dokumencie *APRS Protocol Reference, APRS Protocol Version 1.0.1* (rozdział 5, „APRS Data Type Identifiers”). Powiązane formaty i przypadki historyczne doprecyzowano na podstawie dostarczonych notatek o pogodzie, wiadomościach, parserze i formatach definiowanych przez użytkownika.
