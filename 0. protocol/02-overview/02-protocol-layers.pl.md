---
title: Warstwy protokołu APRS
description: Wyjaśnienie zależności pomiędzy APRS, AX.25, warstwą radiową, modemem i APRS-IS.
template: doc
tableOfContents: true
---

APRS nie jest pojedynczym protokołem obejmującym wszystko od sygnału radiowego aż po aplikację użytkownika.

W praktyce system składa się z kilku warstw, które współpracują ze sobą.

Na drodze radiowej można je przedstawić w uproszczeniu tak:

```text
aplikacja
   |
   v
dane APRS
   |
   v
AX.25
   |
   v
modem / modulacja
   |
   v
radio
   |
   v
RF
```

Każda z tych warstw odpowiada za coś innego.

APRS definiuje przede wszystkim **znaczenie informacji**.

AX.25 zapewnia strukturę ramki używanej do jej transportu przez radio.

Modem zamienia dane cyfrowe na sygnał możliwy do przesłania przez tor radiowy.

Radio przenosi ten sygnał przez kanał RF.

Po stronie Internetu ta sama informacja APRS może być transportowana inaczej:

```text
dane APRS
   |
   v
APRS-IS
   |
   v
TCP/IP
   |
   v
aplikacje
```

Zrozumienie tego podziału jest bardzo ważne, ponieważ wiele elementów widocznych w typowym pakiecie APRS należy w rzeczywistości do różnych warstw systemu.

## APRS nie jest wszystkim naraz

W codziennym języku słowo "APRS" jest często używane do opisania całego systemu:

- radia,
- modulacji,
- ramek AX.25,
- pozycji i wiadomości,
- digipeaterów,
- IGate,
- APRS-IS,
- aplikacji internetowych.

Jest to wygodne, ale technicznie nieprecyzyjne.

Na przykład transmisja:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

zawiera elementy należące do różnych warstw.

W dużym uproszczeniu:

```text
SQ9MDD-7      źródło AX.25
APRS          destination AX.25
WIDE1-1       ścieżka AX.25
!             identyfikator typu danych APRS
5012.34N...   dane APRS
```

Do tego dochodzą jeszcze informacje obecne w rzeczywistej ramce radiowej, których nie widać w takiej reprezentacji tekstowej.

## Uproszczony model warstw

Dla APRS na kanale radiowym można przyjąć następujący model:

```text
+-----------------------------+
| aplikacja użytkownika       |
+-----------------------------+
| dane APRS                   |
+-----------------------------+
| AX.25 UI                    |
+-----------------------------+
| modem / modulacja           |
+-----------------------------+
| radio                       |
+-----------------------------+
| kanał RF                    |
+-----------------------------+
```

Nie jest to formalny model OSI protokołu APRS.

Jest to praktyczny sposób pokazania, gdzie znajdują się poszczególne elementy systemu i za co odpowiadają.

## Warstwa radiowa

Najniżej znajduje się fizyczna transmisja radiowa.

To tutaj znaczenie mają między innymi:

- częstotliwość pracy,
- szerokość kanału,
- moc nadajnika,
- antena,
- propagacja,
- poziom sygnału,
- zakłócenia,
- współdzielenie kanału przez wiele stacji.

W europejskich sieciach APRS VHF bardzo często wykorzystywana jest częstotliwość:

```text
144.800 MHz
```

Sama częstotliwość nie jest jednak częścią protokołu APRS.

APRS może być transportowany również w innych pasmach i przy użyciu innych metod transmisji.

Częstotliwość należy więc do warstwy radiowej, a nie do struktury danych APRS.

## Modem i modulacja

Dane cyfrowe muszą zostać zamienione na sygnał, który może zostać przesłany przez radio.

W klasycznym APRS na VHF bardzo często stosuje się:

```text
1200 baud AFSK
```

z modulacją opartą na standardzie Bell 202.

W typowej implementacji wykorzystywane są dwa tony audio:

```text
1200 Hz
2200 Hz
```

Dane cyfrowe są zamieniane na odpowiednią sekwencję sygnałów audio, które następnie modulują nadajnik radiowy.

Po stronie odbiorczej proces przebiega w odwrotnym kierunku.

