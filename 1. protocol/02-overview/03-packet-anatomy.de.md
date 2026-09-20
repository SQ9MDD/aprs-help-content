---
title: Anatomie eines APRS-Pakets
description: Detaillierte Erklärung der Textdarstellung eines APRS-Pakets, der AX.25-Felder, des Pfads und der APRS-Daten.
template: doc
tableOfContents: true
---

Ein APRS-Paket wird sehr häufig in einer gut lesbaren Textform dargestellt:

```text
SOURCE>DEST,PATH:DATA
```

Beispiel:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Auf den ersten Blick sieht das wie ein einziges Format aus.

Tatsächlich kombiniert diese Darstellung in einer einzigen Zeile Informationen aus mehreren Elementen des AX.25-Frames mit den eigentlichen APRS-Daten.

Vereinfacht:

```text
SQ9MDD-7 > APRS , WIDE1-1 : !5012.34N/01956.78E>
^^^^^^^^   ^^^^   ^^^^^^^   ^^^^^^^^^^^^^^^^^^^^^
 SOURCE    DEST     PATH           DATA
  AX.25    AX.25    AX.25          APRS
```

Das Verständnis dieser Aufteilung ist die Grundlage für die weitere Analyse des Protokolls.

## Allgemeine Paketform

Die häufigste Textdarstellung eines APRS-Pakets hat die Form:

```text
SOURCE>DEST,PATH:INFORMATION
```

Nicht alle Elemente müssen immer vorhanden sein.

Ein Paket ohne Pfad kann so aussehen:

```text
SOURCE>DEST:INFORMATION
```

Beispiel:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

Ein Paket mit Pfad kann so aussehen:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:INFORMATION
```

Beispiel:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:>QRV 145.550 MHz
```

Logisch lässt es sich daher aufteilen in:

```text
SOURCE
DEST
PATH
INFORMATION
```

Die ersten drei Elemente gehören zur AX.25-Adressierung.

Das Feld `INFORMATION` enthält die eigentliche APRS-Information.

## Beispiel Schritt für Schritt

Betrachten wir das Paket:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Wir können es in folgende Teile zerlegen:

```text
SQ9MDD-7
```

Paketquelle,

```text
>
```

Trennzeichen der Textdarstellung,

```text
APRS
```

Destination-Adresse,

```text
,
```

Trennzeichen vor dem Pfad,

```text
WIDE1-1
```

Digipeater-Pfad,

```text
:
```

Trennzeichen zwischen Header und Information-Feld,

```text
!
```

Data Type Identifier,

```text
5012.34N/01956.78E>
```

eigentliche APRS-Daten.

Eine einzelne Zeile zeigt somit gleichzeitig Informationen der AX.25-Schicht und Daten des APRS-Protokolls.

## SOURCE

Das erste Element ist die Quelladresse:

```text
SOURCE
```

Beispiel:

```text
SQ9MDD-7
```

Bei typischem APRS ist dies das Rufzeichen der Station zusammen mit einer optionalen SSID.

Es kann zum Beispiel so erscheinen:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

SOURCE identifiziert die Station, die den AX.25-Frame erzeugt hat.

Es ist ein Element der AX.25-Adressierung und kein Teil des APRS-Information-Feldes.

## Rufzeichen und SSID

Eine AX.25-Adresse kann ein Rufzeichen und eine SSID enthalten.

Beispiel:

```text
SQ9MDD-7
```

kann logisch gelesen werden als:

```text
callsign: SQ9MDD
SSID:     7
```

Die SSID ermöglicht es, dasselbe Rufzeichen für mehrere logische Stationen zu verwenden.

Zum Beispiel kann ein Operator getrennte SSIDs verwenden für:

- eine Basisstation,
- eine Mobilstation,
- ein Handfunkgerät,
- einen Digipeater,
- ein IGate,
- einen Tracker.

Es gibt historische und praktische Konventionen zur Bedeutung einzelner SSIDs, sie sollten jedoch nicht als verbindlicher Teil des Protokolls zur Festlegung der Gerätefunktion betrachtet werden.

