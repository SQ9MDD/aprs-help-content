---
title: VP-Digi
description: Moderner, stromsparender APRS-Digipeater-Controller und KISS-Modem mit AX.25, FX.25 und mehreren Modemarten.
template: doc
tableOfContents: true
---

VP-Digi ist ein eigenständiger AX.25-Digipeater-Controller und KISS-TNC-Modem, unter anderem für APRS.

Das Projekt wurde als kleines, preiswertes und stromsparendes STM32-basiertes Gerät entwickelt. Es kombiniert die Funktionen eines Hardware-TNC, Packet-Radio-Modems und eines leistungsfähigen APRS-Digipeaters.

Offizielles Repository:

https://github.com/sq8vps/vp-digi

Dokumentation:

https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md

VP-Digi ist ein Open-Source-Projekt und wird weiterhin entwickelt. Neue Versionen bringen Fehlerkorrekturen, Verbesserungen der Modems, Weiterentwicklung von FX.25 sowie Optimierungen bei AX.25 und KISS.

## Wozu VP-Digi?

VP-Digi ist eine interessante Alternative zu klassischen TNCs und größeren Computern in APRS-Stationen.

Ein typischer Aufbau kann so aussehen:

```text
Funkgerät
  |
Audio + PTT
  |
VP-Digi
  |
KISS / USB / UART
  |
APRSBox / Computer / andere Anwendung
```

Das Gerät kann auch vollständig autonom als Digipeater arbeiten:

```text
Funkgerät
  |
Audio + PTT
  |
VP-Digi
```

Dafür sind weder Raspberry Pi noch PC oder Betriebssystem erforderlich.

## Moderne Architektur

VP-Digi basiert auf einem **STM32F103**-Mikrocontroller.

Diese Architektur ermöglicht die digitale Verarbeitung von:

- Modulation und Demodulation,
- Kanalerkennung,
- AX.25-Codierung und -Decodierung,
- FX.25,
- Duplikatfilterung,
- Digipeater-Funktionen,
- KISS TNC,
- Beacon-Erzeugung.

Damit ist VP-Digi nicht nur ein Tracker oder Frame-Generator, sondern ein vollständiger Controller der Funkschicht für Packet Radio und APRS.

## AX.25- und APRS-Kompatibilität

Eine der wichtigsten Eigenschaften ist die korrekte Unterstützung von **AX.25**, dem Protokoll hinter klassischem Packet Radio und APRS.

Das Gerät kann arbeiten als:

- AX.25-Modem,
- KISS-TNC,
- APRS-Digipeater,
- Beacon-Generator,
- Packet-Radio-Modem,
- Funkschnittstelle für externe Software.

Durch KISS kann es mit vielen Anwendungen arbeiten, ohne an ein proprietäres Herstellerprotokoll gebunden zu sein.

Beispiel:

```text
VP-Digi
   |
KISS
   |
APRSBox
```

oder:

```text
VP-Digi
   |
KISS
   |
Packet-Radio-Software
```

## Unterstützte Modems

VP-Digi unterstützt mehrere Modemarten.

### 1200 Bd Bell 202

Das klassische APRS-Modem für VHF:

```text
AFSK 1200 Bd
1200 / 2200 Hz
```

Dies ist der grundlegende Modus für klassisches APRS im 2-m-Band.

### 300 Bd Bell 103

Ein Modus hauptsächlich für HF:

```text
AFSK 300 Bd
1600 / 1800 Hz
```

### 9600 Bd G3RUH

VP-Digi unterstützt außerdem:

```text
GFSK 9600 Bd
G3RUH
```

Dieser Modus benötigt einen geeigneten Signalweg und normalerweise Flat-Audio.

Nicht jedes Funkgerät eignet sich für 9600 Bd über Mikrofoneingang und Lautsprecherausgang.

### 1200 Bd V.23

Zusätzlich steht zur Verfügung:

```text
AFSK 1200 Bd
1300 / 2100 Hz
```

