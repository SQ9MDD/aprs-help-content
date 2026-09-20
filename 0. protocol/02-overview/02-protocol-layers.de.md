---
title: Schichten des APRS-Protokolls
description: Erklärung der Beziehungen zwischen APRS, AX.25, Funkschicht, Modem und APRS-IS.
template: doc
tableOfContents: true
---

APRS ist kein einzelnes Protokoll, das alles vom Funksignal bis zur Benutzeranwendung umfasst.

In der Praxis besteht das System aus mehreren Schichten, die zusammenarbeiten.

Auf dem Funkweg können sie vereinfacht so dargestellt werden:

```text
Anwendung
   |
   v
APRS-Daten
   |
   v
AX.25
   |
   v
Modem / Modulation
   |
   v
Funkgerät
   |
   v
RF
```

Jede dieser Schichten ist für etwas anderes zuständig.

APRS definiert in erster Linie die **Bedeutung der Information**.

AX.25 stellt die Rahmenstruktur bereit, mit der diese Information über Funk transportiert wird.

Das Modem wandelt digitale Daten in ein Signal um, das über den Funkweg übertragen werden kann.

Das Funkgerät überträgt dieses Signal über den RF-Kanal.

Auf der Internetseite kann dieselbe APRS-Information anders transportiert werden:

```text
APRS-Daten
   |
   v
APRS-IS
   |
   v
TCP/IP
   |
   v
Anwendungen
```

Das Verständnis dieser Trennung ist sehr wichtig, weil viele Elemente, die in einem typischen APRS-Paket sichtbar sind, tatsächlich zu unterschiedlichen Schichten des Systems gehören.

## APRS ist nicht alles gleichzeitig

Im alltäglichen Sprachgebrauch wird das Wort "APRS" häufig für das gesamte System verwendet:

- Funk,
- Modulation,
- AX.25-Frames,
- Positionen und Nachrichten,
- Digipeater,
- IGates,
- APRS-IS,
- Internetanwendungen.

Das ist bequem, aber technisch ungenau.

Zum Beispiel enthält die Aussendung:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Elemente aus verschiedenen Schichten.

Sehr vereinfacht:

```text
SQ9MDD-7      AX.25-Quelle
APRS          AX.25-Destination
WIDE1-1       AX.25-Pfad
!             APRS-Datentypkennung
5012.34N...   APRS-Daten
```

Zusätzlich enthält ein realer Funkframe Informationen, die in dieser Textdarstellung nicht sichtbar sind.

## Vereinfachtes Schichtenmodell

Für APRS auf einem Funkkanal kann folgendes Modell verwendet werden:

```text
+-----------------------------+
| Benutzeranwendung           |
+-----------------------------+
| APRS-Daten                  |
+-----------------------------+
| AX.25 UI                    |
+-----------------------------+
| Modem / Modulation          |
+-----------------------------+
| Funkgerät                   |
+-----------------------------+
| RF-Kanal                    |
+-----------------------------+
```

Dies ist kein formales OSI-Modell des APRS-Protokolls.

Es ist eine praktische Darstellung, die zeigt, wo sich die einzelnen Elemente des Systems befinden und wofür sie zuständig sind.

## Funkschicht

Auf der untersten Ebene befindet sich die physische Funkübertragung.

Hier spielen unter anderem folgende Faktoren eine Rolle:

- Betriebsfrequenz,
- Kanalbandbreite,
- Sendeleistung,
- Antenne,
- Ausbreitungsbedingungen,
- Signalpegel,
- Störungen,
- gemeinsame Nutzung des Kanals durch viele Stationen.

In europäischen VHF-APRS-Netzen wird sehr häufig die Frequenz:

```text
144.800 MHz
```

verwendet.

Die Frequenz selbst ist jedoch kein Bestandteil des APRS-Protokolls.

APRS kann auch auf anderen Bändern und mit anderen Übertragungsverfahren transportiert werden.

Die Frequenz gehört daher zur Funkschicht und nicht zur APRS-Datenstruktur.

## Modem und Modulation

Digitale Daten müssen in ein Signal umgewandelt werden, das über Funk übertragen werden kann.

Bei klassischem APRS auf VHF wird sehr häufig verwendet:

```text
1200 baud AFSK
```

mit einer auf dem Bell-202-Standard basierenden Modulation.

Eine typische Implementierung verwendet zwei Audiotöne:

```text
1200 Hz
2200 Hz
```

Die digitalen Daten werden in eine entsprechende Folge von Audiosignalen umgewandelt, die anschließend den Funksender modulieren.

