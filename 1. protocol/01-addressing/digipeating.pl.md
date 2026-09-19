---
title: Digipeating w APRS
description: Jak działa retransmisja pakietów APRS przez digipeatery, czym są H-bit i znak *, jak przetwarzane są ścieżki WIDEn-N oraz dlaczego tłumienie duplikatów jest kluczowe dla poprawnej pracy sieci.
template: doc
tableOfContents: true
---

**Digipeater** jest cyfrową stacją retransmisyjną. Odbiera kompletną ramkę AX.25, analizuje jej ścieżkę i, jeżeli spełnione są odpowiednie warunki, nadaje ją ponownie.

Nie działa więc jak analogowy przemiennik, który retransmituje sygnał na bieżąco. Digipeater pracuje według zasady **store and forward**:

```text
odbiór ramki
    ↓
sprawdzenie ścieżki i reguł
    ↓
sprawdzenie duplikatu
    ↓
modyfikacja pola ścieżki
    ↓
ponowne nadanie
```

Najczęściej pakiet jest retransmitowany na tym samym kanale radiowym, ale digipeater wieloportowy może również przekazywać ruch pomiędzy różnymi kanałami.

Podstawowa decyzja o retransmisji dotyczy pola adresowego AX.25, a nie zawartości APRS w polu Information. Digipeater nie musi rozumieć, czy pakiet zawiera pozycję, wiadomość, pogodę czy telemetrię, aby poprawnie obsłużyć jego ścieżkę.

## Ważna uwaga o specyfikacji

Oryginalny APRS Protocol Reference opisuje format ścieżek i mechanizmy APRS, ale nie zawiera kompletnego, jednoznacznego algorytmu działania nowoczesnego digipeatera.

W efekcie przez lata powstały implementacje różniące się szczegółami.

W tym artykule jako punkt odniesienia przyjęto:

- współczesny, trasowalny model **New-N**,
- zalecenia APRS 1.1,
- referencyjny algorytm opisany przez Johna Langnera WB2OSZ w dokumencie **APRS Digipeater Algorithm**.

Tam, gdzie zachowanie starszych urządzeń lub innych implementacji może się różnić, zostało to zaznaczone.

## Pole ścieżki AX.25

Przykładowy pakiet APRS może wyglądać tak:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

Po Source Address i Destination Address znajdują się adresy digipeaterów:

```text
WIDE1-1,WIDE2-1
```

AX.25 przewiduje maksymalnie osiem adresów digipeaterów w jednej ramce.

Każdy taki adres zawiera między innymi **H-bit**, czyli **Has Been Repeated**. Bit ten informuje, czy dany element ścieżki został już wykorzystany.

Digipeater podejmuje decyzję na podstawie **pierwszego niewykorzystanego elementu ścieżki**.

Elementy już wykorzystane nie są ponownie brane pod uwagę.

## H-bit i znak `*`

W tekstowej reprezentacji monitorowej stan H-bit jest pokazywany znakiem:

```text
*
```

Przykład:

```text
SQ9MDD-7>APBOX0,SR5ABC*:...
```

oznacza, że adres `SR5ABC` został już wykorzystany podczas retransmisji.

`*` nie jest częścią znaku wywoławczego ani aliasu. Jest sposobem przedstawienia stanu pola adresowego AX.25.

### Tylko ostatni wykorzystany adres z `*`

W klasycznym formacie monitorowym znak `*` umieszcza się przy **ostatnim wykorzystanym adresie digipeatera**.

Jeżeli ścieżka wygląda tak:

```text
SR5AAA,SR5BBB*
```

oznacza to, że wykorzystane zostały:

```text
SR5AAA
SR5BBB
```

a `SR5BBB` jest ostatnim wykorzystanym elementem.

Zapis:

```text
SR5AAA*,SR5BBB*
```

może być pokazywany przez niektóre programy jako bezpośrednia wizualizacja poszczególnych H-bitów, ale nie jest standardową konwencją klasycznego formatu monitorowego. W standardowym zapisie wcześniejsze wykorzystane adresy są implikowane przez położenie ostatniej gwiazdki.

## Retransmisja po konkretnym znaku