Details zu Rufzeichen und SSIDs werden separat beschrieben.

## Das Trennzeichen `>`

Nach der Quelladresse erscheint in der Textdarstellung das Zeichen:

```text
>
```

Zum Beispiel:

```text
SQ9MDD-7>APRS
```

Es trennt SOURCE von DEST.

Es ist ein Element der Textdarstellung des Pakets.

Das Zeichen `>` darf nicht als separates Byte an dieser Stelle des über Funk übertragenen rohen AX.25-Frames verstanden werden.

## DEST

Nach dem Zeichen `>` folgt die Destination-Adresse.

Beispiel:

```text
APRS
```

Zusammen:

```text
SQ9MDD-7>APRS
```

In AX.25 ist dies die Destination-Adresse.

Im klassischen Packet Radio kann sie die Zielstation kennzeichnen.

Bei APRS ist ihre Bedeutung häufig anders.

## Destination in APRS

In vielen APRS-Paketen bezeichnet das Destination-Feld nicht den tatsächlichen Empfänger der Information.

Es kann unter anderem verwendet werden zur Identifikation von:

- Gerätetyp,
- Hersteller,
- Software,
- Anwendungsfamilie,
- Art der Paketerzeugung.

Zu diesem Zweck verwendet APRS unter anderem das **TOCALL**-System.

Beispielwerte können so aussehen:

```text
APRS
APDWxx
APRSxx
```

Ihre genaue Bedeutung hängt von der TOCALL-Zuweisung ab.

Daher bedeutet das Paket:

```text
SQ9MDD-7>APRS:...
```

nicht:

```text
SQ9MDD-7 sendet Daten an die Station APRS
```

Das Destination-Feld ist hier in erster Linie Teil des AX.25-Headers und wird von APRS zusätzlich zur Identifikation genutzt.

## Destination ist nicht der Empfänger einer APRS-Nachricht

Das ist einer der wichtigsten Punkte.

Betrachten wir:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Hallo
```

Die AX.25-Destination lautet:

```text
APRS
```

der Nachrichtenempfänger ist jedoch:

```text
SP9XYZ
```

Der Empfänger befindet sich innerhalb der APRS-Daten.

Wir haben also zwei unterschiedliche Begriffe:

```text
AX.25 destination
```

und:

```text
APRS message addressee
```

Sie dürfen nicht miteinander verwechselt werden.

## PATH

Nach der Destination kann ein Pfad folgen:

```text
SOURCE>DEST,PATH:DATA
```

Beispiel:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

Der Pfad kann ein oder mehrere Elemente enthalten:

```text
WIDE1-1
WIDE2-1
```

In der Textdarstellung werden sie durch Kommas getrennt.

## PATH ist ein AX.25-Element

Der Pfad befindet sich nicht im APRS-Payload.

Er stellt die Liste der AX.25-Digipeater-Adressen dar.

Logisch:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:DATA
```

kann verstanden werden als:

```text
AX.25:
    SOURCE
    DEST
    DIGI 1
    DIGI 2

APRS:
    DATA
```

Das ist bei der Paketanalyse wichtig.

`WIDE1-1` ist kein Befehl innerhalb der APRS-Daten.

Es gehört zur Adressierung des AX.25-Frames.

## Mehrere Pfadelemente

Der Pfad kann mehrere Adressen enthalten.

Beispiel:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

oder nach dem Durchlaufen der Infrastruktur:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:...
```

Das Zeichen:

```text
*
```

in der Monitordarstellung kann anzeigen, dass das betreffende Pfadelement bereits verwendet wurde.

Details zum H-Bit, zu `WIDE`-Aliasen, zum New-N Paradigm und zur Pfadverarbeitung werden in einem eigenen Artikel beschrieben.

## Das Trennzeichen `:`

Die wichtigste Grenze in der Textdarstellung eines Pakets ist:

```text
:
```

Beispiel:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Alles vor dem ersten `:` bildet die Textdarstellung der Header-bezogenen Informationen.

Danach folgt das Information-Feld.

Wir können daher schreiben:

```text
SOURCE>DEST,PATH : INFORMATION
^^^^^^^^^^^^^^^^   ^^^^^^^^^^^
     header           data