Auf der Empfangsseite läuft der Vorgang in umgekehrter Richtung.

```text
RF
 |
 v
Funkgerät
 |
 v
Audio
 |
 v
Modem
 |
 v
digitale Daten
```

Das Modem kann sein:

- ein Hardwaregerät,
- Teil eines Funkgeräts,
- ein TNC,
- Software auf einem Computer.

Dire Wolf ist ein Beispiel für ein Softwaremodem.

Wichtig ist jedoch:

**1200-Baud-AFSK ist nicht APRS.**

Es ist nur eine Möglichkeit, die von APRS verwendeten Daten zu transportieren.

## AX.25

Über der Modemschicht befindet sich **AX.25**.

AX.25 ist ein Protokoll der Sicherungsschicht, das im Packet Radio verwendet wird.

AX.25 definiert die Struktur des Funkframes, einschließlich unter anderem:

- Destination-Adresse,
- Source-Adresse,
- optionaler Digipeater-Adressen,
- Control-Feld,
- PID-Feld,
- Information-Feld,
- Fehlerprüfung des Frames.

Vereinfacht:

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

APRS erstellt keine eigene vollständige Funkrahmenstruktur von Grund auf.

Stattdessen nutzt es AX.25 als Mechanismus zum Transport der Daten.

## AX.25 UI

Typischer APRS-Verkehr verwendet AX.25-Frames des Typs **UI**, wobei UI bedeutet:

**Unnumbered Information**

Diese Abkürzung darf nicht mit *User Interface* verwechselt werden.

UI-Frames ermöglichen die Übertragung von Informationen, ohne vorher eine klassische AX.25-Verbindung aufzubauen.

Dadurch kann eine Station einfach Informationen wie:

```text
Position
Status
Objekt
Wetter
Telemetrie
```

aussenden, und alle Stationen innerhalb der Reichweite können sie empfangen.

Das passt sehr gut zum Broadcast-Charakter von APRS.

## Wo beginnt APRS selbst?

Bei einem typischen APRS-Frame befinden sich die eigentlichen APRS-Daten im **Information**-Feld des AX.25-Frames.

Das kann so dargestellt werden:

```text
AX.25
+------------------------------------------+
| Adressen | Control | PID | Information  |
+------------------------------------------+
                           |
                           v
                      APRS-Daten
```

Der Inhalt dieses Feldes definiert unter anderem:

- Position,
- Status,
- Nachricht,
- Objekt,
- Wetter,
- Telemetrie,
- Abfrage,
- andere APRS-Informationstypen.

Das erste Zeichen des Information-Feldes dient häufig als **Data Type Identifier**, kurz DTI.

Zum Beispiel:

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

können unterschiedliche Datentypen kennzeichnen.

Die genaue Bedeutung der einzelnen DTIs wird in einem eigenen Abschnitt der Dokumentation beschrieben.

## Beispiel für die Aufteilung eines Pakets

Betrachten wir das Paket:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Wir können es logisch wie folgt aufteilen:

```text
SQ9MDD-7
```

ist die AX.25-Quelladresse.

```text
APRS
```

ist die AX.25-Destination-Adresse.

```text
WIDE1-1
```

ist ein Element des AX.25-Pfads.

```text
:
```

trennt in der Textdarstellung den Header vom Information-Feld.

```text
!
```

ist ein APRS Data Type Identifier.

```text
5012.34N/01956.78E>
```

ist der eigentliche APRS-Inhalt, der Position und Symbol beschreibt.

Damit ist klar erkennbar, dass nicht alle sichtbaren Elemente des Pakets zur gleichen Schicht gehören.

## Destination address

Das Destination-Feld ist Teil des AX.25-Headers.

Bei klassischer AX.25-Nutzung kann die Destination die Zielstation angeben.

Bei APRS ist die Situation komplexer.

Viele APRS-Pakete verwenden die Destination-Adresse zur Identifikation des Geräte- oder Softwaretyps.

Beispiel:

```text
SQ9MDD-7>APRS:...
```

Das bedeutet nicht, dass das Paket an eine Station mit dem Rufzeichen `APRS` gerichtet ist.

Ebenso kann die Destination Werte aus dem TOCALL-System enthalten.

Daher sollte die Destination-Adresse in APRS nicht automatisch als Adresse eines bestimmten Empfängers interpretiert werden.

Die genauen Regeln für Destination Address und TOCALL werden separat beschrieben.

## Der Empfänger einer APRS-Nachricht

