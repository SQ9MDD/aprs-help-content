---
title: Jak działa APRS?
description: Przegląd drogi informacji w APRS, od transmisji radiowej przez digipeatery i IGate po APRS-IS i aplikacje.
template: doc
tableOfContents: true
---

APRS jest systemem rozproszonym.

Nie istnieje jeden centralny punkt, przez który musi przejść każda informacja. Pakiet może zostać odebrany bezpośrednio przez inną stację, powtórzony przez digipeater, przekazany do APRS-IS przez IGate albo dotrzeć jednocześnie do kilku elementów infrastruktury.

Dlatego APRS najlepiej rozumieć nie jako pojedynczą liniową trasę, ale jako **sieć radiową, w której jedna transmisja może zostać wykorzystana przez wielu odbiorców**.

Najprostsza droga pakietu może wyglądać tak:

![Droga pakietu via RF](./_img/diagram1.png)

```text
Stacja A
   |
   | RF
   v
Stacja B
```

Ale w większej sieci ten sam pakiet może jednocześnie trafić do kilku odbiorców:

```text
                  -> Stacja B
                 /
Stacja A --------+-> Digipeater
                 \
                  -> IGate
```

Każdy z tych elementów pełni inną rolę.

## Najprostszy przypadek: stacja do stacji

Do działania APRS nie jest potrzebny Internet, serwer ani digipeater.

Jeżeli dwie stacje znajdują się w swoim bezpośrednim zasięgu radiowym, jedna z nich może nadać pakiet APRS, a druga odebrać go bezpośrednio.

```text
Stacja A
   |
   | RF
   v
Stacja B
```

Stacja A może na przykład przesłać:

- swoją pozycję,
- status,
- monitorowaną częstotliwość,
- wiadomość,
- dane telemetryczne,
- informację pogodową.

Jeżeli Stacja B potrafi odebrać i zinterpretować dany typ informacji, może wykorzystać ją natychmiast.

Na tym etapie pakiet nie musi trafić nigdzie dalej.

To już jest poprawnie działający APRS.

## Co właściwie jest nadawane?

Typowy pakiet APRS na warstwie radiowej jest przenoszony w ramce **AX.25 UI**, czyli *Unnumbered Information*.

W uproszczeniu zawiera ona między innymi:

- znak stacji źródłowej,
- pole destination,
- ścieżkę,
- pole informacji APRS.

W reprezentacji tekstowej taki pakiet może wyglądać na przykład tak:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Na tym etapie najważniejsze jest jednak nie to, jak dokładnie zbudowany jest pakiet, ale co dzieje się z nim po nadaniu.

Szczegółowa struktura ramek AX.25 i danych APRS jest opisana w kolejnych artykułach.

## Jedna transmisja może mieć wielu odbiorców

Transmisja radiowa nie jest kierowana do jednego fizycznego urządzenia w taki sposób jak klasyczne połączenie punkt-punkt.

Jeżeli kilka stacji znajduje się w zasięgu nadajnika, wszystkie mogą odebrać ten sam pakiet.

Przykładowo:

```text
                -> Stacja B
               /
Stacja A ------+-> Digipeater
               \
                -> IGate
```

Ta sama pojedyncza transmisja Stacji A może więc zostać odebrana przez:

- inną stację użytkownika,
- digipeater,
- IGate,
- kilka takich urządzeń jednocześnie.

Nie oznacza to, że pakiet musi później przejść przez każdy z tych elementów.

Każdy odbiorca może wykonać własne zadanie niezależnie od pozostałych.

## Rola digipeatera

**Digipeater** to stacja, która może odebrać pakiet APRS i ponownie nadać go przez radio.

Jego podstawowym zadaniem jest zwiększenie zasięgu radiowego informacji.

Przykład:

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

Stacja B może znajdować się poza bezpośrednim zasięgiem Stacji A, ale nadal odebrać jej pakiet dzięki retransmisji.

Digipeater nie powtarza jednak automatycznie każdej odebranej ramki.

Decyzja o retransmisji zależy między innymi od:

- zawartości ścieżki pakietu,
- konfiguracji digipeatera,
- mechanizmów eliminacji duplikatów,
- lokalnej polityki sieci.

Współczesne sieci APRS najczęściej korzystają z mechanizmów ścieżek opartych między innymi na `WIDE1-1` i `WIDE2-n`.