```

Für die APRS-Analyse beginnt rechts von diesem Trennzeichen der eigentliche APRS-Inhalt.

## INFORMATION

Das Feld nach dem `:` enthält die Daten aus dem AX.25-Information-Feld.

Beispiel:

```text
!5012.34N/01956.78E>
```

Hier befindet sich das eigentliche APRS-Datenprotokoll.

Der Inhalt kann unter anderem darstellen:

- Position,
- Nachricht,
- Status,
- Objekt,
- Item,
- Wetter,
- Telemetrie,
- Abfrage,
- Stationsfähigkeiten,
- spezielle Daten.

Die Interpretation des Feldes hängt vor allem von seinem Anfang ab.

## Data Type Identifier

Das erste Zeichen des APRS-Information-Feldes ist sehr häufig der **Data Type Identifier**, kurz DTI.

Beispiel:

```text
!5012.34N/01956.78E>
^
|
DTI
```

Der DTI bestimmt, wie die folgenden Daten interpretiert werden.

Beispiele für DTIs:

| DTI | Allgemeine Bedeutung |
|---|---|
| `!` | Position ohne Zeitstempel |
| `=` | Position ohne Zeitstempel, mit Messaging-Unterstützung |
| `/` | Position mit Zeitstempel |
| `@` | Position mit Zeitstempel, mit Messaging-Unterstützung |
| `:` | Nachricht |
| `;` | Objekt |
| `)` | Item |
| `>` | Status |
| `?` | Abfrage |
| `_` | Wetterdaten ohne Position |
| `T` | Telemetrie |
| `}` | third-party traffic |

Dies ist keine vollständige Tabelle aller möglichen Werte.

Eine vollständige DTI-Übersicht befindet sich im Artikel über APRS-Frame-Typen.

## Der DTI reicht nicht immer für die vollständige Interpretation

Das Erkennen des ersten Zeichens ist nur der Beginn der Paketanalyse.

Beispiel:

```text
!5012.34N/01956.78E>
```

DTI:

```text
!
```

zeigt eine Position ohne Zeitstempel an.

Erst die folgenden Zeichen bestimmen:

- geografische Breite,
- Symboltabelle,
- geografische Länge,
- Symbol,
- optionale Erweiterungen,
- Kommentar.

Ein APRS-Parser muss daher zuerst den Datentyp erkennen und anschließend die dafür geltenden Regeln anwenden.

## Positionsbeispiel

Das Paket:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

kann wie folgt zerlegt werden:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

```text
!
```

DTI für eine Position ohne Zeitstempel,

```text
5012.34N
```

geografische Breite,

```text
/
```

Kennung der Symboltabelle,

```text
01956.78E
```

geografische Länge,

```text
>
```

Symbolcode.

In diesem Beispiel kann das gesamte Paket so betrachtet werden:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E >
SOURCE      DEST    PATH      DTI LATITUDE   LONGITUDE  SYMBOL
```

## Das Symbol ist Teil des Positionsformats

In einem APRS-Positionspaket wird das Symbol nicht als Name übertragen:

```text
car
house
repeater
```

Stattdessen wird es durch Zeichen an bestimmten Stellen des Positionsformats festgelegt.

Bei einer klassischen unkomprimierten Position sind wichtig:

- das Zeichen der Symboltabelle,
- der Symbolcode.

Daher ist:

```text
/
```

innerhalb eines Positionspakets kein Texttrennzeichen wie `>` oder `:`.

Es ist Teil der APRS-Daten und für die Interpretation des Symbols relevant.

## Nachrichtenbeispiel

Betrachten wir:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

Die Aufteilung sieht so aus:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

und das Information-Feld:

```text
:SP9XYZ   :Test
```

beginnt mit:

```text
:
```

also dem Nachrichten-DTI.

Danach folgt das Empfängerfeld:

```text
SP9XYZ
```

und der Inhalt:

```text
Test
```

Beachtenswert sind die zwei verschiedenen Doppelpunkte:

```text
...WIDE1-1::SP9XYZ   :Test
           ^^
```

Der erste:

```text
:
```

trennt den Header von Information.

Der zweite:

```text
:
```

ist der APRS-Nachrichten-DTI.

Diese Wiederholung ist nicht zufällig.

## Statusbeispiel

Das Paket:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

enthält:

```text
>
```

als erstes Zeichen des Information-Feldes.

Hier ist `>` der Status-DTI.

Das zeigt, warum dasselbe Zeichen je nach Position unterschiedliche Bedeutungen haben kann.

Im Header:

```text
SOURCE>DEST
```

ist `>` ein Trennzeichen der Textdarstellung.

In Information:

```text
>QRV 145.550 MHz
```

ist `>` ein APRS-DTI.

Ein Parser muss immer den Kontext berücksichtigen.

## Objektbeispiel

Ein Objektpaket beginnt in Information mit:

```text
;
```

Schematisch:

```text
SOURCE>DEST,PATH:;OBJECTNAM*...
                 ^
                 |
                DTI
```

Nach dem DTI folgen objektspezifische Felder, darunter Name, Status, Zeit und Position.

Die genaue Objektsyntax wird in einem eigenen Teil der Dokumentation beschrieben.

## Kommentar

Viele APRS-Formate erlauben nach den Grunddaten einen zusätzlichen Kommentar.

Zum Beispiel kann ein Positionspaket enthalten:

```text
!5012.34N/01956.78E>QRV 145.550 MHz
```

Der erste Teil definiert Position und Symbol.

Der folgende Teil:

```text
QRV 145.550 MHz
```

ist der Kommentar.

Der Kommentar kann auch bestimmte APRS-Datenerweiterungen enthalten.

Daher sollte nicht alles nach der Position automatisch als beliebiger Freitext behandelt werden.

## Datenerweiterungen

Nach der grundlegenden APRS-Position können zusätzliche Informationen erscheinen.

Je nach Format und Kontext können sie unter anderem beschreiben:

- Kurs und Geschwindigkeit,
- Höhe,
- PHG,
- Reichweite,
- Frequenz,
- tone,
- offset,
- DAO,
- weitere Erweiterungen.

Ein Beispiel kann daher deutlich umfangreicher sein als eine einfache Position:

```text
SOURCE>DEST,PATH:POSITION + EXTENSION + COMMENT
```

Die genauen Regeln hängen vom jeweiligen Datentyp ab und werden in den entsprechenden Dokumentationsabschnitten beschrieben.

## Die Länge des Information-Feldes ist wichtig

APRS arbeitet in einer Umgebung mit begrenzter Bandbreite, besonders auf dem klassischen 1200-Baud-VHF-Kanal.

Jedes zusätzliche Zeichen verlängert die Aussendung.

Ein gut gestaltetes Paket sollte daher die benötigte Information ohne unnötige Verlängerung übertragen.

Das gilt besonders für:

- Kommentare,
- Positionsbeacons,
- Objekte,
- Statusmeldungen,
- Nachrichten.

Ein langes Paket belegt den Kanal länger als ein kurzes.

## Der im Monitor sichtbare Text ist TNC2-Format

Die Form:

```text
SOURCE>DEST,PATH:INFORMATION
```

wird üblicherweise als TNC2-Monitorformat oder TNC2-Darstellung bezeichnet.

Es wird unter anderem verwendet von:

- Terminals,
- APRS-Software,
- Logs,
- APRS-IS-Servern,
- Diagnosewerkzeugen.

Damit kann ein AX.25-Frame in einer für Menschen leicht lesbaren Form dargestellt werden.

Das bedeutet jedoch nicht, dass genau diese Zeichenfolge über Funk übertragen wurde.

## Was befindet sich tatsächlich in einem AX.25-Frame?

Auf RF werden Adressen nicht als wörtlicher Text übertragen:

```text
SQ9MDD-7>APRS,WIDE1-1
```

Ein AX.25-Frame besitzt eine eigene binäre Codierung für Adressen und Steuerfelder.

Er enthält unter anderem:

```text
Destination
Source
Digipeater addresses
Control
PID
Information
FCS
```