```text
RF
 |
 v
radio
 |
 v
audio
 |
 v
modem
 |
 v
dane cyfrowe
```

Modem może być:

- urządzeniem sprzętowym,
- częścią radiotelefonu,
- układem TNC,
- programem pracującym na komputerze.

Przykładem modemu programowego jest Dire Wolf.

Należy jednak pamiętać, że:

**AFSK 1200 nie jest APRS.**

Jest tylko jednym ze sposobów transportowania danych używanych przez APRS.

## AX.25

Nad warstwą modemu znajduje się **AX.25**.

AX.25 jest protokołem warstwy łącza danych używanym w packet radio.

To właśnie AX.25 definiuje strukturę ramki radiowej zawierającej między innymi:

- adres destination,
- adres source,
- opcjonalne adresy digipeaterów,
- pole control,
- pole PID,
- pole information,
- kontrolę poprawności ramki.

W uproszczeniu:

```text
+-------------+
| Destination |
+-------------+
| Source      |
+-------------+
| Digipeaters |
+-------------+
| Control     |
+-------------+
| PID         |
+-------------+
| Information |
+-------------+
| FCS         |
+-------------+
```

APRS nie tworzy własnej kompletnej struktury ramki radiowej od zera.

Zamiast tego wykorzystuje AX.25 jako mechanizm transportu danych.

## AX.25 UI

Typowy ruch APRS wykorzystuje ramki AX.25 typu **UI**, czyli:

**Unnumbered Information**

Nie należy mylić tego skrótu z określeniem *User Interface*.

Ramki UI pozwalają na przesyłanie informacji bez wcześniejszego zestawienia klasycznego połączenia AX.25.

Dzięki temu stacja może po prostu nadać informację:

```text
pozycja
status
obiekt
pogoda
telemetria
```

a wszystkie stacje znajdujące się w zasięgu mogą ją odebrać.

To bardzo dobrze odpowiada rozgłoszeniowemu charakterowi APRS.

## Gdzie zaczyna się właściwy APRS?

W przypadku typowej ramki APRS właściwe dane APRS znajdują się w polu **Information** ramki AX.25.

Można to przedstawić tak:

```text
AX.25
+------------------------------------------+
| adresy | control | PID | Information    |
+------------------------------------------+
                           |
                           v
                     dane APRS
```

To właśnie zawartość tego pola określa między innymi:

- pozycję,
- status,
- wiadomość,
- obiekt,
- pogodę,
- telemetrię,
- zapytanie,
- inne typy informacji APRS.

Pierwszy znak pola informacji często pełni funkcję **Data Type Identifier**, czyli DTI.

Przykładowo:

```text
!
=
/
@
:
;
>
?
```

mogą oznaczać różne typy danych.

Szczegółowe znaczenie poszczególnych DTI opisane jest w osobnej części dokumentacji.

## Przykład podziału pakietu

Rozważmy pakiet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Możemy podzielić go logicznie tak:

```text
SQ9MDD-7
```

to adres źródłowy AX.25.

```text
APRS
```

to destination address AX.25.

```text
WIDE1-1
```

to element ścieżki AX.25.

```text
:
```

oddziela w tekstowej reprezentacji nagłówek od pola informacji.

```text
!
```

to Data Type Identifier APRS.

```text
5012.34N/01956.78E>
```

to właściwa treść APRS opisująca pozycję i symbol.

Widać więc wyraźnie, że nie wszystkie elementy widocznego pakietu należą do tej samej warstwy.

## Destination address

Pole destination jest częścią nagłówka AX.25.

W klasycznym użyciu AX.25 destination może wskazywać stację docelową.

W APRS sytuacja jest bardziej złożona.

Wiele pakietów APRS wykorzystuje destination address do identyfikowania rodzaju urządzenia lub oprogramowania.

Przykład:

```text
SQ9MDD-7>APRS:...
```

Nie oznacza to, że pakiet jest kierowany do stacji o znaku `APRS`.

Podobnie destination może zawierać wartości należące do systemu TOCALL.

Dlatego destination address w APRS nie powinien być automatycznie interpretowany jako adres konkretnego odbiorcy.

Szczegółowe zasady destination address i TOCALL są opisane osobno.

## Adresat wiadomości APRS

To rozróżnienie jest szczególnie ważne dla wiadomości.

