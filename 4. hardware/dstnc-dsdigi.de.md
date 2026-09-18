---
title: dsTNC und dsDIGI
description: Hardware-KISS-TNC-Modem und APRS-Digipeater auf Basis eines dsPIC-DSP-Prozessors mit sehr effektivem AFSK-1200-Demodulator.
template: doc
tableOfContents: true
---

dsTNC ist ein **AFSK-1200-Hardwaremodem und KISS-TNC-Controller**, entwickelt von Tomek SP9UOB.

Auf derselben Hardware kann auch die Firmware **dsDIGI** betrieben werden, wodurch das Gerät zu einem eigenständigen APRS-Digipeater wird.

Projektseite dsTNC:

https://sp9uob.verox.pl/dstnc.html

Beschreibung von dsDIGI:

https://sp9uob.verox.pl/dsdigi.html

Das Projekt ist ein interessantes Beispiel für digitale Signalverarbeitung in einem APRS-Hardwaremodem.

Anstelle eines klassischen analogen Modem-ICs übernimmt ein **dsPIC30F4013** die AFSK-Decodierung.

## Zwei Anwendungen derselben Hardware

Die dsTNC-Hardware kann zwei grundlegende Rollen übernehmen.

### KISS TNC

```text
Funkgerät
  |
Audio + PTT
  |
dsTNC
  |
KISS
  |
Computer / APRS-Anwendung
```

In diesem Modus übernimmt das Gerät das AFSK-Modem und die AX.25-Schicht, während externe Software die weitere Paketverarbeitung übernimmt.

### Eigenständiger Digipeater

Mit installierter dsDIGI-Firmware kann das Gerät ohne Computer arbeiten:

```text
Funkgerät
  |
Audio + PTT
  |
dsDIGI
  |
APRS-Digipeater
```

Dieselbe Platine kann somit sowohl als Hardwaremodem als auch als autonomer APRS-Knoten eingesetzt werden.

## DSP statt klassischem Analogmodem

Eine der interessantesten Eigenschaften von dsTNC ist die Art der Demodulation.

Das Design verwendet **digitale Signalverarbeitung, DSP**.

Das Audiosignal des Empfängers gelangt über einen Tiefpassfilter zum ADC des Prozessors.

Die weitere Verarbeitung erfolgt in Software.

Vereinfachter Empfangspfad:

```text
Funkgerät
  |
Audio
  |
Filter
  |
ADC
  |
DSP
  |
AX.25
```

Dadurch können fortgeschrittenere Demodulationsalgorithmen verwendet werden, ohne einen klassischen Modem-IC einzusetzen.

## Sehr effektiver AFSK-1200-Demodulator

Dies ist eine der stärksten Seiten der Konstruktion.

Der Autor verwendete einen Algorithmus, der von einer Lösung von Thomas Sailer HB9JNX inspiriert wurde.

Bei Tests mit der bekannten **WA8LMF APRS Test CD** decodierte dsTNC auf der zweiten Spur korrekt:

```text
900 Frames
```

Im selben Test decodierte ein klassisches Modem auf Basis des **TCM3105**:

```text
747 Frames
```

Das Ergebnis zeigt die Effektivität des DSP-Demodulators, insbesondere bei verrauschten und verzerrten Signalen.

## AFSK 1200

Der grundlegende Betriebsmodus von dsTNC ist klassisches APRS:

```text
AFSK 1200 Bd
1200 Hz / 2200 Hz
Bell 202
```

Dies ist die übliche Modulation für klassisches APRS im 2-m-Band.

Das Empfangsaudio wird vom ADC des Prozessors abgetastet. Das Sendesignal wird digital per PWM erzeugt und nach Filterung dem Sender zugeführt.

## KISS TNC

Die dsTNC-Firmware implementiert **KISS TNC**.

Der serielle Port kann mit:

```text
57600 bit/s
```

betrieben werden.

Dadurch kann das Gerät unter anderem mit folgenden Anwendungen arbeiten:

- APRS-Anwendungen,
- Xastir,
- UI-View,
- Linux-AX.25-Stack,
- anderer KISS-kompatibler Software.

KISS stellt eine einfache und weit verbreitete Schnittstelle zwischen Modem/TNC und Anwendung bereit.

Damit ist dsTNC nicht an ein bestimmtes Programm gebunden.

## dsDIGI

Eine alternative Firmware für dieselbe Hardware ist **dsDIGI**.

Damit kann das Gerät als eigenständiger APRS-Digipeater arbeiten.

Die grundlegende Konfiguration umfasst:

- Rufzeichen,
- Digipeater-Alias,
- Position,
- Höhe,
- APRS-Symbol,
- Beacon,
- Beacon-Intervall,
- Beacon-Pfad,
- TXDELAY,
- TXTAIL,
- Geschwindigkeit der seriellen Schnittstelle.

Die Konfiguration erfolgt über ein einfaches Textterminal.

## WIDE1-1

dsDIGI wurde hauptsächlich für den Betrieb als lokaler Digipeater mit:

