---
title: Quelladresse, Rufzeichen und SSID
description: Wie APRS die Quelle eines Pakets über das AX.25-Feld Source Address identifiziert und welche Rolle Rufzeichen und SSID spielen.
template: doc
tableOfContents: true
---

In der textuellen Darstellung eines APRS-Frames steht an erster Stelle die Quelladresse:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

In diesem Beispiel ist:

```text
SQ9MDD-7
```

die **Source Address**, also die Quelladresse des AX.25-Frames. Sie identifiziert die Station, von der das Paket ursprünglich erzeugt wurde.

Das muss nicht die Station sein, von der das Paket unmittelbar über Funk empfangen wurde. Wenn ein Digipeater den Frame erneut aussendet, verweist die Source Address weiterhin auf die ursprüngliche Quelle.

Dieser Artikel behandelt ausschließlich diesen Teil der Adressierung. Die Elemente hinter dem Zeichen `>` werden separat beschrieben.

## Woraus besteht die Source Address?

Eine AX.25-Quelladresse besteht aus einem Rufzeichen und einer SSID:

```text
CALLSIGN-SSID
```

Für:

```text
SQ9MDD-7
```

sind dies:

```text
callsign: SQ9MDD
SSID:     7
```

**SSID** steht für **Secondary Station Identifier**.

Rufzeichen und SSID sind in AX.25 getrennte Bestandteile der Adresse. Der Bindestrich gehört nur zur textuellen Darstellung.

## Rufzeichen

Eine klassische AX.25-Adresse stellt für den Rufzeichenteil maximal sechs Stellen zur Verfügung.

Beispiele für gültige Werte:

```text
SQ9MDD
SP5ABC
N0CALL
AB1CDE
```

Die Zeichenfolge:

```text
SQ5AUTO
```

enthält sieben Zeichen und passt daher nicht in das Rufzeichenfeld einer klassischen AX.25-Adresse.

Die SSID zählt nicht zu diesem Limit, da sie getrennt codiert wird. Daher enthält:

```text
SQ9MDD-15
```

weiterhin das sechsstellige Rufzeichen `SQ9MDD`.

AX.25-Adressen verwenden Großbuchstaben und Ziffern. Software kann eine Eingabe in Kleinbuchstaben akzeptieren und in Großbuchstaben normalisieren.

## SSID

Mit der SSID lassen sich mehrere unterschiedliche Adressen mit demselben Basisrufzeichen bilden:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

Dies sind vier verschiedene AX.25-Adressen. Sie können unterschiedliche Geräte oder unterschiedliche Funktionen unter demselben Basisrufzeichen darstellen.

Der Wertebereich der SSID ist:

```text
0-15
```

Dies ergibt sich aus dem AX.25-Format, in dem vier Bits für diesen Wert vorgesehen sind.

Daher ist:

```text
SQ9MDD-15
```

eine gültige Adresse, während:

```text
SQ9MDD-16
```

außerhalb des von AX.25 vorgesehenen SSID-Bereichs liegt.

### SSID 0

Der Wert `0` wird in der textuellen Darstellung normalerweise weggelassen.

Daher bezeichnen:

```text
SQ9MDD
```

und:

```text
SQ9MDD-0
```

dieselbe AX.25-Adresse.

Im praktischen APRS-Betrieb ist fast immer die erste Form zu sehen.

## Warum ist die SSID wichtig?

Die vollständige Source Address identifiziert eine bestimmte logische Station.

Wenn mehrere Geräte mit demselben Basisrufzeichen gleichzeitig in APRS arbeiten sollen, können sie durch unterschiedliche SSIDs unterschieden werden. Jedes Gerät kann dann unabhängig seine eigene Position, seinen Status, Nachrichten oder andere Daten senden.

Verwenden zwei Geräte dieselbe Source Address, erscheinen sie auf der Adressierungsebene von APRS als dieselbe Paketquelle. Dadurch können beispielsweise Positionen, Statusmeldungen oder Stationsverläufe miteinander vermischt werden.

## Legt die SSID den Stationstyp fest?

Nicht eindeutig.

In der APRS-Praxis gibt es verbreitete Konventionen für bestimmte SSID-Nummern, zum Beispiel `-7` für tragbare oder Handfunkgeräte und `-9` für Mobilstationen.

Dabei handelt es sich um Betriebskonventionen und nicht um die grundlegende Funktion des SSID-Feldes.

Die wichtigste Aufgabe der SSID besteht darin, logische Stationsadressen voneinander zu unterscheiden. Der Gerätetyp sollte daher nicht allein aus der SSID-Nummer abgeleitet werden.

## Die Source Address kennzeichnet die Paketquelle

Die Source Address identifiziert die Station, von der das Paket ursprünglich stammt. Sie muss jedoch nicht dem Namen des Elements entsprechen, das durch die APRS-Daten beschrieben wird.

APRS-Objekte sind dafür ein gutes Beispiel. Eine Station:

```text
SQ9MDD-4
```

kann ein Paket senden, das ein Objekt mit dem Namen:

```text
REPEATER
```

beschreibt.

Die Source Address bleibt:

```text
SQ9MDD-4
```

während `REPEATER` der in den APRS-Daten enthaltene Objektname ist.

Die Source Address beantwortet somit die Frage:

**„Woher stammt dieses Paket?“**

Sie beantwortet nicht immer die Frage:

**„Was beschreiben die Daten in diesem Paket?“**

## Beispiele für Quelladressen

| Adresse | Gültig | Hinweise |
|---|---|---|
| `SQ9MDD` | ja | Rufzeichen `SQ9MDD`, SSID 0 |
| `SQ9MDD-0` | ja | dieselbe Adresse mit ausdrücklich angegebener SSID 0 |
| `SQ9MDD-7` | ja | SSID 7 |
| `SQ9MDD-15` | ja | höchster SSID-Wert |
| `SQ9MDD-16` | nein | SSID außerhalb des Bereichs 0-15 |
| `SQ5AUTO` | nein | das Rufzeichen enthält sieben Zeichen |

## Wie geht es weiter?

Im Beispiel-Frame:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

kennen wir nun die Bedeutung des ersten Elements:

```text
SQ9MDD-7
```

Der nächste Artikel beschreibt die **Destination Address**, also das Feld direkt hinter dem Zeichen `>`, und wie APRS dieses Feld unter anderem als TOCALL verwendet.
