---
title: "FoxTrak-M"
slug: "foxtrak-m"
type: "hardware"
category: "tracker"
manufacturer: "Fox Delta"
status: "legacy"
protocols:
  - "APRS"
  - "AX.25"
  - "NMEA 0183"
tags:
  - "tracker"
  - "GPS"
  - "1200-baud"
  - "Bell-202"
  - "PIC"
source:
  - "https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf"
---

## 1. Grundinformationen

**FoxTrak-M** ist ein kompaktes APRS-Tracker-Modul von Fox Delta. Es wurde als kleines Modul zur Integration in ein eigenes Gerät oder Projekt entwickelt und nicht als vollständiges Standalone-Gerät in einem separaten Gehäuse.

Das Design basiert auf den Mikrocontrollern **PIC16F84A oder PIC16F628A**. Die Platine ist etwa **7 × 4 cm** groß und verwendet 0,1-Zoll-Stiftleisten.

FoxTrak-M ist eine Variante des früheren FoxTrak-Trackers. Der wesentliche konstruktive Unterschied besteht darin, dass D-Sub-Steckverbinder durch Stiftleisten ersetzt wurden, wodurch sich das Modul leichter in andere Geräte integrieren lässt.

## 2. Verwendungszweck

FoxTrak-M ist hauptsächlich vorgesehen für:

- automatische Übertragung der Stationsposition über APRS,
- Aufbau eines mobilen GPS-Trackers,
- Integration eines APRS-Trackers als Modul in ein eigenes Gerät,
- Betrieb mit dem FoxView-2-Terminal,
- Experimente mit eigener Firmware für den PIC-Mikrocontroller.

Das Gerät ist ein sendender Tracker. Es ist kein vollständiges APRS-Terminal und dekodiert keine empfangenen APRS-Frames.

## 3. Funktionsweise

Der Tracker empfängt Positionsdaten vom GPS im **NMEA**-Format, insbesondere Sätze wie:

```text
$GPRMC
```

Aus diesen Daten erzeugt er ein Packet-Radio-Signal mit **1200 bps**, das direkt dem Mikrofoneingang des Senders zugeführt werden kann.

Für den Grundbetrieb ist kein externer TNC erforderlich.

Automatische Beacons werden nur gesendet, wenn das Gerät gültige Positionsdaten vom GPS erhält.

## 4. APRS und unterstützte Funktionen

FoxTrak-M erfüllt die grundlegende Funktion eines APRS-Trackers, nämlich das periodische Senden von Positionsmeldungen.

Die Firmware von DK7IN unterstützt unter anderem:

- festes Beacon-Intervall,
- geschwindigkeitsabhängige Änderung des Beacon-Intervalls,
- zusätzliches Beacon nach einer größeren Richtungsänderung,
- konfigurierbare TX Delay,
- Konfiguration des Rufzeichens und weiterer APRS-Parameter,
- Erzeugung von Testtönen zur Einstellung des Audiopfads.

Das Gerät empfängt oder interpretiert keinen APRS-Verkehr vom Funkkanal.

## 5. Smart Beaconing

FoxTrak-M implementiert eine frühe Form des adaptiven Beaconings. Die Häufigkeit der Positionsaussendungen kann sich abhängig von der Geschwindigkeit ändern.

Es stehen zwei Parametersätze zur Verfügung, die per Schalter gewählt werden.

| Geschwindigkeit | Schnelleres Profil | Langsameres Profil |
|---|---:|---:|
| < 4 km/h | 25 min | 30 min |
| > 4 km/h | 4 min | 5 min |
| > 11 km/h | 120 s | 160 s |
| > 24 km/h | 60 s | 80 s |
| > 50 km/h | 30 s | 40 s |
| > 100 km/h | 20 s | 30 s |
| > 150 km/h | 10 s | 20 s |

Berücksichtigt wird die maximale Geschwindigkeit seit dem Senden des vorherigen Beacons.

Wenn die Geschwindigkeit **15 km/h** überschreitet, kann das Gerät auch nach einer deutlichen Richtungsänderung ein Beacon senden:

- mehr als **40°** im schnelleren Profil,
- mehr als **60°** im langsameren Profil.

Wenn die vorherige Aussendung weniger als 10 Sekunden zurückliegt, wird das nächste Beacon verzögert.

## 6. GPS-Schnittstelle und Konfiguration

Dieselbe serielle Schnittstelle dient zum:

- Empfang von Daten des GPS-Empfängers,
- Konfigurieren der im PIC gespeicherten Parameter.

Die Dokumentation beschreibt Konfigurationsprogramme für:

- DOS,
- Windows,
- Linux.

Außerdem konnte das TinyTrak-Konfigurationsprogramm verwendet werden.

Eine Beispielversion der Firmware meldete sich als:

```text
DK7IN V.1.6
```

Das feste Beacon-Intervall kann in 10-Sekunden-Schritten eingestellt werden, die TX Delay mit einer Auflösung von etwa 6,6 ms.

## 7. Funkpfad

FoxTrak-M erzeugt eine AFSK-Modulation nach dem im Packet Radio verwendeten **Bell-202**-System.

Die nominalen Tonfrequenzen sind:

- **1200 Hz**
- **2200 Hz**