Diese Unterscheidung ist bei Nachrichten besonders wichtig.

Zum Beispiel kann ein Paket so aussehen:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

Die AX.25-Destination kann weiterhin sein:

```text
APRS
```

während der tatsächliche Empfänger der APRS-Nachricht:

```text
SP9XYZ
```

innerhalb des APRS-Information-Feldes steht.

Dies sind zwei unterschiedliche Adressierungsebenen.

## Digipeater-Pfad

Elemente wie:

```text
WIDE1-1
WIDE2-1
```

sind nicht Teil des eigentlichen APRS-Payloads.

Sie gehören zur Liste der Digipeater-Adressen im AX.25-Frame.

Diese Schicht ermöglicht Digipeatern festzustellen, ob und wie ein Frame erneut ausgesendet werden kann.

In der Textdarstellung sehen wir das als:

```text
SOURCE>DEST,PATH:DATA
```

logisch gehören jedoch:

```text
SOURCE
DEST
PATH
```

zum AX.25-Header,

während:

```text
DATA
```

das Information-Feld ist, in dem sich APRS befindet.

## Die Textdarstellung ist nicht der rohe Funkframe

APRS-Pakete werden sehr häufig als Text dargestellt:

```text
SOURCE>DEST,PATH:information
```

Das ist eine äußerst praktische Darstellung für Benutzer, Logs und Anwendungen.

Es ist jedoch keine exakte Byte-für-Byte-Darstellung dessen, was sich auf dem Funkkanal befindet.

Ein realer AX.25-Frame enthält Elemente, die in der Textdarstellung nicht direkt sichtbar sind, darunter:

- codierte AX.25-Adressen,
- Steuerbits,
- Control-Feld,
- PID,
- FCS,
- Mechanismen im Zusammenhang mit der Frameübertragung.

Daher sollte:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

als lesbare Darstellung des Pakets verstanden werden und nicht als wörtlicher Inhalt der Funkübertragung.

## APRS-IS

Auf der Internetseite werden APRS-Pakete nicht als rohe AX.25-Funkframes übertragen.

APRS-IS verwendet eine Textdarstellung der Pakete ähnlich:

```text
SOURCE>DEST,PATH:information
```

Ein Paket kann daher von einem IGate über RF empfangen werden:

```text
AX.25
   |
   v
IGate
```

und anschließend als Textdarstellung an APRS-IS weitergeleitet werden:

```text
SOURCE>DEST,PATH:information
```

Dem Paket können Informationen hinzugefügt werden, die für APRS-IS charakteristisch sind.

Ein Beispiel sind **q-constructs**.

## q-constructs sind kein Teil des Funkpfads

In APRS-IS kann ein Paket wie dieses erscheinen:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:...
```

Das Element:

```text
qAR
```

wurde nicht von der Station über Funk als weiteres Element des normalen Digipeater-Pfads ausgesendet.

Es handelt sich um Information, die in der APRS-IS-Umgebung hinzugefügt wurde.

Ebenso kann:

```text
SQ9MDD-4
```

in diesem Teil das IGate kennzeichnen, das für die Weiterleitung des Pakets an APRS-IS verantwortlich ist.

Daher sollte nicht der gesamte in APRS-IS sichtbare Pfad als exakte Kopie des auf RF vorhandenen Pfads interpretiert werden.

## RF und APRS-IS sind unterschiedliche Transportumgebungen

Dieselbe APRS-Information kann über unterschiedliche Wege transportiert werden.

Auf RF:

```text
APRS-Daten
   |
   v
AX.25
   |
   v
Modem
   |
   v
Funkgerät
```

Im Internet:

```text
APRS-Daten
   |
   v
APRS-IS
   |
   v
TCP/IP
```

In beiden Fällen kann die Bedeutung der APRS-Information gleich bleiben.

Es ändert sich lediglich die Transportmethode.

Diese Unterscheidung ist wichtig.

**APRS ist eine Informationsschicht, während RF und APRS-IS unterschiedliche Umgebungen sind, durch die diese Information transportiert werden kann.**

## IGate als Grenze zwischen den Umgebungen

Ein IGate befindet sich zwischen dem Funknetz und APRS-IS.

In Richtung RF -> Internet:

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

Das IGate empfängt den Funkframe, interpretiert die erforderlichen Elemente und leitet die entsprechende Darstellung des Pakets an APRS-IS weiter.

Schematisch:

```text
[RF / AX.25]
      |
      v
    IGate
      |
      v
 [APRS-IS]
