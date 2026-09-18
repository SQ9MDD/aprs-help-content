---
title: APRX
description: Leichtgewichtiger APRS-Daemon für POSIX-Systeme mit iGate, Digipeater, mehreren Funkschnittstellen, Telemetrie und APRS-IS-Routing.
template: doc
tableOfContents: true
---

APRX ist spezialisierte APRS-Infrastruktursoftware, hauptsächlich für den Betrieb als **iGate, Digipeater oder Kombination aus beiden**.

Es läuft als Daemon auf POSIX-Systemen, insbesondere Linux, BSD und anderen Unix-artigen Systemen.

Offizielles Repository:

https://github.com/PhirePhly/aprx/

Projektseite:

https://thelifeofkenneth.com/aprx/

APRX ist in C geschrieben und als leichtgewichtige Software für dauerhaften Infrastrukturbetrieb konzipiert.

Es ist kein Audiomodem. Für die Funkanbindung nutzt es ein externes TNC, KISS-Modem, eine AX.25-Schnittstelle des Betriebssystems oder eine andere Quelle decodierter AX.25-Frames.

```text
Funkgerät
  |
TNC / KISS-Modem
  |
APRX
  |
APRS-IS
```

## Autoren und Geschichte

Die erste und zweite APRX-Generation wurden von **Matti Aarnio OH2MQK** entwickelt.

```text
2007-2014
```

In den Quellen wird APRX beschrieben als:

```text
2nd generation APRS iGate and digi
```

Seit 2014 übernahm **Kenneth W. Finnegan W6KWF** Pflege und Weiterentwicklung.

Repository:

https://github.com/PhirePhly/aprx/

APRX ist heute ein ausgereiftes Projekt und weiterhin in realen Installationen im Einsatz.

## APRX-Philosophie

APRX wurde mit geringen Anforderungen und wenigen Abhängigkeiten entwickelt.

Geeignet für:

- kleine Computer,
- Router,
- Embedded Linux,
- Raspberry Pi,
- ältere PCs,
- 24/7-Server.

Keine grafische Oberfläche ist erforderlich.

```text
System
  |
aprx daemon
  |
/etc/aprx.conf
```

## APRX ist kein Modem

APRX decodiert kein AFSK von einer Soundkarte.

Es benötigt bereits decodierte AX.25-Frames, zum Beispiel:

```text
Funkgerät
  |
VP-Digi
  |
KISS
  |
APRX
```

oder:

```text
Funkgerät
  |
Dire Wolf
  |
KISS
  |
APRX
```

APRX übernimmt Routing, Digipeating, iGate, APRS-IS, Filterung, Beacons, Telemetrie und Multi-Interface-Logik.

## RX iGate

```text
RF
 |
Funkgerät
 |
TNC
 |
APRX
 |
APRS-IS
```

Beispiel:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

## TX iGate

APRX kann ausgewählten Verkehr von APRS-IS auf RF übertragen.

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

Dazu kann verwendet werden:

```text
relay-type third-party
```

zusammen mit Filterung, Rate Limiting und Viscous Delay.

## Digipeater

APRX enthält einen leistungsfähigen Digipeater mit:

```text
WIDEn-N
```

und normalen AX.25-Aliasen.

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

## New-N

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

## Viscous Digipeating

APRX kann vor einer Wiederholung kurz warten.

Wird das Paket in dieser Zeit bereits von einem anderen Digipeater wiederholt, kann APRX die eigene Aussendung unterdrücken.

```text
Paket empfangen
     |
     v
kurze Wartezeit
     |
     +---- erneut gehört ---> DROP
     |
     +---- nicht gehört ----> TX
```

## Mehrere Empfänger

```text
Radio RX Nord ---> TNC ---\
                           \
Radio RX Süd ----> TNC ----> APRX ---> Radio TX
                           /
Radio RX Lokal ---> TNC --/
```

Zusätzlich:

```text
igate-group
```

## Mehrere Kanäle

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
anderes RF  ---> lokaler Kanal
```

## KISS-Schnittstellen

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

## TNC2-Monitormodus

APRX kann:

```text
TNC2
```

als Text-Monitorformat einlesen.

## Linux AX.25

```text
ax25-device
```

## DPRS

```text
D-PRS -> APRS
```

## APRS-IS

Server:

```text
server rotate.aprs2.net
```

Filter:

```text
filter "m/100"
```

## Filterung

Filterbar sind:

- Quelle,
- Ziel,
- Pfad,
- Payload,
- geografischer Bereich.

Beispiel:

```text
filter -b/CALL
```

## Rate Limiting

```text
ratelimit
srcratelimit
```

## Beacons

APRX kann Positionen, Items, Objekte, rohe APRS-Frames und Dateidaten senden.

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

## Telemetrie

Beispiele:

```text
RX
DROP
TX
```

## Erlang-Monitor

Typische Intervalle:

```text
1 Minute
10 Minuten
20 Minuten
```

## Logging

```text
aprx-rf.log
aprx.log
erlang.log
```

## Konfiguration

Hauptdatei:

```text
/etc/aprx.conf
```

Beispiel:

```text
mycall SQ9ABC-1

<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>

<interface>
    serial-device /dev/ttyUSB0 9600 8n1 KISS
</interface>
```

Sektionen:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

## APRX mit Dire Wolf

```text
Funkgerät
  |
Dire Wolf
  |
KISS
  |
APRX
  |
APRS-IS
```

Dire Wolf übernimmt AFSK, APRX die Infrastruktur-Logik.

## APRX mit Hardware-TNC

```text
Funkgerät
  |
VP-Digi / TNC-X / anderes TNC
  |
KISS
  |
APRX
```

## Geringe Anforderungen

APRX eignet sich für Raspberry Pi, kleine x86-Systeme, ältere Hardware, Embedded Linux und virtuelle Maschinen.

## Reife

APRX besitzt eine lange Geschichte und ist in vielen Installationen erprobt.

Die 2.9.1-Linie ist weiterhin in Debian verfügbar.

Heute sollte APRX vor allem als **ausgereifte Infrastruktursoftware** betrachtet werden.

## Was APRX nicht ist

APRX ist kein:

- Audiomodem,
- grafischer APRS-Client,
- APRS-Kartenprogramm,
- Stations-Tracker,
- täglicher Messaging-Client.

## Für wen ist APRX?

Geeignet für:

- RX iGate,
- TX iGate,
- Digipeater,
- Digi + iGate,
- Multi-Receiver-Knoten,
- mehrere Funkschnittstellen,
- Routing,
- Filterung,
- Telemetrie,
- 24/7-Betrieb.

## Lizenz

```text
BSD 3-Clause
```

## Zusammenfassung

```text
mehrere Empfänger
      |
      v
    APRX
   /    \
 Digi   iGate
   \    /
    RF / APRS-IS
```

APRX kombiniert iGate, New-N- und Viscous-Digipeating, mehrere Interfaces, KISS, Linux AX.25, Filterung, Rate Limiting, Beacons, Telemetrie, Kanalmonitoring, DPRS und APRS-IS.

## Dokumentation

- Repository: https://github.com/PhirePhly/aprx/
- Projektseite: https://thelifeofkenneth.com/aprx/
- Beispielkonfiguration: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- komplexe Konfiguration: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
