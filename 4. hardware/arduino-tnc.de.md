---
title: Arduino TNC und Arduino TNC Plus
description: Offenes KISS-TNC-Modem für APRS, entwickelt vom ursprünglichen KI4MCW-Projekt über Experimente von M1GEO bis zu Arduino TNC Plus von SQ9MDD und SQ5RWU.
template: doc
tableOfContents: true
---

Arduino TNC ist ein offenes Projekt für einen **KISS TNC mit AFSK 1200**, dessen Geschichte mehrere Entwicklungsstufen und Beiträge verschiedener Funkamateure umfasst.

Das Projekt begann mit Robert Marshall **KI4MCW**, der ein softwarebasiertes AFSK-Modem ohne speziellen Modem-IC entwickelte.

Das Audiosignal wurde vom ADC eines AVR-Mikrocontrollers abgetastet, während AFSK-Demodulation und AX.25-Decodierung in Software erfolgten.

Ursprüngliches KI4MCW-Projekt:

https://sites.google.com/site/ki4mcw/Home/arduino-tnc

Experimente und Weiterentwicklung durch George Smart M1GEO:

https://www.george-smart.co.uk/arduino/arduino_tnc/

Polnische Hardwareversion:

https://hamspirit.pl/SQ9MDD/?p=337

Arduino TNC Plus:

https://hamspirit.pl/SQ9MDD/?p=541

Von SQ9MDD und SQ5RWU weiterentwickelter Code:

https://github.com/SQ9MDD/arduino_tnc

## Projektidee

Die Grundidee war einfach: Ein verbreiteter AVR-Mikrocontroller sollte als AFSK-Modem arbeiten, ohne einen spezialisierten Modem-Baustein zu benötigen.

Funktionsschema:

```text
Funkgerät
  |
Audio + PTT
  |
Arduino
  |
AFSK / AX.25
  |
KISS
  |
Computer / APRS-Anwendung
```

Arduino übernimmt:

- Audioabtastung,
- AFSK-Demodulation,
- AX.25-Decodierung,
- Erzeugung des Sendesignals,
- PTT-Steuerung,
- KISS-Kommunikation.

Die externe Anwendung übernimmt die höheren APRS- oder Packet-Radio-Funktionen.

## Ursprung: KI4MCW

Robert Marshall KI4MCW begann um 2010 mit der Arbeit an Arduino TNC.

Das Projekt war ein experimentelles softwarebasiertes APRS-Modem, hauptsächlich für AVR-basierte Arduino-Plattformen.

Ein wichtiges Ziel war, möglichst viele Funktionen direkt im Mikrocontroller-Code auszuführen.

Anstelle eines Bausteins wie TCM3105 oder MX614 wurde das Empfangsaudio direkt dem ADC des Prozessors zugeführt.

```text
Funkgerät
  |
Audio
  |
ADC
  |
Demodulationsalgorithmus
  |
AX.25
```

Spätere Versionen brachten unter anderem:

- AFSK-1200-Empfang,
- KISS,
- DCD,
- automatische ADC-Bias-Korrektur,
- Paketaussendung,
- UART-Unterstützung.

Der Code wurde stark für die begrenzten Ressourcen der AVR-Mikrocontroller optimiert.

## Software-Demodulation

Der interessanteste Aspekt des ursprünglichen Projekts war der Verzicht auf ein dediziertes Analogmodem.

Arduino tastete das Audiosignal ab und erkannte softwareseitig die Töne:

```text
1200 Hz
2200 Hz
```

des klassischen Bell-202-AFSK von APRS.

Dadurch konnte mit sehr wenigen Bauteilen ein vollständiges Modem aufgebaut werden.

Gleichzeitig war sorgfältig optimierter Code erforderlich, da der ATmega328P nur begrenzte Rechenleistung bietet.

## Experimente von M1GEO

George Smart M1GEO testete das KI4MCW-Projekt ausführlich und experimentierte mit einer eigenen Eingangsschaltung.

Zu seinen Arbeiten gehörten:

- Filterung des Audioeingangs,
- Stabilisierung des ADC-Bias,
- Verringerung von USB-Störungen,
- Analyse der Abtastfrequenz,
- Tests verschiedener KI4MCW-Codeversionen.