Für Abgleich und Diagnose kann das Gerät Testtöne erzeugen.

Beispielhafte Terminalbefehle:

```text
ESC T 0
```

erzeugt einen 1200-Hz-Ton.

```text
ESC T 1
```

erzeugt einen 2200-Hz-Ton.

```text
ESC T M
```

erzeugt ein gemischtes 1200/2200-Hz-Signal.

Die in der Anleitung beschriebene Konfigurationskommunikation verwendet:

```text
4800 baud, 8N1
```

## 8. Verbindung zum Funkgerät

Der Funkgeräteanschluss stellt unter anderem folgende Signale bereit:

| Signal | Funktion |
|---|---|
| Audio In | audio z odbiornika do trackera |
| CD | Carrier Detect, jeśli dostępny |
| GND | masa |
| SW1 | wejście przycisku natychmiastowej transmisji |
| Audio Out | audio trackera do wejścia mikrofonowego radia |
| +5V | zasilanie modułu |

Das Modul ermöglicht die Einstellung des Audiopegels zum und vom Funkgerät.

Außerdem kann die PTT-Steuerung für einige Handfunkgeräte durch einen 2,2-kΩ-Widerstand angepasst werden.

## 9. GPS-/PC-Anschluss

Der zweite 8-polige 0,1-Zoll-Anschluss dient zur Kommunikation mit dem GPS oder einem Computer.

Die Dokumentation nennt folgende Signale:

| Signal | Funktion |
|---|---|
| GND | masa |
| S0 | dane szeregowe OUT |
| SI | dane szeregowe IN |
| PI | PTT IN |
| PO | PTT OUT |
| A | niewykorzystywane w FoxTrak-M |
| B | niewykorzystywane w FoxTrak-M |

## 10. Anzeigen und Bedienelemente

FoxTrak-M verfügt über LEDs zur Anzeige des Gerätezustands.

**CD**

Leuchtet, wenn die Aussendung einer anderen Station erkannt wird.

**GPS**

Leuchtet bei gültigen GPS-Positionsdaten. Sie blinkt, wenn GPS-Daten empfangen werden, die Position jedoch nicht gültig ist.

**PTT**

Leuchtet, während der Sender getastet ist.

Beim Start führen die LEDs eine Initialisierungssequenz aus, anschließend kann ein Beacon gesendet werden.

## 11. Stromversorgung

FoxTrak-M benötigt eine externe Stromversorgung:

```text
+5 V DC
```

Das Modul besitzt keinen eigenen 5-V-Spannungsregler.

Es versorgt auch den GPS-Empfänger nicht, daher benötigt das GPS eine eigene Stromversorgung.

## 12. Firmware

Die in der Dokumentation beschriebene Hauptfirmware wurde von **DK7IN** entwickelt.

Die Hardware erlaubt den Austausch der Firmware und bei Bedarf auch den Ersatz des PIC16F84A durch einen PIC16F628A.

FoxTrak-M wurde somit nicht nur als fertiger Tracker, sondern auch als Plattform für Experimente mit eigener APRS-Software entwickelt.

## 13. Einschränkungen

Die wichtigsten in der Dokumentation genannten Einschränkungen sind:

- keine Dekodierung empfangener APRS-Frames,
- keine vollständige TNC-Funktionalität,
- das Gerät konzentriert sich auf die Übertragung der GPS-Position,
- externe 5-V-Stromversorgung erforderlich,
- das GPS wird nicht vom Modul versorgt,
- das Design basiert auf älteren PIC-Mikrocontrollern.

## 14. Anwendungen

Typische Anwendungen von FoxTrak-M:

- APRS-Tracker im Fahrzeug,
- in ein anderes Gerät eingebauter Tracker,
- mobile GPS/APRS-Station,
- experimentelle APRS-Plattform,
- Ausbildungsprojekt zu AFSK, AX.25 und APRS.

## 15. Klassifikation

| Merkmal | Wert |
|---|---|
| Typ | Hardware |
| Klasse | APRS-Tracker |
| Betriebsrichtung | TX |
| Positionsquelle | GPS |
| GPS-Daten | NMEA, GPRMC |
| Funkschicht | AFSK 1200 bps |
| Modulation | Bell 202 |
| Mikrocontroller | PIC16F84A / PIC16F628A |
| Versorgung | 5 V DC |
| Funkgeräte-Schnittstelle | Audio + PTT |
| APRS-Empfang | nein |
| Smart Beaconing | ja |
| PC-Konfiguration | ja |

## 16. Historische Bedeutung

FoxTrak-M steht für eine Klasse einfacher Hardware-APRS-Tracker, mit denen sich eine mobile Positionsstation ohne vollständigen TNC und ohne Computer aufbauen ließ.

Sein Aufbau veranschaulicht gut das klassische Modell eines APRS-Trackers:

```text
GPS
  ↓
NMEA
  ↓
mikrokontroler
  ↓
APRS / AX.25
  ↓
AFSK 1200 bps
  ↓
radio
```

## 17. Quellen

Grundlage dieser Beschreibung ist die Dokumentation des Herstellers:

**Fox Delta, FD - FoxTrak-M, Technical information: Compact PIC 16F84A/628A APRS Tracker Module**

https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf

Das Dokument ist auf den 8. Juni 2008 datiert.