```text
WIDE1-1
```

entwickelt.

Der Alias kann geändert werden, und die Implementierung unterstützt einen Alias vom Typ `WIDEn-N`.

Dies ergibt eine einfache und übersichtliche Konfiguration für einen lokalen Digipeater.

## Eigener Beacon

dsDIGI kann automatisch einen eigenen Positionsbeacon erzeugen.

Konfigurierbar sind unter anderem:

- Rufzeichen,
- Breitengrad,
- Längengrad,
- Höhe,
- Symbol,
- Kommentar,
- Pfad,
- Intervall.

Die Position wird automatisch dem Beacon-Inhalt hinzugefügt.

So kann sich der Digipeater im APRS-Netz korrekt identifizieren.

## Telemetrie

Eine interessante dsDIGI-Funktion ist die Erzeugung von APRS-Telemetrie.

Das Gerät kann melden:

- Versorgungsspannung,
- Temperatur bei angeschlossenem **DS18B20**,
- Anzahl empfangener Pakete,
- Anzahl gesendeter Pakete.

Damit lässt sich der grundlegende Zustand der Installation aus der Ferne beobachten.

Bei einem Digipeater an einem schwer zugänglichen Standort ist das sehr nützlich.

## Diagnoseinformationen

dsDIGI beschränkt sich nicht auf die Wiederholung von Paketen.

Das Terminal kann Informationen über empfangene Frames anzeigen, darunter:

- Quelle,
- Ziel,
- Pfad,
- Inhalt,
- decodierte Position,
- Entfernung vom Digipeater,
- Richtung zur empfangenen Station.

Zum Beispiel:

```text
Dist = 56 km
bearing = 78 degrees
```

So lässt sich der lokale Verkehr bequem ohne zusätzliche APRS-Software beobachten.

## Digipeater-Status

dsDIGI erzeugt auch eigene Statusinformationen.

Darin können enthalten sein:

- Betriebszeit,
- Anzahl empfangener Frames,
- Anzahl wiederholter Pakete.

Damit lässt sich die Aktivität des Geräts einfach beurteilen.

## Einstellung des Sendepfads

Die Firmware kann außerdem Testtöne erzeugen:

```text
1200 Hz
2200 Hz
```

Sie können zum Einstellen des Audiopegels und der Sendedeviation verwendet werden.

Das ist bei der Inbetriebnahme eines Hardwaremodems sehr praktisch.

## Einfache Konstruktion

Ein Vorteil von dsTNC ist die relativ geringe Zahl an Bauteilen.

Die meisten Modemfunktionen werden softwareseitig durch den dsPIC-Prozessor realisiert.

Laut Autor benötigt die Schaltung keine spezielle Abstimmung.

Nach dem Programmieren des Prozessors besteht der wichtigste Inbetriebnahmeschritt in der korrekten Einstellung des Audiopegels.

## DSP-basiertes Hardwaredesign

dsTNC und dsDIGI sind ein interessantes Beispiel für APRS-Hardware auf DSP-Basis.

Das Projekt kombiniert:

- digitale AFSK-Demodulation,
- KISS,
- autonomen Digipeater,
- APRS-Telemetrie,
- Positionsanalyse empfangener Stationen,
- einfache Diagnose über Terminal.

Die auf der Projektseite verfügbare dsDIGI-Firmware v1.25 trägt das Datum:

```text
24. November 2013
```

Das Projekt bleibt technisch interessant und zeigt den praktischen Einsatz eines dsPIC als APRS-Modem und Controller.

## Rollen von dsTNC und dsDIGI

Der Unterschied lässt sich einfach zusammenfassen.

### dsTNC

```text
Funkgerät
  |
AFSK-Modem
  |
KISS
  |
externe Anwendung
```

Vor allem ein Hardwaremodem und TNC.

### dsDIGI

```text
Funkgerät
  |
AFSK-Modem
  |
Digipeater-Logik
```

Ein eigenständiger Digipeater auf derselben Hardware.

Die installierte Firmware bestimmt die Funktion des Geräts.

## Für wen ist das Projekt interessant?

dsTNC und dsDIGI können interessant sein für Personen, die:

- einen Hardware-KISS-TNC nutzen möchten,
- einen einfachen autonomen Digipeater benötigen,
- mit AX.25 und AFSK 1200 experimentieren,
- sich für DSP-Demodulation interessieren,
- eine Hardwareimplementierung eines APRS-Modems untersuchen möchten,
- bereits ein dsTNC- oder dsDIGI-Gerät besitzen.

Ein besonders interessanter Teil des Projekts ist der **effektive DSP-Demodulator von dsTNC**.

## Dokumentation

Wichtige Quellen:

- dsTNC: https://sp9uob.verox.pl/dstnc.html
- dsDIGI: https://sp9uob.verox.pl/dsdigi.html

Auf der Projektseite sind außerdem verfügbar:

- Schaltplan,
- Stückliste,
- Bestückungszeichnung,
- PCB-Layout,
- Firmware,
- Änderungsverlauf.