Najprostszy przypadek występuje wtedy, gdy stacja źródłowa wpisze do ścieżki konkretny znak digipeatera:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB:...
```

`SR5AAA` widzi swój znak jako pierwszy niewykorzystany element ścieżki.

Po retransmisji otrzymujemy:

```text
SQ9MDD-7>APBOX0,SR5AAA*,SR5BBB:...
```

Następnie `SR5BBB` może wykorzystać kolejny element.

Po jego retransmisji standardowy zapis monitorowy będzie wyglądał tak:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

W samym polu AX.25 H-bit jest ustawiony dla obu wykorzystanych adresów.

Taki sposób pracy nazywany jest routingiem po jawnie wskazanych digipeaterach.

## Aliasy

APRS nie wymaga znajomości konkretnych znaków wszystkich digipeaterów na trasie. Zamiast nich powszechnie używa aliasów.

Digipeater może reagować na:

- własny znak,
- skonfigurowany alias prosty,
- alias typu **WIDEn-N**,
- lokalne lub regionalne aliasy zbudowane według podobnych zasad.

Jeżeli alias ma być trasowalny, nowoczesny digipeater powinien pozostawić w ścieżce własny znak, tak aby później było wiadomo, którędy pakiet rzeczywiście przeszedł.

Przykładowo trasowalny alias:

```text
TEMP
```

może po retransmisji zostać zastąpiony przez:

```text
SR5ABC*
```

Istnieją również implementacje aliasów nietrasowalnych, które jedynie oznaczają alias jako wykorzystany. W APRS współczesna praktyka preferuje jednak ścieżki, z których da się odtworzyć rzeczywistą drogę pakietu.

## WIDEn-N

Najważniejszym współczesnym mechanizmem ogólnego digipeatingu APRS jest **WIDEn-N**.

Przykład:

```text
WIDE2-2
```

Pierwsza liczba:

```text
2
```

określa klasę aliasu `WIDE2`.

Druga liczba:

```text
2
```

jest licznikiem pozostałych użyć tego elementu ścieżki.

W uproszczeniu:

```text
WIDE2-2
      ^
      pozostały 2 użycia