```

Dabei handelt es sich jedoch nicht einfach um das Weiterreichen von Bits von einer Schnittstelle zur anderen.

Das IGate arbeitet an der Grenze zwischen zwei unterschiedlichen Transportumgebungen.

## Richtung APRS-IS -> RF

Der Übergang in die Gegenrichtung ist komplexer.

Man kann nicht einfach eine beliebige Textzeile aus APRS-IS nehmen und unverändert über Funk aussenden.

APRS stellt Mechanismen bereit, die bestimmen, wie ausgewählte Informationen von APRS-IS zu RF übertragen werden.

Insbesondere kann das Format:

**third-party traffic**

verwendet werden.

Es ermöglicht, Informationen über die Herkunft des ursprünglichen Pakets zu erhalten.

Die detaillierten Regeln dieses Mechanismus werden in den Abschnitten zu speziellen Formaten und zum IGate-Betrieb beschrieben.

## Third-party traffic

Der Third-party-traffic-Mechanismus erzeugt eine zusätzliche Kapselungsschicht.

Sehr vereinfacht:

```text
AX.25
 |
 v
APRS third-party packet
 |
 v
ursprüngliches Paket
```

Das bedeutet, dass ein Paket aus einer anderen Umgebung nicht einfach als normales RF-Paket kopiert wird.

Es wird in ein spezielles APRS-Format eingebettet.

Dadurch kann der Empfänger erkennen, dass die Information durch ein anderes Element der Infrastruktur weitergeleitet wurde.

## Was gehört zu welcher Schicht?

Die folgende Tabelle zeigt einige typische APRS-Elemente und ihre Position im System.

| Element | Schicht |
|---|---|
| `144.800 MHz` | Funk / RF |
| `1200 baud` | Modem / Übertragung |
| AFSK | Modulation |
| Bell 202 | Modulation |
| AX.25 | Sicherungsschicht |
| Source callsign | AX.25 |
| Destination address | AX.25 |
| `WIDE1-1` | AX.25-Pfad |
| `WIDE2-1` | AX.25-Pfad |
| UI | AX.25-Frame-Typ |
| PID | AX.25 |
| FCS | AX.25 |
| `!` | APRS DTI |
| `=` | APRS DTI |
| `:` | APRS message DTI |
| Position | APRS |
| Objekt | APRS |
| Nachricht | APRS |
| Telemetrie | APRS |
| Wetter | APRS |
| TOCALL | APRS-Nutzung der Destination |
| APRS-IS | APRS-Internettransport |
| `qAR` | APRS-IS |
| TCP/IP | Internettransport |
| APRS.fi | Anwendung / Dienst |

Diese Trennung hilft zu verstehen, warum einige Paketelemente nur auf RF, andere nur in APRS-IS auftreten und wieder andere ihre Bedeutung in beiden Umgebungen behalten.

## Dieselben Daten, unterschiedliche Transporte

Nehmen wir an, die eigentliche APRS-Information beschreibt die Position einer Station.

Auf RF kann sie so transportiert werden:

```text
APRS-Position
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

Nach dem Durchlaufen eines IGate:

```text
APRS-Position
     |
     v
  APRS-IS
     |
     v
   TCP/IP
```

Die Bedeutung der Position muss sich nicht ändern.

Es ändert sich lediglich der Mechanismus, mit dem die Information transportiert wird.

## Die Anwendung liegt noch darüber

Am Ende befindet sich die Benutzeranwendung.

Das kann sein:

- ein Funkgerät mit APRS-Decoder,
- ein APRS-Terminal,
- eine Computeranwendung,
- eine Karte,
- ein Internetdienst,
- ein Monitoring-System,
- eine Datenbank.

Die Anwendung interpretiert APRS-Informationen und stellt sie dem Benutzer dar.

Zum Beispiel:

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
Anwendung
 |
 v
Karte
```

oder:

```text
APRS-IS
   |
   v
Anwendung
   |
   v