Przykładowo pakiet może wyglądać tak:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

Destination AX.25 nadal może być:

```text
APRS
```

natomiast rzeczywisty adresat wiadomości APRS:

```text
SP9XYZ
```

znajduje się wewnątrz pola informacji APRS.

Są to dwie różne warstwy adresowania.

## Ścieżka digipeaterów

Elementy takie jak:

```text
WIDE1-1
WIDE2-1
```

nie są częścią właściwego payloadu APRS.

Należą do listy adresów digipeaterów w ramce AX.25.

To właśnie ta warstwa pozwala digipeaterom określić, czy i w jaki sposób dana ramka może zostać retransmitowana.

W reprezentacji tekstowej widzimy to jako:

```text
SOURCE>DEST,PATH:DATA
```

ale logicznie:

```text
SOURCE
DEST
PATH
```

należą do nagłówka AX.25,

natomiast:

```text
DATA
```

jest polem informacji, w którym znajduje się APRS.

## Reprezentacja tekstowa nie jest surową ramką radiową

Pakiety APRS bardzo często przedstawiane są jako tekst:

```text
SOURCE>DEST,PATH:information
```

Jest to niezwykle wygodna reprezentacja dla użytkownika, logów i aplikacji.

Nie jest to jednak dokładny zapis bajt po bajcie tego, co znajduje się na kanale radiowym.

Rzeczywista ramka AX.25 zawiera elementy, które w reprezentacji tekstowej nie są widoczne bezpośrednio, między innymi:

- zakodowane adresy AX.25,
- bity sterujące,
- pole control,
- PID,
- FCS,
- mechanizmy związane z transmisją ramki.

Dlatego:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

należy traktować jako czytelną reprezentację pakietu, a nie literalną zawartość transmisji radiowej.

## APRS-IS

Po stronie Internetu pakiety APRS nie są przesyłane jako surowe radiowe ramki AX.25.

APRS-IS używa tekstowej reprezentacji pakietów podobnej do:

```text
SOURCE>DEST,PATH:information
```

Pakiet może więc zostać odebrany przez IGate po RF:

```text
AX.25
   |
   v
IGate
```

a następnie przekazany do APRS-IS jako reprezentacja tekstowa:

```text
SOURCE>DEST,PATH:information
```

Do pakietu mogą zostać dodane informacje charakterystyczne dla APRS-IS.

Przykładem są **q-constructs**.

## q-constructs nie są ścieżką radiową

W APRS-IS można zobaczyć pakiet taki jak:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:...
```

Element:

```text
qAR
```

nie został nadany przez stację przez radio jako kolejny element zwykłej ścieżki digipeaterów.

Jest to informacja dodana w środowisku APRS-IS.

Podobnie:

```text
SQ9MDD-4
```

w tej części może identyfikować IGate odpowiedzialny za przekazanie pakietu do APRS-IS.

Dlatego nie należy interpretować całej ścieżki widocznej w APRS-IS jako dokładnej kopii ścieżki obecnej na RF.

## RF i APRS-IS to różne środowiska transportu

Ta sama informacja APRS może być przenoszona różnymi drogami.

Na RF:

```text
APRS data
   |
   v
AX.25
   |
   v
modem
   |
   v
radio
```

W Internecie:

```text
APRS data
   |
   v
APRS-IS
   |
   v
TCP/IP
```

W obu przypadkach znaczenie informacji APRS może pozostać takie samo.

Zmienia się jednak sposób jej transportu.

To ważne rozróżnienie.

**APRS jest warstwą informacji, a RF i APRS-IS są różnymi środowiskami, przez które ta informacja może być przenoszona.**

## IGate jako granica pomiędzy środowiskami

IGate znajduje się pomiędzy siecią radiową i APRS-IS.

W kierunku RF -> Internet:

```text
RF
 |
 v
AX.25
 |
 v
IGate
 |
 v
APRS-IS
```

IGate odbiera ramkę radiową, interpretuje potrzebne elementy i przekazuje odpowiednią reprezentację pakietu do APRS-IS.

Schematycznie:

```text
[RF / AX.25]
      |
      v
    IGate
      |
      v
 [APRS-IS]