Szczegółowe zasady działania ścieżek i digipeaterów są opisane osobno.

## Digipeater nie jest IGate

Digipeater i IGate to dwie różne funkcje.

Digipeater działa przede wszystkim po stronie radiowej:

```text
RF -> RF
```

Odbiera pakiet radiowy i w określonych warunkach ponownie nadaje go przez radio.

IGate natomiast łączy sieć radiową z APRS-IS:

```text
RF -> Internet
```

Jedno urządzenie może pełnić obie role jednocześnie, ale nie musi.

Możliwa jest więc stacja:

- tylko digipeater,
- tylko IGate,
- digipeater i IGate jednocześnie.

Rozróżnienie tych funkcji jest ważne dla zrozumienia działania całej infrastruktury APRS.

## Rola IGate

**IGate**, czyli Internet Gateway, odbiera pakiety z lokalnego kanału radiowego i może przekazywać je do sieci APRS-IS.

Przykład:

```text
Stacja A
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

Jeżeli IGate odbierze pakiet Stacji A, może przesłać go do APRS-IS.

Od tego momentu informacja może być dostępna dla:

- klientów APRS,
- serwisów mapowych,
- baz danych,
- aplikacji monitorujących,
- innych systemów korzystających z APRS-IS.

IGate nie jest wymagany, aby APRS działał lokalnie.

Jego rolą jest przede wszystkim połączenie lokalnej sieci radiowej z infrastrukturą internetową.

## Rola APRS-IS

**APRS-IS, APRS Internet System**, to globalna infrastruktura internetowa służąca do dystrybucji danych APRS.

Do APRS-IS trafiają między innymi pakiety przekazane przez IGate.

Uproszczony schemat może wyglądać tak:

```text
Sieć RF
   |
   v
IGate
   |
   v
APRS-IS
   |
   +-> aplikacje APRS
   |
   +-> serwisy mapowe
   |
   +-> systemy monitorujące
   |
   +-> inne usługi
```

APRS-IS pozwala aplikacjom odbierać dane pochodzące z wielu różnych obszarów geograficznych.

Nie oznacza to jednak, że APRS-IS zastępuje sieć radiową.

Jest jej rozszerzeniem.

## Typowa droga pakietu

Rozważmy prosty przykład.

Stacja:

```text
SQ9MDD-7
```

nadaje pakiet APRS przez radio.

Pakiet może zostać odebrany przez lokalną stację:

```text
SQ9MDD-7
   |
   | RF
   v
Stacja B
```

W tym momencie informacja jest już użyteczna.

Jednocześnie ten sam pakiet może odebrać digipeater:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
```

Jeżeli ścieżka i konfiguracja na to pozwalają, digipeater retransmituje pakiet:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Stacja C
```

Pakiet może również odebrać IGate:

```text
SQ9MDD-7
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

A następnie dane mogą zostać pobrane przez aplikację:

```text
SQ9MDD-7
   |
   v
IGate
   |
   v
APRS-IS
   |
   v
Aplikacja APRS
```

To nadal może być ten sam pierwotny pakiet.

## Pakiet nie musi przejść wszystkich etapów

Bardzo ważne jest, aby nie traktować tej drogi jako obowiązkowego łańcucha:

```text
Stacja -> Digipeater -> IGate -> APRS-IS
```

To tylko jedna z możliwych dróg.

Pakiet może zostać odebrany bezpośrednio:

```text
Stacja A -> Stacja B
```

Może zostać odebrany przez IGate bez udziału digipeatera:

```text
Stacja A -> IGate -> APRS-IS
```

Może działać wyłącznie w sieci radiowej:

```text
Stacja A -> Digipeater -> Stacja B
```

Może też dotrzeć do kilku odbiorców jednocześnie:

```text
                     -> Stacja B
                    /
Stacja A -> Digipeater -> Stacja C
       \            \
        \            -> IGate 2
         \
          -> IGate 1
```

APRS nie ma więc jednej obowiązkowej drogi pakietu.

## Jedna ramka może dotrzeć wieloma drogami

W rzeczywistej sieci ta sama ramka może zostać odebrana przez kilka digipeaterów i kilka IGate.

Przykładowo:

```text
                    -> IGate 1
                   /
Stacja A -> DIGI -+-> IGate 2
        \          \
         \          -> Stacja B
          \
           -> IGate 3
```

