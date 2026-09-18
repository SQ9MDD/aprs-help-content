---
title: Ręczna stacja APRS
description: APRS w radiotelefonie ręcznym, zastosowania w terenie, dostępny sprzęt i różnice między pełną obsługą APRS a prostym trackerem.
template: doc
tableOfContents: true
---

APRS w radiotelefonie ręcznym jest jednym z tych zastosowań, w których możliwości systemu stają się szczególnie użyteczne.

Zamiast osobnego trackera, komputera i radia możemy mieć przy sobie jedno niewielkie urządzenie, które jednocześnie służy do normalnej łączności głosowej oraz wymiany danych APRS.

W zależności od możliwości konkretnego radiotelefonu możemy:

- wysyłać własną pozycję,
- odbierać pozycje innych stacji,
- zobaczyć odległość i kierunek do innego operatora,
- odbierać i wysyłać wiadomości APRS,
- odbierać obiekty i informacje lokalne,
- przekazywać częstotliwość, na której jesteśmy dostępni,
- obserwować aktywność innych użytkowników znajdujących się w pobliżu.

To sprawia, że APRS w ręcznym radiu nie jest tylko dodatkiem do mapy. Może być dodatkowym kanałem informacji działającym niezależnie od sieci komórkowej.

## Gdzie ręczne APRS ma największy sens?

Szczególnie dobrze widać zalety APRS podczas pracy w terenie.

### Góry i turystyka

Podczas wędrówki możemy okresowo wysyłać własną pozycję, dzięki czemu inni krótkofalowcy korzystający z APRS widzą, gdzie się znajdujemy.

Jeżeli w pobliżu pracuje inna stacja APRS, radio może pokazać jej położenie, odległość i kierunek.

Może to być bardzo wygodne podczas:

- wspólnych wycieczek,
- aktywności SOTA,
- spotkań terenowych,
- zawodów,
- wypraw krótkofalarskich,
- poszukiwania innych operatorów znajdujących się w okolicy.

APRS nie zastępuje oczywiście sprzętu nawigacyjnego ani systemów ratunkowych, ale może być bardzo użytecznym dodatkowym źródłem informacji.

### Na wodzie

Podobna sytuacja występuje podczas żeglowania, kajakarstwa czy innych aktywności na wodzie.

Położenie stacji może być przesyłane drogą radiową bez konieczności korzystania z sieci komórkowej.

Operatorzy należący do jednej grupy mogą wzajemnie widzieć swoje pozycje, a jednocześnie korzystać z tego samego radiotelefonu do zwykłej łączności głosowej.

### W terenie

APRS jest również bardzo wygodny podczas wszelkiego rodzaju aktywności krótkofalarskich poza domem.

Przykładowo przyjeżdżając na miejsce testów lub spotkania możemy wysłać swoją pozycję i informację:

```text
QRV 145.500 MHz
```

Inny krótkofalowiec widzący naszą stację na APRS wie wtedy nie tylko **gdzie jesteśmy**, ale również **gdzie może nas zawołać**.

To jest jedna z najważniejszych różnic między APRS a zwykłym systemem śledzenia pozycji.

APRS ma pomagać operatorom odnajdywać się i nawiązywać łączność.

## APRS bez dostępu do Internetu

Do bezpośredniej wymiany informacji APRS pomiędzy stacjami nie jest potrzebny Internet.

```text
Radio A
   |
   |  RF
   |
Radio B
```

Jeżeli obie stacje znajdują się w swoim zasięgu, mogą wymieniać dane bezpośrednio.

Jeżeli pomiędzy nimi znajduje się digipeater:

```text
Radio A
   |
   v
Digipeater
   |
   v
Radio B
```

pakiet może zostać retransmitowany i dotrzeć znacznie dalej.

Internet, APRS-IS oraz iGate rozszerzają możliwości systemu, ale nie są niezbędne do jego lokalnego działania.

Właśnie dlatego APRS jest interesujący podczas pracy terenowej.

## Co potrafi ręczne radio APRS?

Tutaj pojawia się bardzo ważna różnica pomiędzy urządzeniami.

Określenie:

> **radio obsługuje APRS**

nie mówi jeszcze, jakie funkcje rzeczywiście oferuje.

Możliwości urządzeń są bardzo różne.

### Pełna stacja APRS

Najbardziej rozbudowane radiotelefony potrafią samodzielnie obsługiwać dużą część funkcji APRS.

