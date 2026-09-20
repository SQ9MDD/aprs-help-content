---
title: SSID w AX.25 i APRS
description: Techniczne wyjaśnienie pola SSID w AX.25, konwencji APRS oraz różnic pomiędzy adresowaniem RF i APRS-IS.
template: doc
tableOfContents: true
---

SSID, **Secondary Station Identifier**, jest częścią adresu AX.25. APRS wykorzystuje to pole zgodnie z jego funkcją protokołową, a dodatkowo stosuje konwencje określające typowe przeznaczenie stacji.

Należy rozróżniać:

- SSID jako pole adresowe AX.25,
- rekomendacje SSID stosowane w APRS,
- historyczne zastosowania SSID,
- identyfikatory występujące wyłącznie po stronie APRS-IS lub innych systemów.

## SSID w AX.25

Adres AX.25 składa się z sześciu bajtów znaku oraz siódmego bajtu zawierającego między innymi czterobitowe pole SSID.

Dla adresu źródłowego lub docelowego:

```text
bit:   7 6 5 4 3 2 1 0
       C R R S S S S E
```

Dla adresu digipeatera:

```text
bit:   7 6 5 4 3 2 1 0
       H R R S S S S E
```

Znaczenie:

- `SSSS` - SSID,
- `R` - bity zarezerwowane,
- `C` - Command/Response,
- `H` - Has Been Repeated,
- `E` - Extension Bit.

SSID zajmuje cztery bity, dlatego zakres wartości wynosi `0..15`.

Ograniczenie dotyczy każdego rzeczywistego adresu AX.25, niezależnie od tego, czy występuje jako source, destination czy adres digipeatera.

## SSID w APRS

APRS nie zmienia formatu SSID AX.25. Dla stacji źródłowej przyjęto jednak konwencje pozwalające zasugerować przeznaczenie danej stacji.

Są to **rekomendacje**, nie element składni APRS ani reguła walidacyjna.

Klasyczne zalecenia APRS:

| SSID | Typowe zastosowanie |
|---|---|
| `-0` | główna stacja operatora, zwykle stacjonarna i zdolna do obsługi wiadomości |
| `-1` | dodatkowa stacja |
| `-2` | dodatkowa stacja |
| `-3` | dodatkowa stacja |
| `-4` | dodatkowa stacja |
| `-5` | inne sieci lub systemy |
| `-6` | aktywność specjalna, satelity, inne pasma itp. |
| `-7` | radiotelefon ręczny lub inna stacja przenośna |
| `-8` | łódź, kamper lub drugi główny mobile |
| `-9` | główna stacja mobilna, zwykle zdolna do komunikacji |
| `-10` | Internet, iGate, EchoLink, Winlink i podobne zastosowania |
| `-11` | balon, samolot, statek kosmiczny |
| `-12` | APRStt, DTMF, RFID, urządzenia, jednokierunkowe trackery |
| `-13` | stacja pogodowa |
| `-14` | pojazd zawodowego kierowcy lub podobne zastosowanie |
| `-15` | dodatkowa stacja |

`CALL-9` jest na poziomie AX.25 stacją o SSID równym `9`. Interpretacja jej jako głównej stacji mobilnej wynika z konwencji APRS.

Oprogramowanie nie powinno traktować tej tabeli jako podstawy do odrzucania pakietów ani wymuszania typu stacji.

## `-9` i `-12`

W klasycznych rekomendacjach APRS `-9` jest przeznaczone przede wszystkim dla głównej stacji mobilnej operatora, zwykle umożliwiającej również komunikację przez APRS lub głos.

`-12` jest odpowiednie między innymi dla jednokierunkowych trackerów.

Urządzenie zamontowane w samochodzie nie musi więc automatycznie używać `-9`. Jeżeli jest wyłącznie nadajnikiem pozycji, `-12` może lepiej opisywać jego rolę.

## Historyczne użycie SSID do wyboru symbolu

We wczesnym APRS SSID było używane również do wyboru symbolu dla trackerów przekazujących surowe dane NMEA-0183.

Mechanizm ten został później zastąpiony innymi metodami kodowania symbolu, między innymi rozwiązaniem `GPSxyz` wykorzystującym destination address oraz formatami pozycji APRS zawierającymi symbol bezpośrednio.

Współczesnego source SSID nie należy więc używać do określania symbolu stacji.

## Source, destination i digipeater SSID

Ramka:

```text
SQ9MDD-9>APRS-2,WIDE1-1:
```

zawiera trzy niezależne wartości SSID:

```text
SQ9MDD-9     source SSID = 9
APRS-2       destination SSID = 2
WIDE1-1      digipeater SSID = 1
```

Wszystkie są kodowane w ten sam sposób na poziomie AX.25, ale ich znaczenie w APRS zależy od miejsca występowania.

Rekomendacje typu `-7` handheld, `-9` mobile czy `-13` weather dotyczą przede wszystkim **source SSID**. Destination SSID oraz SSID w ścieżce mogą być wykorzystywane przez inne mechanizmy APRS i nie powinny być interpretowane według tabeli przeznaczenia stacji.

## APRS-IS a ograniczenia AX.25

APRS-IS używa tekstowej reprezentacji pakietów i dopuszcza sufiks po myślniku składający się z jednego lub dwóch znaków alfanumerycznych, przy zachowaniu pozostałych ograniczeń formatu APRS-IS.

Możliwy jest więc przykładowo identyfikator:

```text
SQ9MDD-D
```

Taki identyfikator może być używany przez stację istniejącą po stronie APRS-IS, na przykład przez bramkę lub system integrujący APRS z inną siecią.

Nie można go jednak zakodować jako natywnego adresu źródłowego AX.25 na RF, ponieważ pole SSID AX.25 zawiera wyłącznie czterobitową wartość `0..15`.

Bramka APRS-IS -> RF nie może więc zakładać, że każdy poprawny identyfikator APRS-IS da się bezpośrednio przenieść do pola adresowego AX.25.

## Third-party traffic

APRS posiada mechanizm **third-party traffic**, w którym oryginalny nagłówek i dane mogą zostać umieszczone w polu informacyjnym innej, poprawnej ramki AX.25.

Pozwala to przenieść informację o stacji pochodzącej z innej sieci, ale nie rozszerza przestrzeni adresowej AX.25. Identyfikator taki jak `SQ9MDD-D` może wystąpić w danych third-party, lecz nie staje się przez to natywnym adresem źródłowym AX.25.

## Identyfikatory spoza zakresu `0..15`

W dokumentacji APRS można spotkać historyczne przykłady sufiksów takich jak:

```text
-63
-tt
-ID
-A ... -Z
```

Były stosowane dla obiektów lub stacji generowanych przez systemy internetowe i inne sieci. Nie są dodatkowymi wartościami czterobitowego SSID AX.25.

Na poziomie natywnego adresu RF obowiązuje zakres `0..15`.

## Obiekty APRS

Nazwa obiektu APRS znajduje się w polu informacyjnym, a nie w polu adresowym AX.25, i może mieć do dziewięciu znaków.

Nazwy takie jak:

```text
TEST-63
TEST-ID
WX-A
```

mogą więc zawierać końcówki przypominające SSID, ale są one częścią nazwy obiektu i nie podlegają czterobitowemu ograniczeniu SSID AX.25.

## Konsekwencje dla bramek i oprogramowania

Przy implementacji APRS należy rozdzielić walidację natywnego adresu AX.25 od walidacji identyfikatora używanego po stronie APRS-IS.

Dla adresu transmitowanego bezpośrednio przez RF SSID musi mieścić się w zakresie `0..15`.

Nie należy automatycznie zakładać, że każdy identyfikator obecny w APRS-IS można:

- zamienić na adres AX.25,
- retransmitować na RF,
- użyć jako source address,
- przekazać przez KISS do TNC jako natywny adres ramki.

Ma to szczególne znaczenie dla bramek APRS-IS -> RF, integracji DMR/APRS i D-STAR/APRS, hotspotów, systemów telemetrycznych oraz aplikacji generujących stacje wyłącznie internetowe.

## Interpretacja SSID w oprogramowaniu

SSID może być używane jako pomocnicza informacja o przeznaczeniu stacji, ale nie powinno nadpisywać danych zawartych w pakiecie.

Jeżeli `CALL-9` nadaje konkretny symbol APRS, należy użyć symbolu z pakietu. Jeżeli `CALL-13` nie przesyła danych pogodowych, samo SSID nie powoduje, że pakiet staje się pakietem pogodowym.

Konwencje SSID służą przede wszystkim organizacji i identyfikacji stacji przez operatorów.

## Podsumowanie

Najważniejsze zasady:

```text
AX.25 SSID
    4 bity
    zakres 0..15

APRS source SSID
    wykorzystuje SSID AX.25
    może dodatkowo stosować rekomendacje 0..15

APRS-IS
    może zawierać identyfikatory niemające
    bezpośredniej reprezentacji w AX.25

obiekt APRS
    nazwa znajduje się w information field
    końcówka po myślniku nie musi być SSID

third-party traffic
    może przenosić tekstową reprezentację
    stacji spoza RF, ale nie rozszerza AX.25
```

Podczas analizy wartości zapisanej po myślniku należy najpierw ustalić, czy jest ona częścią rzeczywistego adresu AX.25, identyfikatorem APRS-IS czy fragmentem nazwy znajdującej się w polu informacyjnym.

## Źródła

- [AX.25 Link Access Protocol for Amateur Packet Radio, Version 2.2](https://www.ax25.net/AX25.2.2-Jul%2098-2.pdf)
- [APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [Bob Bruninga WB4APR, APRS SSID Recommendations](https://github.com/SQ9MDD/APRSBox/blob/main/APRS-SPEC/SSIDs.txt)
- [APRS-IS - Connecting to APRS-IS](https://www.aprs-is.net/connecting.aspx)
- [APRS-IS - Server Design](https://www.aprs-is.net/ServerDesign.aspx)