Bei typischem APRS:

```text
Control = UI
PID     = no layer 3
```

Das Information-Feld enthält hingegen die APRS-Daten.

Die TNC2-Darstellung blendet viele Details der AX.25-Schicht aus, damit das Paket leichter analysiert werden kann.

## FCS ist in TNC2 nicht sichtbar

Ein realer Funkframe enthält mit FCS einen Mechanismus zur Fehlerprüfung.

In einer typischen Zeile ist er jedoch nicht sichtbar:

```text
SOURCE>DEST,PATH:DATA
```

Empfänger, TNC oder Modem prüfen die Gültigkeit des Frames vorher.

Wenn der Frame akzeptiert und an die Anwendung übergeben wird, enthält die Monitordarstellung normalerweise kein FCS.

Dies ist ein weiteres Beispiel für den Unterschied zwischen dem realen AX.25-Frame und seiner Textdarstellung.

## Direkt empfangenes Paket

Beispiel eines direkt auf RF gesehenen Pakets:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Wir können es so interpretieren:

```text
SOURCE: SQ9MDD-7
DEST:   APRS
PATH:   WIDE1-1
DATA:   !5012.34N/01956.78E>
```

Das ist einer der einfachsten Analysefälle.

## Paket nach einem Digipeater

Nach der Wiederholung kann die Darstellung Informationen über das verwendete Pfadelement enthalten.

Zum Beispiel:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:!5012.34N/01956.78E>
```

Im Monitor kann dann sichtbar sein, über welche Station das Paket wiederholt wurde.

Das bedeutet jedoch nicht, dass sich die APRS-Daten:

```text
!5012.34N/01956.78E>
```

geändert haben.

Geändert hat sich die Information zum AX.25-Transport.

## Paket in APRS-IS

Nach der Weiterleitung durch ein IGate kann das Paket beispielsweise so aussehen:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Hier sehen wir die Elemente:

```text
qAR
SQ9MDD-4
```

die zu APRS-IS gehören.

Daher darf die gesamte Zeichenfolge zwischen Destination und `:` nicht ausschließlich als Funk-AX.25-Pfad interpretiert werden.

In APRS-IS kann die Paketdarstellung auch Informationen enthalten, die von der Internetinfrastruktur hinzugefügt wurden.

## Grenze zwischen RF und APRS-IS

Vergleichen wir:

### RF-Paket

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

### In APRS-IS sichtbares Paket

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Die eigentliche APRS-Information:

```text
!5012.34N/01956.78E>
```

bleibt gleich.

Geändert werden die Informationen, die beschreiben, wie das Paket die jeweilige Umgebung erreicht hat.

## Third-party packet

APRS besitzt außerdem ein Format, mit dem ein vollständiges Paket in ein anderes Paket eingebettet werden kann.

DTI:

```text
}
```

bedeutet third-party traffic.

Schematisch:

```text
SOURCE>DEST,PATH:}ORIGINAL>DEST,PATH:DATA
                 ^
                 |
          third-party DTI
```

Nach dem Zeichen `}` folgt die Darstellung des ursprünglichen Pakets.

Damit befindet sich innerhalb des Information-Feldes eines Frames eine weitere Paketdarstellung.

Ein Parser muss diesen Fall erkennen und die verschachtelte Information entsprechend verarbeiten.

## Ein Paket kann verschachtelte Informationen enthalten

Third-party traffic zeigt eine wichtige Eigenschaft von APRS.

Ein Paket kann nicht immer mit der einfachen Annahme analysiert werden:

```text
finde das erste Zeichen
lies den Rest als Daten
```

Einige Datentypen erzeugen eine zusätzliche interne Struktur.

Ein korrekter Parser muss daher das vom DTI vorgegebene Format erkennen und erst danach den restlichen Inhalt interpretieren.

## Bedeutung der Zeichenposition

Bei der APRS-Analyse ist nicht nur der Wert eines Zeichens wichtig, sondern auch seine Position.

Zum Beispiel kann das Zeichen:

```text
:
```

sein:

- Trennzeichen zwischen Header und Information in der TNC2-Darstellung,
- Nachrichten-DTI,
- Trennzeichen innerhalb des Nachrichtenformats.

Das Zeichen:

```text
>
```

kann sein:

- Trennzeichen zwischen SOURCE und DEST,
- Status-DTI,
- Symbolcode in Positionsdaten.

APRS sollte daher nicht durch die Suche nach einzelnen Zeichen ohne Berücksichtigung der Struktur analysiert werden.

## Reihenfolge beim Parsen

Ein praktischer Paketparser kann logisch in folgender Reihenfolge arbeiten.

Zuerst trennen:

```text
HEADER:INFORMATION
```

Dann aus dem Header erkennen:

```text
SOURCE
DEST
PATH
```

Danach aus dem Information-Feld:

```text
DTI
```

und anhand des DTI den passenden Datenparser auswählen.

Schema:

```text
Paket
  |
  +-> Header
  |     |
  |     +-> Source
  |     +-> Destination
  |     +-> Path
  |
  +-> Information
        |
        +-> DTI
              |
              +-> Positionsparser
              +-> Nachrichtenparser
              +-> Objektparser
              +-> Wetterparser
              +-> ...
```

Das ist deutlich sicherer als zu versuchen, das gesamte Paket als Zeichenkette mit fester Struktur zu interpretieren.

## Nicht jedes Positionspaket sieht gleich aus

Beispiel:

```text
!5012.34N/01956.78E>
```

ist eine klassische unkomprimierte Position.

APRS unterstützt jedoch auch andere Positionscodierungen, darunter:

- Position mit Zeitstempel,
- komprimierte Position,
- Mic-E,
- Genauigkeitserweiterungen.

Ein Parser sollte daher nicht annehmen, dass eine Position immer lesbare Koordinaten in der Form enthält:

```text
DDMM.mmN
DDDMM.mmE
```

Die detaillierten Positionsformate werden in einem eigenen Abschnitt beschrieben.

## Nicht jedes Information-Feld beginnt mit einem einfachen DTI

Die meisten üblichen Formate lassen sich leicht am ersten Zeichen erkennen.

Es gibt jedoch historische Formate, Spezialformate und Konstruktionen, die eine weitere Analyse erfordern.

Eine vollständige APRS-Implementierung muss daher nicht nur die häufigsten berücksichtigen:

```text
!
=
:
;
>
```

sondern den vollständigen Satz der durch Spezifikation und spätere Erweiterungen definierten Formate.

## Was sieht der Benutzer und was sieht das Modem?

Der Benutzer kann sehen:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Die APRS-Anwendung sieht logische Felder:

```text
source
destination
path
information
```

Der APRS-Parser sieht innerhalb von Information:

```text
DTI
payload
```

Die AX.25-Schicht arbeitet mit den tatsächlichen Frame-Feldern.

Das Modem arbeitet mit Bitstrom und Signal.

Das Funkgerät überträgt schließlich das RF-Signal.

Jede Schicht betrachtet also dasselbe Paket aus einer anderen Perspektive.

## Häufige Interpretationsfehler

### Alles vor `:` ist APRS

Nein.

Ein großer Teil dieser Information beschreibt AX.25-Header und Pfad.

### `WIDE1-1` ist Teil des APRS-Payloads

Nein.

Es ist ein AX.25-Pfadelement.

### Destination ist der Nachrichtenempfänger

Nicht immer.

Der Empfänger einer APRS-Nachricht befindet sich innerhalb von Information.

### Jedes `:` hat dieselbe Bedeutung

Nein.

Die Bedeutung hängt von der Position ab.

### Die Textform des Pakets entspricht exakt dem RF-Inhalt

Nein.

Sie ist eine Monitordarstellung des realen AX.25-Frames.

### Alles zwischen Destination und `:` stammt von RF

Nicht immer.

In APRS-IS können von der Infrastruktur hinzugefügte Elemente erscheinen, zum Beispiel q-constructs.

### Jede Position hat die Form DDMM.mmN/DDDMM.mmE

Nein.

APRS besitzt mehrere Positionsformate.

## Wie liest man ein APRS-Paket?

Für das Paket:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>QRV 145.550
```

sollte man schrittweise lesen.

### 1. Wer hat gesendet?

```text
SQ9MDD-7
```

### 2. Wie lautet die Destination?

```text
APRS
```

### 3. Wie lautet der Pfad?

```text
WIDE1-1
```

### 4. Wo beginnt Information?

Nach:

```text
:
```

### 5. Wie lautet der DTI?

```text
!
```

also eine Position ohne Zeitstempel.

### 6. Was sind die eigentlichen Daten?

```text
5012.34N/01956.78E>
```

Position und Symbol.

### 7. Gibt es weitere Informationen?

```text
QRV 145.550
```

Kommentar.

Mit diesem Ansatz können auch deutlich komplexere Pakete Schritt für Schritt analysiert werden.

## Das gesamte Paket als Schichten

Dasselbe Beispiel kann so dargestellt werden:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E > QRV 145.550
|          |      |          | |          |           | |
|          |      |          | |          |           | +-- Kommentar
|          |      |          | |          |           +---- Symbol
|          |      |          | |          +---------------- Länge
|          |      |          | +--------------------------- Breite
|          |      |          +----------------------------- DTI
|          |      +---------------------------------------- Pfad
|          +----------------------------------------------- Destination
+---------------------------------------------------------- Source
```

Auf Schichtebene:

```text
AX.25
+---------------------------------------------------+
| SOURCE | DEST | PATH | INFORMATION               |
+---------------------------------------------------+
                       |
                       v
APRS
+---------------------------------------------------+
| DTI | Pakettyp-Daten | Erweiterungen / Kommentar |
+---------------------------------------------------+
```

Dies ist das wichtigste Modell, das man sich merken sollte.

## Die wichtigsten Punkte

**Ein APRS-Textpaket kombiniert AX.25- und APRS-Informationen.**

Die Form:

```text
SOURCE>DEST,PATH:DATA
```

ist kein einziges flaches Format.

**SOURCE, DEST und PATH gehören zur AX.25-Adressierungsschicht.**

Sie sind nicht der eigentliche APRS-Payload.

**Das Information-Feld beginnt in der TNC2-Darstellung nach dem Trennzeichen `:`.**

Dort befinden sich die APRS-Daten.

**Das erste Zeichen von Information ist häufig der DTI.**

Er bestimmt, wie der Rest des Pakets interpretiert wird.

**Destination muss nicht der tatsächliche Empfänger sein.**

In APRS kann es ein Gerät oder eine Software identifizieren.

**Der Empfänger einer APRS-Nachricht befindet sich in Information.**

Er unterscheidet sich von der AX.25-Destination.

**Der Digipeater-Pfad ist nicht Teil der APRS-Daten.**

Er gehört zu AX.25.

**Die TNC2-Darstellung ist nicht der rohe Funkframe.**

Sie blendet einige AX.25-Felder und binäre Details aus.

**Ein in APRS-IS sichtbares Paket kann Informationen enthalten, die auf RF nicht vorhanden waren.**

q-constructs sind ein Beispiel.

**Die Bedeutung eines Zeichens hängt von seiner Position in der Struktur ab.**

Das Paket sollte daher schichtweise und entsprechend dem vom DTI vorgegebenen Format analysiert werden.

## Weiter

Nach dem Verständnis der Anatomie eines einzelnen Pakets können seine einzelnen Elemente genauer betrachtet werden.

Die nächsten Themen umfassen:

- source callsign,
- SSID,
- destination address,
- TOCALL,
- AX.25-Pfade,
- WIDE-Mechanismus,
- Data Type Identifier,
- Information-Feld,
- timestamp,
- Symbole,
- Datenerweiterungen,
- Positionsformate,
- Mic-E,
- Nachrichten,
- Objekte,
- Telemetrie,
- Wetter,
- third-party traffic,
- q-constructs.

Ab diesem Punkt können einzelne APRS-Typen analysiert werden, ohne Informationen aus AX.25, APRS und APRS-IS miteinander zu vermischen.