Mogą oferować między innymi:

- wbudowany GPS,
- automatyczne wysyłanie pozycji,
- SmartBeaconing,
- odbiór i dekodowanie ramek innych stacji,
- listę odebranych stacji,
- wyświetlanie odległości i kierunku,
- wiadomości APRS,
- statusy,
- obiekty,
- informacje o częstotliwości,
- obsługę ścieżki APRS,
- dostęp do wbudowanego TNC.

W takim przypadku radio jest rzeczywistym, samodzielnym terminalem APRS.

Nie potrzebujemy telefonu ani komputera, żeby zobaczyć inne stacje czy odpowiedzieć na wiadomość.

## Klasyka: Kenwood i Yaesu

Przez wiele lat ręczne APRS kojarzyło się przede wszystkim z urządzeniami firm **Kenwood** oraz **Yaesu**.

Kenwood stworzył kilka generacji urządzeń bardzo mocno zintegrowanych z APRS. Współczesnym przykładem jest seria TH-D75.

Takie radio może nie tylko nadawać własną pozycję, ale również odbierać dane innych stacji, wyświetlać ich położenie, obsługiwać wiadomości i realizować inne funkcje APRS.

Yaesu również od lat oferuje ręczne radiotelefony z rozbudowaną obsługą APRS, na przykład z serii FT.

Dzięki temu APRS może działać całkowicie samodzielnie wewnątrz radiotelefonu.

## Coraz więcej sprzętu z APRS

Rynek radiotelefonów bardzo się jednak zmienił.

APRS przestał być funkcją spotykaną wyłącznie w kilku drogich urządzeniach japońskich producentów.

Coraz więcej radiotelefonów, również produkowanych przez firmy chińskie, oferuje GPS oraz funkcje opisywane przez producentów jako APRS.

To dobra zmiana.

Większa liczba urządzeń oznacza:

- niższy próg wejścia,
- większy wybór sprzętu,
- większą liczbę użytkowników APRS,
- większe zainteresowanie transmisją danych w radiotelefonach ręcznych.

Trzeba jednak zwrócić uwagę na bardzo istotny szczegół.

## "Ma APRS" nie zawsze oznacza to samo

W przypadku zakupu radiotelefonu nie warto opierać się wyłącznie na informacji:

> **APRS supported**

umieszczonej w tabeli parametrów.

W jednym urządzeniu może to oznaczać rozbudowany terminal APRS.

W innym jedynie możliwość okresowego wysyłania własnej pozycji.

To ogromna różnica.

### Tracker APRS

Najprostszy wariant wygląda tak:

```text
GPS
 |
Radio
 |
pozycja APRS
 |
RF
```

Radio pobiera własną pozycję z GPS i co pewien czas wysyła beacon APRS.

Inne stacje oraz iGate mogą go odebrać.

Dla użytkownika urządzenia funkcjonalność może się jednak na tym kończyć.

Radio może nie potrafić:

- dekodować APRS od innych stacji,
- wyświetlać listy stacji,
- pokazywać ich położenia,
- odbierać wiadomości,
- wysyłać wiadomości,
- obsługiwać obiektów,
- realizować części bardziej zaawansowanych funkcji protokołu.

Takie urządzenie jest w praktyce przede wszystkim **trackerem APRS wbudowanym w radiotelefon**.

I nie ma w tym nic złego, jeżeli właśnie tego potrzebujemy.

Problem pojawia się dopiero wtedy, gdy kupujemy je oczekując pełnego terminala APRS.

## APRS analogowy i rozwiązania zależne od systemu cyfrowego

Warto również sprawdzić, **w jaki sposób radio realizuje APRS**.

Niektóre urządzenia mogą wysyłać pozycję klasycznym APRS AFSK na kanale analogowym.

Inne przekazują dane pozycyjne w ramach używanego systemu cyfrowego albo wymagają dodatkowej infrastruktury.

Z punktu widzenia użytkownika efekt może wyglądać podobnie: pozycja pojawia się na mapie.

Technicznie mogą to być jednak zupełnie różne rozwiązania.

Jeżeli chcemy korzystać z klasycznej lokalnej sieci APRS na 2 m, należy upewnić się, że radio rzeczywiście obsługuje APRS przez analogowy kanał radiowy.

## Co sprawdzić przed zakupem?