```

Każdy poprawnie działający digipeater WIDEn-N zmniejsza drugi licznik podczas retransmisji.

### Gdy N jest większe od 1

Pakiet:

```text
SQ9MDD-7>APBOX0,WIDE2-2:...
```

zostaje odebrany przez digipeater `SR5AAA`.

Po retransmisji:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

Digipeater:

1. dodał swój znak do ścieżki,
2. oznaczył go jako wykorzystany,
3. zmniejszył licznik z `2` do `1`.

Pozostawiony:

```text
WIDE2-1
```

jest pierwszym niewykorzystanym elementem ścieżki i może zostać obsłużony przez kolejny digipeater.

### Gdy N wynosi 1

Jeżeli kolejny digipeater `SR5BBB` odbierze:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

licznik zostanie wyczerpany.

W referencyjnym algorytmie nie pozostawia się bezcelowego:

```text
WIDE2-0
```

Alias zostaje zastąpiony znakiem digipeatera.

W standardowej reprezentacji monitorowej rezultat będzie wyglądał tak:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

Pakiet wykorzystał całą zaplanowaną część `WIDE2-2`.

Niektóre starsze implementacje mogą pozostawiać wyczerpany alias w postaci z ustawionym H-bitem, dlatego w rzeczywistych logach można spotkać także inne warianty reprezentacji.

### Gdy N wynosi 0

Element typu:

```text
WIDE2-0
```

jest wyczerpany i nie powinien powodować kolejnej retransmisji.

W poprawnie utworzonej ścieżce taki stan nie powinien pojawiać się jako pierwszy niewykorzystany adres.

## WIDE1-1 i fill-in digipeater

Alias:

```text
WIDE1-1
```

ma szczególne znaczenie w typowej architekturze APRS.

Jest używany przede wszystkim do uzyskania pierwszego, lokalnego hopu przez **fill-in digipeater**, czyli stację o mniejszym zasięgu, umieszczoną tam, gdzie użytkownicy nie mają dobrego bezpośredniego dostępu do głównych digipeaterów.

Przykładowa ścieżka:

```text
WIDE1-1,WIDE2-1
```

może zostać przetworzona następująco.

Pakiet źródłowy:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

po lokalnym fill-in `SR5FILL`:

```text
SQ9MDD-7>APBOX0,SR5FILL*,WIDE2-1:...
```

następnie po głównym digipeaterze `SR5WIDE`:

```text
SQ9MDD-7>APBOX0,SR5FILL,SR5WIDE*:...
```

Fill-in powinien być skonfigurowany zgodnie ze swoją lokalną rolą. Typowy fill-in reaguje na `WIDE1-1`, ale nie powinien zachowywać się jak pełny szerokoobszarowy digipeater obsługujący dowolne `WIDEn-N`.

Dobór ścieżki przez użytkownika jest osobnym zagadnieniem. Tutaj istotne jest jedynie to, w jaki sposób poszczególne elementy są przetwarzane.

## Pierwszy niewykorzystany element jest kluczowy

Podstawowy algorytm digipeatera nie przeszukuje ścieżki dowolnie w poszukiwaniu czegoś, co potrafi obsłużyć.

Analizowany jest przede wszystkim:

**pierwszy niewykorzystany adres digipeatera.**

Dla:

```text
SR5AAA*,WIDE2-1,SR5XYZ
```

pierwszym niewykorzystanym elementem jest:

```text
WIDE2-1
```

`SR5XYZ` znajduje się później i w normalnym trybie nie powinien "przeskoczyć" nad `WIDE2-1`.

Ta zasada zachowuje kolejność ścieżki zadaną przez nadawcę i zapobiega przypadkowemu omijaniu jej elementów.

## Preemptive digipeating

Niektóre nowoczesne implementacje oferują opcjonalny **preemptive digipeating**.

Mechanizm pozwala digipeaterowi odnaleźć własny znak lub określony alias dalej w niewykorzystanej części ścieżki i zareagować mimo wcześniejszych elementów.

Przykładowo:

```text
WIDE1-1,SR5ABC
```

normalnie wymagałoby najpierw wykorzystania `WIDE1-1`.

Preemptive digipeating może pozwolić `SR5ABC` na wcześniejsze przejęcie pakietu.

Nie jest to jednak podstawowa reguła klasycznego digipeatingu APRS. Zmienia semantykę kolejności ścieżki i powinien być stosowany świadomie. Konkretne zachowanie zależy od implementacji.

## Duplicate suppression

Sam licznik WIDEn-N i H-bity nie wystarczają do ochrony sieci.

Digipeater musi również wykrywać **duplikaty**.

Ta sama oryginalna transmisja może zostać odebrana przez kilka sąsiednich digipeaterów, a następnie ponownie dotrzeć do jednego z nich różnymi drogami.

Bez pamięci ostatnio retransmitowanych pakietów sieć bardzo szybko zaczęłaby generować kolejne kopie tej samej informacji.

W referencyjnym algorytmie WB2OSZ digipeater zapamiętuje pakiety retransmitowane w ostatnim czasie, typowo przez około:

```text
30 s
```

Jeżeli ten sam pakiet pojawi się ponownie w tym oknie, nie jest retransmitowany drugi raz.

### Ścieżka nie może być częścią porównania duplikatu

To bardzo ważne.

Ta sama ramka po przejściu przez różne digipeatery ma inną ścieżkę:

```text
SQ9MDD-7>APBOX0,SR5AAA*:...
```

i:

```text
SQ9MDD-7>APBOX0,SR5BBB*:...
```

ale nadal mogą to być dwie kopie tej samej oryginalnej transmisji.

Dlatego pole ścieżki nie może decydować o tym, czy pakiet jest duplikatem.

Referencyjny algorytm WB2OSZ porównuje:

- Source Address,
- Destination Address, z pominięciem jego SSID,
- pole Information,

a ignoruje adresy digipeaterów.

Implementacje często przechowują skrót tych danych zamiast całej ramki.

## Dlaczego digipeater nie powinien zmieniać danych pakietu

Podczas normalnego współczesnego digipeatingu zmienia się **ścieżka digipeaterowa**.

Digipeater nie powinien modyfikować:

```text
Source Address
Destination Address
Information
```

Zmiana któregoś z tych pól oznaczałaby, że retransmitowana kopia przestaje być tą samą ramką z punktu widzenia mechanizmów wykrywania duplikatów.

Jest to szczególnie istotne dlatego, że różne kopie tej samej transmisji mogą przejść różnymi ścieżkami, ale nadal muszą zostać rozpoznane jako ten sam pakiet.

Historyczny mechanizm routingu przez Destination SSID był wyjątkiem od tej współczesnej zasady, ale jest obecnie mechanizmem przestarzałym i nie powinien być podstawą nowych implementacji.

## Ochrona przed pętlami

Poprawna sieć APRS korzysta z kilku niezależnych zabezpieczeń:

1. **H-bit** oznacza już wykorzystane elementy ścieżki.
2. **Pierwszy niewykorzystany adres** narzuca kolejność przetwarzania.
3. **Licznik WIDEn-N** ogranicza liczbę kolejnych retransmisji.
4. **Duplicate suppression** zapobiega ponownemu nadaniu tej samej transmisji odebranej inną drogą.
5. **Trasowanie własnym znakiem** pozwala zobaczyć rzeczywistą drogę pakietu i wykrywać błędne zachowanie sieci.

Żaden pojedynczy mechanizm nie zastępuje pozostałych.

## Traced i non-traced digipeating

Historycznie spotykano dwa sposoby obsługi aliasów.

### Non-traced

Alias pozostawał w ścieżce i jedynie zmieniał swój stan lub licznik.

Taki zapis nie pozwalał jednoznacznie ustalić, który fizyczny digipeater wykonał retransmisję.

### Traced

Digipeater wstawia swój własny znak do ścieżki.

Przykład:

```text
WIDE2-2
```

po pierwszym hopie:

```text
SR5AAA*,WIDE2-1
```

Dzięki temu odbiorca może odtworzyć rzeczywistą drogę pakietu.

Współczesny **New-N Paradigm** preferuje trasowalny WIDEn-N właśnie dlatego, że umożliwia analizę, diagnostykę i zarządzanie siecią.

## RELAY, WIDE i TRACE - mechanizmy historyczne

W starszej dokumentacji APRS można spotkać ścieżki zawierające:

```text
RELAY
WIDE
TRACE
TRACEn-N
```

Nie należy ich mylić ze współczesnym:

```text
WIDE1-1
WIDEn-N
```

Stare aliasy `RELAY`, zwykłe `WIDE` oraz `TRACE` zostały wycofane w ramach **New-N Paradigm**, ponieważ starsze implementacje generowały z nimi dużą liczbę duplikatów i nie zapewniały wystarczająco dobrej kontroli nad ruchem.

APRS 1.1 oraz późniejsze zalecenia kierują nowe implementacje w stronę trasowalnego WIDEn-N i małych, lokalnie uzasadnionych wartości N.

Artykuły i konfiguracje używające `RELAY,WIDE` należy traktować jako materiały historyczne.

## Ograniczanie zbyt dużych wartości N

Nowoczesny digipeater nie musi bezwarunkowo honorować każdej wartości:

```text
WIDEn-N
```

otrzymanej od użytkownika.

Implementacje mogą mieć lokalne ograniczenia i pułapki dla zbyt dużych wartości, aby pojedyncza źle skonfigurowana stacja nie mogła niepotrzebnie zająć dużej części kanału.

Przykładowo pakiet z bardzo agresywną ścieżką może zostać:

- odrzucony,
- ograniczony do jednego hopu,
- przepisany według lokalnej polityki.

Jest to element ochrony zasobów wspólnego kanału, a nie zmiana podstawowego znaczenia WIDEn-N.

## Maksymalna długość ścieżki

AX.25 pozwala umieścić maksymalnie osiem adresów digipeaterów.

Ma to znaczenie dla trasowalnego WIDEn-N, ponieważ kolejne digipeatery mogą wstawiać swoje znaki do ścieżki.

Jeżeli lista adresów jest już pełna, implementacja nie może bez końca dodawać następnych elementów.

Referencyjny algorytm uwzględnia ten limit podczas przepisywania ścieżki.

Nowe systemy nie powinny tworzyć sztucznie długich tras. W praktycznym APRS celem jest skuteczne dotarcie do lokalnej sieci, a nie maksymalizacja liczby hopów.

## Opóźnione i "viscous" digipeating

Niektóre implementacje stosują dodatkowy mechanizm opóźnionej retransmisji.

Digipeater:

1. kwalifikuje pakiet do retransmisji,
2. nie nadaje go natychmiast,
3. przez krótki czas nasłuchuje, czy inny digipeater nie retransmituje tej samej ramki,
4. anuluje własną transmisję, jeżeli usłyszy odpowiednią kopię.

Takie rozwiązanie może ograniczyć liczbę równoczesnych retransmisji w sieci.

Nie jest to podstawowy element formatu APRS ani AX.25. Jest strategią implementacyjną stosowaną przez niektóre programy i urządzenia.

## Co właściwie zmienia digipeater?

Dla współczesnego, standardowego digipeatingu najważniejszą zasadę można sprowadzić do jednego zdania:

> **Digipeater przekazuje ten sam pakiet, zmieniając tylko informacje potrzebne do obsługi ścieżki AX.25.**

Przykład:

```text
przed:
SQ9MDD-7>APBOX0,WIDE2-2:!5213.45N/02100.12E>Test

po pierwszym digi:
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:!5213.45N/02100.12E>Test
```

Nie zmieniły się:

```text
SQ9MDD-7                    Source Address
APBOX0                      Destination Address
!5213.45N/02100.12E>Test    Information
```

Zmieniła się wyłącznie ścieżka:

```text
WIDE2-2
```

na:

```text
SR5AAA*,WIDE2-1
```

To rozdzielenie jest kluczowe dla poprawnego działania trasowania, śledzenia drogi pakietu i tłumienia duplikatów.

## Źródła

Podstawą tego artykułu są:

- [APRS Digipeater Algorithm - WB2OSZ](https://github.com/wb2osz/aprsspec/blob/main/APRS-Digipeater-Algorithm.pdf)
- [APRS Digipeaters - WB2OSZ](https://raw.githubusercontent.com/wb2osz/direwolf-doc/main/APRS-Digipeaters.pdf)
- [Understanding APRS Packets](https://github.com/wb2osz/aprsspec/blob/main/Understanding-APRS-Packets.pdf)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [New-N Paradigm / Fixing Digipeaters](https://www.aprs.org/fix14439.html)
- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