```

Nie jest to jednak zwykłe przekazywanie bitów z jednego interfejsu na drugi.

IGate działa na granicy dwóch różnych środowisk transportu.

## Kierunek APRS-IS -> RF

Przejście w przeciwnym kierunku jest bardziej złożone.

Nie można po prostu wziąć dowolnej linii tekstowej z APRS-IS i nadać jej bez zmian przez radio.

APRS posiada mechanizmy określające sposób przenoszenia wybranych informacji z APRS-IS na RF.

W szczególności może zostać wykorzystany format:

**third-party traffic**

Pozwala on zachować informację o pochodzeniu oryginalnego pakietu.

Szczegółowe zasady tego mechanizmu opisane są w części dotyczącej formatów specjalnych i pracy IGate.

## Third-party traffic

Mechanizm third-party traffic tworzy dodatkową warstwę enkapsulacji.

W dużym uproszczeniu:

```text
AX.25
 |
 v
APRS third-party packet
 |
 v
oryginalny pakiet
```

Oznacza to, że pakiet pochodzący z innego środowiska nie jest po prostu kopiowany jako zwykły pakiet RF.

Zostaje umieszczony wewnątrz specjalnego formatu APRS.

Dzięki temu odbiorca może rozpoznać, że informacja została przekazana przez inny element infrastruktury.

## Co należy do której warstwy?

Poniższa tabela pokazuje kilka typowych elementów APRS i ich miejsce w systemie.

| Element | Warstwa |
|---|---|
| `144.800 MHz` | radio / RF |
| `1200 baud` | modem / transmisja |
| AFSK | modulacja |
| Bell 202 | modulacja |
| AX.25 | warstwa łącza danych |
| Source callsign | AX.25 |
| Destination address | AX.25 |
| `WIDE1-1` | ścieżka AX.25 |
| `WIDE2-1` | ścieżka AX.25 |
| UI | typ ramki AX.25 |
| PID | AX.25 |
| FCS | AX.25 |
| `!` | APRS DTI |
| `=` | APRS DTI |
| `:` | APRS message DTI |
| pozycja | APRS |
| obiekt | APRS |
| wiadomość | APRS |
| telemetria | APRS |
| pogoda | APRS |
| TOCALL | wykorzystanie destination przez APRS |
| APRS-IS | transport internetowy APRS |
| `qAR` | APRS-IS |
| TCP/IP | transport internetowy |
| APRS.fi | aplikacja / usługa |

Takie rozdzielenie pomaga zrozumieć, dlaczego niektóre elementy pakietu występują wyłącznie na RF, inne wyłącznie w APRS-IS, a jeszcze inne zachowują swoje znaczenie w obu środowiskach.

## Te same dane, różne transporty

Załóżmy, że właściwa informacja APRS opisuje pozycję stacji.

Na RF może być ona transportowana tak:

```text
pozycja APRS
     |
     v
   AX.25
     |
     v
   AFSK
     |
     v
    RF
```

Po przejściu przez IGate:

```text
pozycja APRS
     |
     v
  APRS-IS
     |
     v
   TCP/IP
```

Znaczenie pozycji nie musi się zmienić.

Zmienia się jedynie mechanizm, którym informacja jest przenoszona.

## Aplikacja znajduje się jeszcze wyżej

Na samym końcu znajduje się aplikacja użytkownika.

Może to być:

- radiotelefon z dekoderem APRS,
- terminal APRS,
- aplikacja komputerowa,
- mapa,
- serwis internetowy,
- system monitorujący,
- baza danych.

Aplikacja interpretuje informacje APRS i przedstawia je użytkownikowi.

Przykładowo:

```text
RF
 |
 v
AX.25
 |
 v
APRS
 |
 v
aplikacja
 |
 v
mapa
```

albo:

```text
APRS-IS
   |
   v
aplikacja
   |
   v