Jeżeli jednym z powodów zakupu radia jest APRS, warto przed zakupem odpowiedzieć sobie na kilka pytań.

### Czy radio nadaje klasyczne APRS przez RF?

Nie tylko przez Internet, aplikację lub sieć cyfrową.

### Czy odbiera APRS?

To jedna z najważniejszych różnic pomiędzy terminalem APRS a trackerem.

### Czy pokazuje odebrane stacje?

Dobrze, jeżeli możemy zobaczyć:

- znak stacji,
- odległość,
- kierunek,
- symbol,
- komentarz lub status.

### Czy obsługuje wiadomości APRS?

Najlepiej sprawdzić osobno:

- odbieranie wiadomości,
- wysyłanie wiadomości,
- potwierdzenia ACK.

### Czy można ustawić ścieżkę?

Przykładowo:

```text
WIDE1-1,WIDE2-1
```

Nie wszystkie urządzenia dają użytkownikowi pełną kontrolę nad parametrami APRS.

### Czy obsługuje SmartBeaconing?

Przy pracy pieszej lub mobilnej inteligentne dobieranie częstotliwości wysyłania pozycji może znacząco ograniczyć niepotrzebny ruch radiowy.

### Czy APRS działa bez telefonu?

Jeżeli zależy nam na samodzielnej pracy terenowej, jest to szczególnie ważne pytanie.

Niektóre urządzenia potrzebują aplikacji w telefonie do obsługi części funkcji.

### Czy producent rzeczywiście opisuje implementację APRS?

Warto zajrzeć do instrukcji urządzenia przed zakupem.

Sama obecność słowa `APRS` w opisie sklepu jest zdecydowanie zbyt małą ilością informacji.

## Telefon jako uzupełnienie radia

Smartfon i APRS nie muszą się wzajemnie wykluczać.

Niektóre radiotelefony mogą współpracować z telefonem przez Bluetooth.

Telefon może wtedy zaoferować wygodniejszy:

- interfejs,
- ekran mapy,
- sposób wpisywania wiadomości,
- podgląd odebranych stacji.

Radio nadal realizuje właściwą transmisję drogą radiową.

Może to być bardzo wygodny kompromis pomiędzy klasycznym ręcznym terminalem APRS a rozbudowanym zestawem terenowym.

## Ręczne APRS to coś więcej niż kropka na mapie

Najprostsze wykorzystanie APRS w radiu ręcznym to:

```text
GPS -> beacon -> mapa
```

ale możliwości systemu są znacznie większe.

Pełny terminal może pozwolić operatorowi stojącemu gdzieś w górach zobaczyć:

```text
SQ9ABC-7
3.2 km
kierunek: NE
QRV 145.500 MHz
```

i chwilę później zawołać tę stację głosowo.

Może odebrać krótką wiadomość od innego operatora.

Może zobaczyć obiekt informujący o miejscu spotkania.

Może dowiedzieć się, że kilka kilometrów dalej znajduje się inny krótkofalowiec.

Wtedy APRS przestaje być systemem śledzenia pozycji.

Staje się tym, czym miał być od początku: **lokalnym systemem wymiany informacji pomiędzy krótkofalowcami w czasie rzeczywistym**.

## Od czego zacząć?

Jeżeli masz już radio z pełną obsługą APRS:

1. ustaw swój znak i SSID,
2. włącz GPS,
3. ustaw właściwą częstotliwość APRS dla swojego regionu,
4. ustaw odpowiednią ścieżkę,
5. ustaw rozsądny beacon lub SmartBeaconing,
6. zacznij od obserwowania odbieranych stacji,
7. sprawdź wiadomości APRS,
8. dopiero potem dostosuj konfigurację do sposobu, w jaki rzeczywiście korzystasz z radia.

W Polsce i dużej części Europy klasyczny APRS na paśmie 2 m pracuje na:

```text
144.800 MHz FM
```

Jeżeli dopiero wybierasz sprzęt, najpierw ustal, czego oczekujesz.

Jeżeli chcesz tylko, żeby inni widzieli twoją pozycję, prosty tracker może być całkowicie wystarczający.

Jeżeli natomiast chcesz **korzystać z APRS jako systemu komunikacji i informacji**, szukaj urządzenia, które nie tylko nadaje własną pozycję, ale również odbiera i prezentuje dane innych stacji oraz obsługuje wiadomości APRS.