M1GEO verwendete Version 0.14 und erreichte einen guten Betrieb als KISS TNC.

Er experimentierte außerdem mit der Übertragung von APRS-Daten per Bluetooth zu einem Smartphone und entwarf eine eigene Platine.

Das zeigt eine wichtige Eigenschaft von Arduino TNC: Das Projekt war offen und einfach genug, um es an unterschiedliche Anwendungen anzupassen.

## Polnische Arduino-TNC-Version

Auf Basis der früheren Arbeiten dokumentierte SQ9MDD eine Hardwareversion.

Ziel war eine einfache und preiswerte TNC-Schnittstelle, die sich aus leicht verfügbaren Bauteilen aufbauen lässt.

Typische Konfiguration:

```text
Funkgerät
  |
Arduino TNC
  |
USB / KISS
  |
Raspberry Pi / PC
  |
APRS-Software
```

Die Konstruktion wurde sowohl mit WA8LMF-Testmaterial als auch auf dem realen APRS-Kanal getestet.

Sie eignete sich gut als kleines Modem für Raspberry Pi, iGate oder Heimstation.

## Softwareentwicklung durch SQ5RWU

Łukasz **SQ5RWU** leistete einen wichtigen Beitrag zu späteren Codeversionen.

Die Entwicklungsgeschichte umfasst unter anderem:

- CRC-Prüfung empfangener Frames,
- Stabilitätskorrekturen,
- Verbesserungen im Empfang,
- Verbesserungen beim Senden,
- Bereinigung des Codes,
- Korrekturen für stabilen Betrieb.

Version:

```text
0.15.3
```

enthält eine umfangreiche Reihe von Korrekturen und Verbesserungen durch SQ5RWU.

Danach folgte:

```text
0.15.4
```

als stabile Ausgabe.

Beim Start meldet sich das Gerät mit:

```text
Arduino TNC v.0.15.4
```

## KISS

Arduino TNC arbeitet als klassischer **KISS TNC**.

Typischer Aufbau:

```text
Funkgerät
  |
Arduino TNC
  |
KISS
  |
APRSBox / Xastir / Linux AX.25 / andere Software
```

KISS ist eine einfache Standardschnittstelle zwischen TNC und Anwendung.

Dadurch ist das Gerät nicht an ein bestimmtes Programm gebunden.

## AFSK 1200

Der grundlegende Betriebsmodus ist:

```text
AFSK 1200 Bd
1200 / 2200 Hz
Bell 202
```

die klassische APRS-Modulation auf VHF.

Arduino erzeugt das Sendesignal softwareseitig und steuert PTT.

## Arduino TNC Plus

Die nächste Entwicklungsstufe war **Arduino TNC Plus**, kurz ATNC+.

Es entstand als Erweiterung der früheren Konstruktion.

Die Grundfunktion blieb erhalten, der Funkweg wurde jedoch erweitert.

Die wichtigste Änderung war die Ergänzung eines:

```text
TCM3105
```

als Signalregenerationsstufe nach einer Lösung von Adam SP5RZP.

Funktionsschema:

```text
Funkgerät
  |
Audioweg
  |
TCM3105
  |
Arduino
  |
KISS
  |
Computer
```

Ziel war eine bessere Signalqualität für den digitalen Teil und eine höhere Decodiersicherheit unter realen Funkbedingungen.

## Hardwareänderungen bei ATNC+

Neben dem TCM3105 wurden weitere Verbesserungen eingeführt.

Dazu gehören:

- Gleichspannungs-Sperrkondensatoren an den Transformator-Eingängen,
- verbesserter Audioweg,
- Möglichkeit zur Steuerung von Handfunkgeräten,
- zusätzlicher Konfigurationsjumper,
- 2,2-kΩ-Widerstand für bestimmte PTT-Steuerungen.

Die ATNC+-Platine ist wegen der zusätzlichen Modemschaltung größer als die ursprüngliche Version.

## Stabile Software für ATNC+

Bei Tests von Arduino TNC Plus zeigte sich ebenfalls Bedarf an weiteren Softwarekorrekturen.