Dodatkowo niektóre IGate mogą odebrać również pierwotną transmisję bezpośrednio.

W efekcie ta sama informacja może dotrzeć do infrastruktury więcej niż jedną drogą.

Jest to normalne zjawisko w APRS.

## Duplikaty

Ponieważ jedna transmisja może być odebrana i przekazana przez wiele elementów infrastruktury, APRS musi radzić sobie z duplikatami.

Ta sama ramka może na przykład zostać:

- odebrana bezpośrednio przez IGate,
- odebrana ponownie po retransmisji przez digipeater,
- odebrana przez drugi IGate,
- przekazana do APRS-IS z kilku miejsc.

Dlatego digipeatery, IGate i serwery mogą stosować mechanizmy rozpoznawania i odrzucania powtórzeń.

Bez takich mechanizmów jedna transmisja mogłaby niepotrzebnie mnożyć się w sieci.

Szczegółowe zasady obsługi duplikatów zależą od konkretnego elementu infrastruktury i są opisane w dalszych częściach dokumentacji.

## RF do APRS-IS

Kierunek:

```text
RF -> APRS-IS
```

jest jednym z podstawowych zadań IGate.

Pakiety odebrane lokalnie przez radio mogą być przekazywane do APRS-IS, gdzie stają się dostępne dla aplikacji internetowych.

Nie oznacza to jednak, że każda odebrana ramka musi być przekazana.

IGate może stosować określone zasady i filtry zależne od konfiguracji oraz typu ruchu.

## APRS-IS do RF

Kierunek przeciwny:

```text
APRS-IS -> RF
```

wymaga znacznie większej ostrożności.

Kanał radiowy ma ograniczoną przepustowość, dlatego nie można bezrefleksyjnie przenosić całego ruchu APRS-IS na RF.

Na radio mogą być przekazywane tylko wybrane informacje zgodnie z zasadami działania IGate.

Typowym przykładem jest wiadomość skierowana do lokalnej stacji, która była niedawno słyszana przez dany IGate.

Schemat może wtedy wyglądać tak:

```text
APRS-IS
   |
   v
IGate
   |
   | RF
   v
Stacja lokalna
```

Ruch w kierunku Internet -> RF wymaga kontroli, ponieważ każdy taki pakiet zajmuje czas wspólnego kanału radiowego.

Szczegółowe zasady gatingu APRS-IS -> RF są opisane osobno.

## Third-party traffic

W niektórych sytuacjach pakiet pochodzący z innej części systemu musi zostać ponownie umieszczony na RF.

APRS posiada do tego specjalny mechanizm **third-party traffic**.

Nie jest to zwykłe skopiowanie pakietu tekstowego z APRS-IS i nadanie go bez zmian przez radio.

Oryginalna informacja jest umieszczana w specjalnej strukturze, która zachowuje informacje o jej pochodzeniu.

Mechanizm ten jest istotny między innymi przy kontrolowanym przekazywaniu określonego ruchu z APRS-IS na RF.

Szczegółowa składnia third-party traffic jest opisana w części dotyczącej formatów specjalnych.

## Co działa bez Internetu?

Bardzo dużo.

Przykład:

```text
HT
 |
 | RF
 v
Digipeater
 |
 | RF
 v
Radio mobilne
```

Jeżeli operator radiotelefonu mobilnego odbierze pakiet stacji przenośnej, APRS spełnił swoje zadanie.

Nie jest potrzebny APRS-IS.

Nie jest potrzebna mapa internetowa.

Nie jest potrzebny żaden serwer.

**Brak Internetu nie oznacza braku APRS.**

## Co działa bez digipeatera?

Jeżeli stacje są w swoim bezpośrednim zasięgu, digipeater nie jest potrzebny.

```text
Stacja A
   |
   +-----> Stacja B
   |
   +-----> IGate
```

Stacja B może wykorzystać informację lokalnie, a IGate niezależnie przekazać ją do APRS-IS.

Digipeater jest potrzebny dopiero wtedy, gdy retransmisja rzeczywiście zwiększa użyteczny zasięg sieci.

## Co działa bez IGate?

Również pełna lokalna sieć radiowa.

```text
Stacja A
   |
   v
Digipeater
   |
   v
Stacja B
```

Stacje mogą wymieniać pozycje, statusy, wiadomości i inne informacje bez jakiegokolwiek połączenia z APRS-IS.