Karte
```

Die Karte ist daher die letzte Präsentationsschicht der Information und nicht das APRS-Protokoll selbst.

## Häufige Fehler

### APRS und AX.25 sind dasselbe

Nein.

AX.25 stellt unter anderem die Frame-Struktur und die Adressierung der Sicherungsschicht bereit.

APRS verwendet AX.25 zum Transport seiner eigenen Daten.

### 1200-Baud-AFSK ist APRS

Nein.

1200-Baud-AFSK ist eine Methode zur Datenübertragung.

APRS liegt darüber.

### WIDE1-1 ist Teil der APRS-Daten

Nein.

`WIDE1-1` befindet sich im AX.25-Adresspfad.

### Destination address bezeichnet immer den APRS-Empfänger

Nein.

Bei APRS kann die Destination auch zur Identifikation eines Geräts oder einer Software dienen.

### qAR ist Teil des Funkpfads

Nein.

`qAR` ist ein Element von APRS-IS.

### Eine Textzeile eines Pakets ist der genaue Funkframe

Nein.

```text
SOURCE>DEST,PATH:DATA
```

ist eine lesbare Darstellung der im Frame enthaltenen Informationen, enthält aber nicht alle Bestandteile der tatsächlichen AX.25-Übertragung.

### APRS-IS ist eine Internetversion von AX.25

Nicht direkt.

APRS-IS transportiert APRS-Informationen in einer Internetumgebung und verwendet dafür eine eigene Methode zur Darstellung und Verteilung von Paketen.

## Das Gesamtbild

Die Schichten der Funkübertragung können wie folgt zusammengefasst werden:

```text
+-----------------------------+
| Anwendung                   |
+-----------------------------+
| APRS                        |
| Positionen, Nachrichten,    |
| Objekte, Wetter, Telemetrie |
+-----------------------------+
| AX.25 UI                    |
| Adressen, path, control,    |
| PID                         |
+-----------------------------+
| Modem / Modulation          |
| z. B. AFSK 1200             |
+-----------------------------+
| Funkgerät                   |
+-----------------------------+
| RF                          |
+-----------------------------+
```

Auf der APRS-IS-Seite:

```text
+-----------------------------+
| Anwendung                   |
+-----------------------------+
| APRS                        |
+-----------------------------+
| APRS-IS                     |
+-----------------------------+
| TCP/IP                      |
+-----------------------------+
| Internetnetz                |
+-----------------------------+
```

Zwischen diesen Umgebungen kann ein IGate arbeiten:

```text
             RF                         Internet

+--------------------------+       +----------------------+
| APRS                     |       | APRS                 |
+--------------------------+       +----------------------+
| AX.25                    |       | APRS-IS              |
+--------------------------+       +----------------------+
| Modem                    |       | TCP/IP               |
+--------------------------+       +----------------------+
| Funkgerät                |       | Internet             |
+--------------------------+       +----------------------+
             \                         /
              \                       /
               +-------- IGate ------+
```

## Die wichtigsten Punkte

**APRS definiert nicht die gesamte Funkübertragung.**

Es verwendet andere Schichten, insbesondere AX.25.

**AX.25 und APRS sind nicht dasselbe.**

AX.25 transportiert Daten, während APRS die Bedeutung der Information im Datenfeld definiert.

**1200-Baud-AFSK ist nicht APRS.**

Es ist eine Möglichkeit, AX.25-Frames über Funk zu übertragen.

**WIDE1-1 und ähnliche Elemente gehören zum AX.25-Pfad.**

Sie sind nicht Teil des eigentlichen APRS-Payloads.

**Die Destination-Adresse ist ein AX.25-Element.**

APRS kann sie zusätzlich zur Identifikation eines Geräts oder einer Softwareimplementierung verwenden.

**APRS-IS ist eine andere Transportumgebung für APRS-Informationen.**

Es überträgt rohe Funkframes nicht 1:1.

**q-constructs gehören zu APRS-IS.**

Sie sollten nicht als normale Elemente des Funkpfads interpretiert werden.

**Die Textdarstellung eines Pakets kombiniert Informationen aus mehreren Schichten in einer einzigen Zeile.**

Daher ist:

```text
SOURCE>DEST,PATH:DATA
```

eine sehr praktische Darstellung für Menschen und Anwendungen, aber kein wörtliches Abbild der gesamten Funkübertragung.

## Weiter

Nach dem Verständnis der Systemschichten kann mit einer detaillierteren Analyse eines einzelnen Pakets fortgefahren werden.

Die nächsten Themen sollten umfassen:

- Aufbau eines AX.25-Frames,
- Anatomie der Textdarstellung eines APRS-Pakets,
- Source Callsign und SSID,
- Destination Address,
- TOCALL,
- Digipeater-Pfade,
- APRS-Information-Feld,
- Data Type Identifier,
- Unterschiede zwischen einem RF-Paket und seiner Darstellung in APRS-IS,
- q-constructs,
- third-party traffic.

Mit dieser Grundlage können die weiteren Protokollelemente mit dem klaren Verständnis analysiert werden, **zu welcher Schicht des Systems sie tatsächlich gehören**.
