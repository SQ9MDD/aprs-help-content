---
title: "Raporty pozycyjne"
---


Raporty pozycyjne są jednym z podstawowych typów danych APRS. Przekazują położenie stacji, a opcjonalnie także informacje o jej ruchu, charakterystyce lub stanie. Pozycja może pochodzić z odbiornika GNSS, zostać wprowadzona ręcznie albo być zaprogramowana na stałe. Raporty pozycyjne nadają zarówno stacje ruchome, jak i stacjonarne.


## Formaty raportów pozycyjnych


APRS definiuje kilka sposobów przekazywania pozycji:

- **Uncompressed Position**: współrzędne geograficzne zapisane w czytelnej postaci tekstowej.

- **Compressed Position**: współrzędne kodowane w Base91 w celu ograniczenia długości raportu.

- **Mic-E**: format kodujący część informacji pozycyjnej w adresie docelowym AX.25, a pozostałą część w polu informacyjnym.


Specyfikacja opisuje także przekazywanie pozycji w zdaniach NMEA i w raportach wykorzystujących lokator Maidenhead. Formaty te mają odrębne identyfikatory i zasady interpretacji.


## Identyfikatory raportów pozycyjnych


W klasycznych i skompresowanych raportach pierwszy znak pola informacyjnego, Data Type Identifier (DTI), wskazuje rodzaj raportu:

| DTI | Znaczenie |
|---|---|
| `!` | Pozycja bez znacznika czasu; stacja nie deklaruje obsługi wiadomości APRS. |
| `=` | Pozycja bez znacznika czasu; stacja deklaruje obsługę wiadomości APRS. |
| `/` | Pozycja ze znacznikiem czasu; stacja nie deklaruje obsługi wiadomości APRS. |
| `@` | Pozycja ze znacznikiem czasu; stacja deklaruje obsługę wiadomości APRS. |


DTI nie rozstrzyga, czy współrzędne mają postać klasyczną, czy skompresowaną. Format rozpoznaje się po strukturze dalszej części raportu. Mic-E używa osobnych identyfikatorów: `'` oraz `` ` ``.


## Budowa raportu


W zależności od formatu raport pozycyjny może zawierać następujące elementy:

| Element | Znaczenie |
|---|---|
| DTI | Identyfikator rodzaju danych. |
| Timestamp | Opcjonalny znacznik czasu. |
| Position | Współrzędne geograficzne. |
| Symbol | Identyfikacja symbolu graficznego stacji. |
| Position extension | Opcjonalne rozszerzenie pozycyjne. |
| Comment | Komentarz i dodatkowe informacje. |


Obecność, kolejność i kodowanie poszczególnych pól zależą od formatu raportu.


### Przykład raportu klasycznego


```text
SQ9MDD>APRS:!5003.50N/01956.00E-
```

W przykładzie `!` oznacza raport bez znacznika czasu, `5003.50N` i `01956.00E` są współrzędnymi, a `/` i `-` określają tablicę i kod symbolu.


## Informacje dodatkowe


Raport pozycyjny może zawierać również dane uzupełniające. Specyfikacja przewiduje między innymi:

- **Course/Speed**: kurs i prędkość.

- **PHG**: moc nadajnika, wysokość anteny, jej zysk i kierunkowość.

- **RNG**: deklarowany zasięg stacji.

- **BRG/NRQ**: informacje wykorzystywane w radiopelengacji.

- **Altitude**: wysokość, między innymi w rozszerzeniu `/A=`.


Dostępność i sposób kodowania tych danych zależą od formatu raportu; nie każde rozszerzenie można zastosować w każdym formacie.


## Dokładność i niejednoznaczność pozycji


Rozdzielczość zapisu współrzędnych nie jest równoznaczna z rzeczywistą dokładnością pomiaru. APRS przewiduje mechanizm **Position Ambiguity**, umożliwiający celowe wskazanie obszaru zamiast dokładnego punktu. Istnieje również rozszerzenie **DAO**, służące do przekazywania dodatkowej precyzji współrzędnych.

Mechanizmy te mają różne przeznaczenie i wymagają odrębnej interpretacji.


## Pozycja stacji a obiekty i elementy


Zwykły raport pozycyjny opisuje stację wskazaną w adresie źródłowym ramki. APRS obsługuje również **Objects** i **Items**, które pozwalają przekazywać pozycję nazwanego obiektu lub elementu, niezależnego od znaku stacji nadającej.

Obiekty i elementy wykorzystują mechanizmy kodowania pozycji APRS, ale mają własne formaty i zasady identyfikacji. Są opisane osobno.


## Dokumentacja szczegółowa


Szczegółowe artykuły obejmują klasyczny format pozycji, znaczniki czasu, Position Ambiguity, rozszerzenia pozycyjne, wysokość i precyzję, symbole, komentarze oraz formaty skompresowane, w tym Mic-E.


## Źródło


[APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF).