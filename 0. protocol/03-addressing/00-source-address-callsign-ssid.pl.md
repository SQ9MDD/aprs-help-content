---
title: Adres źródłowy, znak wywoławczy i SSID
description: Jak APRS identyfikuje źródło pakietu za pomocą pola Source Address AX.25 oraz jaką rolę pełnią znak wywoławczy i SSID.
template: doc
tableOfContents: true
---

W tekstowym zapisie ramki APRS pierwszym elementem jest adres źródłowy:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

W tym przykładzie:

```text
SQ9MDD-7
```

jest **Source Address**, czyli adresem źródłowym ramki AX.25. Identyfikuje stację, która utworzyła pakiet.

Nie musi to być stacja, od której pakiet został odebrany bezpośrednio drogą radiową. Jeżeli ramkę retransmitował digipeater, Source Address nadal wskazuje jej pierwotne źródło.

W tym artykule zajmujemy się wyłącznie tą częścią adresowania. Elementy znajdujące się po znaku `>` zostaną opisane osobno.

## Z czego składa się Source Address?

Adres źródłowy AX.25 składa się ze znaku wywoławczego oraz SSID:

```text
CALLSIGN-SSID
```

Dla:

```text
SQ9MDD-7
```

są to:

```text
callsign: SQ9MDD
SSID:     7
```

**SSID** oznacza **Secondary Station Identifier**.

Callsign i SSID są w AX.25 osobnymi elementami adresu. Myślnik jest jedynie częścią jego tekstowej reprezentacji.

## Callsign

Klasyczny adres AX.25 przewiduje maksymalnie sześć pozycji na część callsign.

Przykładowe poprawne wartości:

```text
SQ9MDD
SP5ABC
N0CALL
AB1CDE
```

Ciąg:

```text
SQ5AUTO
```

ma siedem znaków, więc nie mieści się w polu callsign klasycznego adresu AX.25.

SSID nie wlicza się do tego limitu, ponieważ jest kodowane oddzielnie. Dlatego:

```text
SQ9MDD-15
```

nadal zawiera sześciopozycyjny callsign `SQ9MDD`.

W adresach AX.25 stosuje się wielkie litery i cyfry. Oprogramowanie może przyjąć zapis małymi literami i znormalizować go do wielkich.

## SSID

SSID pozwala utworzyć kilka różnych adresów wykorzystujących ten sam podstawowy znak wywoławczy:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

Są to cztery różne adresy AX.25. Mogą reprezentować różne urządzenia albo różne role pracujące pod tym samym podstawowym znakiem.

SSID ma zakres:

```text
0-15
```

Wynika to z formatu AX.25, w którym na tę wartość przeznaczone są cztery bity.

Dlatego:

```text
SQ9MDD-15
```

jest poprawnym adresem, natomiast:

```text
SQ9MDD-16
```

wykracza poza zakres SSID przewidziany przez AX.25.

### SSID 0

Wartość `0` zwykle nie jest zapisywana.

Dlatego:

```text
SQ9MDD
```

i:

```text
SQ9MDD-0
```

oznaczają ten sam adres AX.25.

W praktycznych zapisach APRS najczęściej spotkamy pierwszą formę.

## Dlaczego SSID ma znaczenie?

Pełny Source Address identyfikuje konkretną logiczną stację.

Jeżeli kilka urządzeń pracujących pod tym samym podstawowym znakiem ma działać w APRS jednocześnie, różne SSID pozwalają je rozróżnić. Każde z nich może wtedy niezależnie wysyłać własną pozycję, status, wiadomości lub inne dane.

Jeżeli dwa urządzenia użyją identycznego Source Address, na poziomie adresowania APRS będą wyglądały jak to samo źródło pakietów. Może to prowadzić między innymi do mieszania pozycji, statusów lub historii stacji.

## Czy numer SSID określa rodzaj stacji?

Nie w sposób bezwzględny.

W środowisku APRS istnieją popularne konwencje stosowania określonych numerów, na przykład `-7` dla urządzeń przenośnych czy `-9` dla stacji mobilnych.

Są to konwencje operatorskie, a nie podstawowa funkcja pola SSID.

SSID przede wszystkim rozróżnia adresy logicznych stacji. Na podstawie samego numeru nie należy więc zakładać, jakim urządzeniem jest dana stacja.

## Source Address wskazuje źródło pakietu

Source Address określa stację, która utworzyła pakiet, ale nie musi być nazwą elementu opisywanego przez dane APRS.

Dobrym przykładem są obiekty APRS. Stacja:

```text
SQ9MDD-4
```

może wysłać pakiet opisujący obiekt o nazwie:

```text
REPEATER
```

Source Address nadal wynosi:

```text
SQ9MDD-4
```

natomiast `REPEATER` jest nazwą obiektu znajdującą się w danych APRS.

Source Address odpowiada więc na pytanie:

**„Skąd pochodzi ten pakiet?”**

Nie zawsze odpowiada na pytanie:

**„Czego dotyczą dane znajdujące się w pakiecie?”**

## Przykłady adresów źródłowych

| Adres | Poprawny | Uwagi |
|---|---|---|
| `SQ9MDD` | tak | callsign `SQ9MDD`, SSID 0 |
| `SQ9MDD-0` | tak | ten sam adres z jawnym SSID 0 |
| `SQ9MDD-7` | tak | SSID 7 |
| `SQ9MDD-15` | tak | najwyższa wartość SSID |
| `SQ9MDD-16` | nie | SSID poza zakresem 0-15 |
| `SQ5AUTO` | nie | callsign ma siedem znaków |

## Co dalej?

W przykładowej ramce:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

znamy już znaczenie pierwszego elementu:

```text
SQ9MDD-7
```

Kolejny artykuł opisze **Destination Address**, czyli pole znajdujące się bezpośrednio po znaku `>`, oraz sposób, w jaki APRS wykorzystuje je między innymi jako TOCALL.