kompatibel mit V.23.

## FX.25

VP-Digi unterstützt auch **FX.25**.

FX.25 erweitert AX.25 um eine Reed-Solomon-basierte Vorwärtsfehlerkorrektur. Dadurch kann die Wahrscheinlichkeit eines korrekten Empfangs bei schwierigen Funkbedingungen steigen, während die Kompatibilität mit klassischem AX.25 erhalten bleibt.

VP-Digi kann:

- AX.25 empfangen,
- FX.25 empfangen,
- AX.25 senden,
- FX.25 senden.

FX.25 kann für Empfang und Aussendung getrennt konfiguriert werden.

## Erkennung eines belegten Kanals

VP-Digi basiert bei DCD nicht nur auf erfolgreich decodierten Daten.

Die Kanalbelegung wird anhand eines gültigen Modulationssignals erkannt.

Dadurch kann ein belegter Kanal früher erkannt und die Zahl der Kollisionen reduziert werden.

In der Praxis sollte das angeschlossene Funkgerät normalerweise mit offener Rauschsperre betrieben werden.

## KISS TNC

Eine der wichtigsten Funktionen ist der **KISS**-Modus.

Dabei übernimmt VP-Digi die Funkschicht:

```text
AX.25
Modulation
Demodulation
PTT
DCD
```

während eine externe Anwendung die höhere Logik übernimmt.

Das kann beispielsweise sein:

- APRSBox,
- ein Packet-Radio-Client,
- Winlink-Software,
- eigene Software,
- Diagnosewerkzeuge.

VP-Digi ist damit nicht auf den Einsatz als Digipeater beschränkt.

## USB und zwei UART-Ports

Das Gerät bietet:

- USB,
- UART1,
- UART2.

Jeder Port kann unabhängig arbeiten als:

- KISS,
- Frame-Monitor,
- Konfigurationsterminal.

So lassen sich flexible Installationen aufbauen, zum Beispiel ein Port für KISS und ein weiterer für Monitoring und Diagnose.

## APRS-Digipeater

VP-Digi enthält eine umfangreiche Digipeater-Logik.

Konfigurierbar sind unter anderem:

- 4 New-N-Aliase wie `WIDEn-N`,
- 4 einfache Aliase,
- maximale Hop-Anzahl,
- Trace-Modus,
- Direct-only-Modus,
- Viscous Delay,
- Paketfilter,
- Duplikatpuffer.

Das bietet deutlich mehr Kontrolle als ein einfacher Paket-Repeater.

## Viscous Delay

VP-Digi unterstützt **Viscous Delay**.

Anstatt ein Paket sofort zu wiederholen, kann der Digipeater kurz warten und prüfen, ob eine andere Station das Paket bereits wiederholt hat.

Ist das der Fall, kann die eigene Aussendung unterdrückt werden.

Das reduziert:

- unnötige Duplikate,
- Kanalbelegung,
- Wiederholungen.

Besonders nützlich ist dies in Gebieten mit mehreren überlappenden Digipeatern.

## Direct-only

Für einzelne Aliase kann zusätzlich **Direct-only** aktiviert werden.

Dann werden nur Pakete wiederholt, die direkt von der Ursprungsstation empfangen wurden.

Dadurch werden unnötige Wiederholungen bereits weitergeleiteter Pakete reduziert.

## Stationsfilterung

VP-Digi kann Pakete anhand des Rufzeichens filtern.

Verfügbar sind:

- Blacklist,
- Whitelist.

Damit können bestimmte Stationen blockiert oder nur ausgewählte Rufzeichen zugelassen werden.

## Duplikatschutz

Der Digipeater besitzt einen Puffer zur Erkennung wiederholter Pakete.

Dadurch wird verhindert, dass derselbe Frame mehrfach über unterschiedliche Wege retransmittiert wird.

Die Pufferzeit ist konfigurierbar.

## Beacons

VP-Digi unterstützt bis zu **8 unabhängige Beacons**.

Für jeden Beacon können eingestellt werden:

- Inhalt,
- Intervall,
- Verzögerung,
- APRS-Pfad,
- Aktivierung oder Deaktivierung.

Beispiel:

```text
!5002.63N/02157.91E#VP-Digi
```

## Frame-Monitor

VP-Digi kann auch als AX.25-Verkehrsmonitor arbeiten.

Empfangene Pakete können über einen seriellen Port angezeigt werden. Das ist nützlich für:

- Inbetriebnahme,
- Konfigurationsprüfung,
- Diagnose des Audiowegs,
- Analyse von Packet-Radio- und APRS-Verkehr.

## Niedriger Energieverbrauch

Eine der größten Stärken von VP-Digi ist der sehr geringe Stromverbrauch.

Laut Projektdokumentation benötigt das Gerät ungefähr:

```text
40-50 mA
```

Das ist besonders wichtig bei Stationen:

- mit Akkubetrieb,
- mit Solarversorgung,
- an Standorten ohne Netzstrom,
- als autonome Digipeater,
- im 24/7-Betrieb.

In solchen Installationen verbraucht das Funkgerät normalerweise wesentlich mehr Energie als der Controller selbst.

## Offenes und aktives Projekt

VP-Digi ist ein Open-Source-Projekt.

Das Repository stellt bereit:

- Quellcode,
- Schaltplan,
- Dokumentation,
- fertige Firmware,
- Änderungsverlauf.

Das Projekt wird weiterentwickelt und neue Versionen bringen Fehlerbehebungen und neue Funktionen.

Für Infrastruktur, die lange zuverlässig arbeiten soll, ist das ein wichtiger Vorteil.

## Für wen eignet sich VP-Digi?

VP-Digi eignet sich gut, wenn benötigt wird:

- ein autonomer APRS-Digipeater,
- ein stromsparender 24/7-Knoten,
- ein Hardware-KISS-Modem,
- ein Packet-Radio-Modem,
- eine Funkschnittstelle für APRSBox oder andere Software,
- eine Plattform für AX.25- und FX.25-Experimente,
- eine kleine Feld- oder Solarstation.

## VP-Digi gegenüber einem Softwaremodem

VP-Digi und Softwarelösungen wie Dire Wolf stehen für zwei unterschiedliche Ansätze.

### VP-Digi

```text
Mikrocontroller
geringer Energieverbrauch
kein Betriebssystem
autonomer Digipeater
KISS
```

### Softwaremodem

```text
Computer
Soundkarte
mehr Rechenleistung
größere DSP-Flexibilität
einfachere Integration mit Netzwerkdiensten
```

Bei einer autonomen Installation kann VP-Digi einfacher und energiesparender sein.

In einer größeren Station kann es als KISS-Modem für externe Software dienen.

## Zusammenfassung

VP-Digi ist ein gutes Beispiel für moderne APRS-Hardware.

Es kombiniert:

- AX.25-Kompatibilität,
- FX.25-Unterstützung,
- mehrere Modemarten,
- einen leistungsfähigen Digipeater,
- KISS,
- USB und UART,
- sehr geringen Energieverbrauch,
- Open-Source-Firmware,
- aktive Weiterentwicklung.

Damit kann es sowohl als eigenständiger Digipeater als auch als Modem innerhalb eines größeren APRS-Systems arbeiten.

Besonders interessant ist es dort, wo **Protokollkonformität, Zuverlässigkeit, niedriger Energieverbrauch und Betrieb ohne vollständigen Computer** wichtig sind.

## Dokumentation

Wichtige Quellen:

- Projekt-Repository: https://github.com/sq8vps/vp-digi
- polnische Dokumentation: https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md
- Projektbeschreibung bei SQ8L: https://sq8l.pzk.pl/index.php/vp-digi-tani-i-funkcjonalny-sterownik-digipeatera-aprs-wraz-z-modemem-kiss/
- Changelog: https://github.com/sq8vps/vp-digi/blob/main/CHANGELOG.md