Pakiety nie pojawią się wtedy w serwisach internetowych, ale lokalna funkcja APRS pozostaje w pełni użyteczna.

## Odbiór bezpośredni jest ważny

W praktyce pakiet odebrany bezpośrednio może być bardziej wartościowy niż informacja dostępna przez Internet.

Jeżeli znajdująca się w pobliżu stacja nada:

```text
SP9XYZ
145.550 MHz
```

operator może natychmiast wykorzystać tę informację do nawiązania łączności.

Nie musi czekać, aż pakiet:

- zostanie odebrany przez IGate,
- trafi do APRS-IS,
- zostanie zapisany przez usługę internetową,
- zostanie pobrany przez aplikację.

Lokalna droga radiowa jest podstawowym elementem APRS.

## Najczęstsze błędne wyobrażenia

### Każdy pakiet musi przejść przez digipeater

Nie.

Jeżeli odbiorca znajduje się w bezpośrednim zasięgu nadajnika, może odebrać pakiet bez żadnej retransmisji.

### Digipeater przekazuje pakiety do Internetu

Nie musi.

Podstawową funkcją digipeatera jest retransmisja pakietów przez radio.

Za połączenie z APRS-IS odpowiada funkcja IGate.

### IGate musi być digipeaterem

Nie.

IGate może wyłącznie odbierać ruch RF i przekazywać go do APRS-IS.

### Jeżeli pakiet nie pojawił się na APRS.fi, APRS nie zadziałał

Nie.

Pakiet mógł zostać poprawnie odebrany i wykorzystany lokalnie przez inne stacje.

### APRS-IS przekazuje cały ruch z powrotem na radio

Nie.

Ruch z APRS-IS na RF musi być ograniczany i kontrolowany.

### Pakiet ma jedną określoną trasę

Nie.

Ta sama transmisja może zostać odebrana przez wiele stacji i dotrzeć do infrastruktury różnymi drogami.

## Cały obraz

Uproszczony model działania APRS można przedstawić tak:

```text
                         +-> Stacja lokalna
                         |
[Stacja APRS] -- RF -----+-> [Digipeater] -- RF --> inne stacje
                         |
                         +-> [IGate]
                               |
                               | Internet
                               v
                            [APRS-IS]
                               |
                 +-------------+-------------+
                 |             |             |
                 v             v             v
             aplikacje       mapy         usługi
```

Najważniejsze jest jednak to, że żadna z tych dróg nie jest obowiązkowa.

APRS może działać:

```text
stacja -> stacja
```

```text
stacja -> digipeater -> stacja
```

```text
stacja -> IGate -> APRS-IS
```

albo wszystkimi tymi drogami jednocześnie.

## Najważniejsze do zapamiętania

**APRS jest systemem rozproszonym.**

Jedna transmisja może być odebrana przez wiele stacji jednocześnie.

**Digipeater zwiększa zasięg radiowy.**

Jego podstawową rolą jest retransmisja pakietów RF.

**IGate łączy lokalną sieć radiową z APRS-IS.**

Nie musi jednocześnie pełnić funkcji digipeatera.

**APRS-IS rozszerza zasięg informacji poza lokalną sieć radiową.**

Nie zastępuje jednak podstawowej komunikacji RF.

**Pakiet nie musi trafić do Internetu, aby spełnić swoje zadanie.**

Lokalny odbiór informacji może być jego najważniejszym celem.

**Jedna ramka może dotrzeć wieloma drogami.**

Dlatego duplikaty i ich eliminacja są naturalnym elementem działania sieci.

**Ruch z Internetu na RF musi być kontrolowany.**

Kanał radiowy ma ograniczoną przepustowość i nie może być traktowany jako kopia APRS-IS.

## Dalej

Po zrozumieniu drogi informacji przez sieć warto przejść do kolejnych elementów protokołu:

- zależności pomiędzy APRS i AX.25,
- budowy ramki AX.25,
- struktury pakietu APRS,
- adresów źródłowych i SSID,
- destination address i TOCALL,
- ścieżek digipeaterów,
- różnic pomiędzy RF i APRS-IS,
- mechanizmów third-party traffic,
- q-constructs,
- szczegółowych zasad pracy IGate.

Dopiero po połączeniu tych elementów widać pełny obraz tego, w jaki sposób informacja APRS przemieszcza się pomiędzy stacjami i różnymi częściami infrastruktury.