mapa
```

Mapa jest więc ostatnią warstwą prezentacji informacji, a nie samym protokołem APRS.

## Typowe pomyłki

### APRS i AX.25 to to samo

Nie.

AX.25 zapewnia między innymi strukturę ramki i adresowanie warstwy łącza.

APRS wykorzystuje AX.25 do transportu własnych danych.

### AFSK 1200 to APRS

Nie.

AFSK 1200 jest jednym ze sposobów transmisji danych.

APRS znajduje się wyżej.

### WIDE1-1 jest częścią danych APRS

Nie.

`WIDE1-1` znajduje się w ścieżce adresowej AX.25.

### Destination address zawsze wskazuje odbiorcę APRS

Nie.

W APRS destination może pełnić również funkcję identyfikacji urządzenia lub oprogramowania.

### qAR jest elementem ścieżki radiowej

Nie.

`qAR` jest elementem związanym z APRS-IS.

### Tekstowa linia pakietu jest dokładną ramką radiową

Nie.

```text
SOURCE>DEST,PATH:DATA
```

jest czytelną reprezentacją informacji zawartych w ramce, ale nie zawiera wszystkich elementów rzeczywistej transmisji AX.25.

### APRS-IS jest internetową wersją AX.25

Nie wprost.

APRS-IS transportuje informacje APRS w środowisku internetowym, wykorzystując własny sposób reprezentacji i dystrybucji pakietów.

## Cały obraz

Warstwy transmisji radiowej można podsumować tak:

```text
+-----------------------------+
| Aplikacja                   |
+-----------------------------+
| APRS                        |
| pozycje, wiadomości,        |
| obiekty, pogoda, telemetria |
+-----------------------------+
| AX.25 UI                    |
| adresy, path, control, PID  |
+-----------------------------+
| modem / modulacja           |
| np. AFSK 1200               |
+-----------------------------+
| radio                       |
+-----------------------------+
| RF                          |
+-----------------------------+
```

Po stronie APRS-IS:

```text
+-----------------------------+
| Aplikacja                   |
+-----------------------------+
| APRS                        |
+-----------------------------+
| APRS-IS                     |
+-----------------------------+
| TCP/IP                      |
+-----------------------------+
| sieć internetowa            |
+-----------------------------+
```

Pomiędzy tymi środowiskami może pracować IGate:

```text
             RF                         Internet

+--------------------------+       +----------------------+
| APRS                     |       | APRS                 |
+--------------------------+       +----------------------+
| AX.25                    |       | APRS-IS              |
+--------------------------+       +----------------------+
| modem                    |       | TCP/IP               |
+--------------------------+       +----------------------+
| radio                    |       | Internet             |
+--------------------------+       +----------------------+
             \                         /
              \                       /
               +-------- IGate ------+
```

## Najważniejsze do zapamiętania

**APRS nie definiuje całej transmisji radiowej.**

Korzysta z innych warstw, przede wszystkim AX.25.

**AX.25 i APRS nie są tym samym.**

AX.25 transportuje dane, natomiast APRS określa znaczenie informacji znajdującej się w polu danych.

**AFSK 1200 nie jest APRS.**

Jest jednym ze sposobów przenoszenia ramek AX.25 przez radio.

**WIDE1-1 i podobne elementy należą do ścieżki AX.25.**

Nie są częścią właściwego payloadu APRS.

**Destination address jest elementem AX.25.**

APRS może wykorzystywać go dodatkowo do identyfikacji urządzenia lub oprogramowania.

**APRS-IS jest innym środowiskiem transportu informacji APRS.**

Nie przesyła surowych ramek radiowych 1:1.

**q-constructs należą do APRS-IS.**

Nie należy ich interpretować jako zwykłych elementów ścieżki radiowej.

**Reprezentacja tekstowa pakietu łączy w jednym wierszu informacje z kilku warstw.**

Dlatego:

```text
SOURCE>DEST,PATH:DATA
```

jest bardzo wygodnym zapisem dla człowieka i aplikacji, ale nie jest dosłownym obrazem całej transmisji radiowej.

## Dalej

Po zrozumieniu warstw systemu można przejść do dokładniejszej analizy pojedynczego pakietu.

Kolejne zagadnienia powinny obejmować:

- strukturę ramki AX.25,
- anatomię tekstowej reprezentacji pakietu APRS,
- source callsign i SSID,
- destination address,
- TOCALL,
- ścieżki digipeaterów,
- pole informacji APRS,
- Data Type Identifier,
- różnice pomiędzy pakietem RF i jego reprezentacją w APRS-IS,
- q-constructs,
- third-party traffic.

Dzięki temu kolejne elementy protokołu można analizować już ze świadomością, **do której warstwy systemu naprawdę należą**.