Einen großen Teil dieser Arbeiten übernahm SQ5RWU.

Das Ergebnis war eine stabile Softwareversion für ATNC+.

Der Code ist hier verfügbar:

https://github.com/SQ9MDD/arduino_tnc

Dieses Repository sollte als spätere, korrigierte Entwicklungslinie betrachtet werden, nicht als ursprüngliche KI4MCW-Version.

## Kommunikationsparameter

In der stabilen Arduino-TNC-Version arbeitet die serielle Schnittstelle mit:

```text
19200 bit/s
```

für KISS.

## Audioisolation

Die Konstruktion verwendet Audio-Übertrager zur galvanischen Trennung.

Sie trennen Funkgerät und digitalen Teil elektrisch.

Das hilft gegen:

- Masseschleifen,
- Computerstörungen,
- Probleme mit Audiopegeln,
- USB-bedingte Störungen.

## PTT-Steuerung

Arduino steuert den Sender automatisch.

Der PTT-Pfad verwendet eine Trennung und einen Transistor.

```text
Arduino
  |
PTT
  |
Trennung
  |
Funkgerät
```

Dadurch kann sicher mit unterschiedlichen Funkgeräten gearbeitet werden.

## Anwendungen

Arduino TNC und ATNC+ können als Modem eingesetzt werden für:

- APRS-iGate,
- APRS-Heimstation,
- Packet Radio,
- Raspberry Pi,
- Linux-Rechner,
- KISS-kompatible Anwendungen,
- eigene experimentelle Projekte.

Ein typischer Aufbau kann so aussehen:

```text
Funkgerät
  |
Arduino TNC Plus
  |
USB
  |
Raspberry Pi
  |
APRSBox
```

## Lern- und Ausbildungswert

Ein großer Vorteil von Arduino TNC ist, dass sich der gesamte Weg eines Pakets nachvollziehen lässt.

Das Projekt zeigt praktisch:

- wie Audio zum ADC gelangt,
- wie AFSK-Töne erkannt werden,
- wie AX.25-Frames decodiert werden,
- wie CRC funktioniert,
- wie KISS arbeitet,
- wie das TX-Signal erzeugt wird,
- wie PTT gesteuert wird.

Es ist daher ein sehr gutes Projekt für Interessierte an APRS und AVR-Mikrocontrollern.

## Projektgeschichte

Die Entwicklung lässt sich vereinfacht so darstellen:

```text
KI4MCW
  |
ursprüngliches softwarebasiertes Arduino TNC
  |
M1GEO
  |
Tests und Entwicklung des Funkwegs
  |
SQ9MDD
  |
polnische Hardwareversion
  |
SQ5RWU
  |
Codekorrekturen und Stabilisierung
  |
Arduino TNC Plus
```

Es handelt sich also nicht um eine Einzelkonstruktion eines Autors, sondern um das Ergebnis aufeinanderfolgender Experimente und Verbesserungen mehrerer Funkamateure.

## Für wen ist das Projekt interessant?

Arduino TNC kann interessant sein für Personen, die:

- einen eigenen KISS TNC bauen möchten,
- mit Arduino und AVR experimentieren,
- AFSK 1200 praktisch kennenlernen möchten,
- ein einfaches Modem für Raspberry Pi benötigen,
- sich für AX.25 interessieren,
- verstehen möchten, wie ein Softwaremodem funktioniert.

Arduino TNC Plus ist zusätzlich interessant für Personen, die den erweiterten Funkweg mit Signalregeneration nutzen möchten.

## Dokumentation

Wichtige Quellen:

- ursprüngliches Arduino TNC von KI4MCW: https://sites.google.com/site/ki4mcw/Home/arduino-tnc
- M1GEO-Experimente: https://www.george-smart.co.uk/arduino/arduino_tnc/
- Arduino TNC SQ9MDD: https://hamspirit.pl/SQ9MDD/?p=337
- Arduino TNC Plus: https://hamspirit.pl/SQ9MDD/?p=541
- späterer SQ9MDD/SQ5RWU-Code: https://github.com/SQ9MDD/arduino_tnc
