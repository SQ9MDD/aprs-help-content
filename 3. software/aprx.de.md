---
title: APRX
description: Leichtgewichtiger APRS-Daemon für POSIX-Systeme mit iGate, Digipeater, mehreren Funkschnittstellen, Telemetrie und APRS-IS-Routing.
template: doc
tableOfContents: true
---

APRX ist spezialisierte APRS-Infrastruktursoftware, die vor allem als **iGate, Digipeater oder Kombination aus beiden Funktionen** eingesetzt wird.

Das Programm läuft als Daemon auf POSIX-kompatiblen Systemen, insbesondere Linux, BSD und anderen Unix-artigen Systemen.

Offizielles Repository:

https://github.com/PhirePhly/aprx/

Projektseite:

https://thelifeofkenneth.com/aprx/

APRX ist in C geschrieben und wurde von Anfang an als leichtgewichtige Software für dauerhaften Infrastrukturbetrieb konzipiert.

Es ist kein Audiomodem.

Für die Verbindung zum Funkgerät verwendet APRX ein externes TNC, KISS-Modem, eine AX.25-Schnittstelle des Betriebssystems oder eine andere unterstützte Quelle bereits decodierter Frames.

Ein typischer Aufbau kann so aussehen:

```text
Funkgerät
  |
TNC / KISS-Modem
  |
APRX
  |
APRS-IS
```

oder als Digipeater:

```text
Funkgerät
  |
TNC / Modem
  |
APRX
  |
Funkgerät
```

In komplexeren Installationen kann APRX mehrere Empfänger, Sender und Kanäle gleichzeitig bedienen.

## Autoren und Projektgeschichte

Autor der ersten und zweiten Generation von APRX war **Matti Aarnio OH2MQK**.

Der von Matti entwickelte Code stammt aus dem Zeitraum:

```text
2007-2014
```

In den Quelltext-Headern wird APRX beschrieben als:

```text
2nd generation APRS iGate and digi
```

Seit 2014 übernahm **Kenneth W. Finnegan W6KWF** die Pflege und weitere Entwicklung des Projekts.

Das aktuelle Repository:

https://github.com/PhirePhly/aprx/

enthält Code aus beiden Entwicklungsphasen.

APRX ist ein ausgereiftes Projekt. Die Entwicklung verläuft heute deutlich langsamer als bei neuen Projekten, die Software ist aber weiterhin in Linux-Distributionen enthalten und in realen APRS-Installationen im Einsatz.

APRX 2.9.1 ist unter anderem in Debian verfügbar.

## APRX-Philosophie

Ein grundlegendes Ziel von APRX war Infrastruktursoftware mit geringen Anforderungen.

Der Quellcode beschreibt das Projekt als Lösung mit möglichst wenigen Abhängigkeiten außerhalb der üblichen UNIX-Systembibliotheken.

Dadurch eignet sich APRX gut für:

- kleine Computer,
- Router,
- Embedded-Linux-Geräte,
- Raspberry Pi,
- ältere PCs,
- dauerhaft laufende Server.

Eine grafische Oberfläche wird nicht benötigt.

Ein typischer Betrieb sieht einfach so aus:

```text
System
  |
aprx daemon
  |
/etc/aprx.conf
```

## APRX ist kein Modem

Diese Unterscheidung ist wichtig.

APRX decodiert kein AFSK von einer Soundkarte.

Es benötigt eine Quelle bereits decodierter AX.25-Frames.

Zum Beispiel:

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

oder ein klassisches Hardware-TNC.

APRX übernimmt:

- Frame-Routing,
- Digipeating,
- iGate,
- APRS-IS,
- Filterung,
- Beacons,
- Telemetrie,
- Logik für mehrere Interfaces.

Das Modem übernimmt die Funkschicht.

## RX iGate

Eine der häufigsten Anwendungen von APRX ist ein **Receive-only iGate**.

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

Über Funk empfangene Frames werden an APRS-IS weitergeleitet.

Eine minimale Konfiguration benötigt hauptsächlich:

- eigenes Rufzeichen,
- APRS-IS-Passcode,
- APRS-IS-Server,
- Funkschnittstelle.

Beispiel:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

APRX kann somit ein einfaches iGate ohne RF-Aussendung realisieren.

## TX iGate

APRX unterstützt auch die Übertragung von APRS-IS auf RF.

Damit lässt sich ein vollständiges bidirektionales iGate aufbauen.

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

Ein TX-iGate sollte nicht den gesamten APRS-IS-Verkehr auf den Funkkanal übertragen.

APRX bietet Mechanismen zur Begrenzung und Filterung dieses Verkehrs.

Die Quelle `APRSIS` kann in der Digipeater-Sektion als eigene Quelle definiert werden:

```text
relay-type third-party
```

Zusätzlich können verwendet werden:

- Filterung,
- Rate Limiting,
- Viscous Delay.

Damit lässt sich der Verkehr von Internet zu RF sehr genau kontrollieren.

## Digipeater

APRX enthält einen leistungsfähigen eingebauten Digipeater.

Unterstützt werden unter anderem:

```text
WIDEn-N
```

und normale AX.25-Aliase.

Eine Digipeater-Sektion besitzt einen Sender, kann aber Pakete aus mehreren Quellen annehmen.

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

Damit lassen sich Systeme aufbauen mit:

- mehreren Empfängern,
- Receiver Diversity,
- getrennten Richtantennen,
- verschiedenen Kanälen,
- gemeinsamem Sender.

Diese Multi-Source-Architektur war eine der Eigenschaften, die APRX von einfachen Digipeater-Steuerungen unterschied.

## New-N

APRX unterstützt das moderne APRS-New-N-Verfahren.

Mögliche Pfade sind zum Beispiel:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

Die Konfiguration kann maximale angeforderte und ausgeführte Hop-Zahlen festlegen.

Eigene Alias-Schlüssel können ebenfalls definiert werden.

Damit kann APRX sowohl als lokaler Fill-in-Digipeater als auch als Bestandteil größerer Infrastruktur arbeiten.

## Viscous Digipeating

Eine charakteristische APRX-Funktion ist **Viscous Digipeating**.

Der Digipeater muss ein Paket nicht sofort wiederholen.

Er wartet zunächst kurz.

Wenn er in dieser Zeit hört, dass eine andere Station das Paket bereits wiederholt hat, kann er die eigene Aussendung unterdrücken.

```text
Paket empfangen
     |
     v
kurze Wartezeit
     |
     +---- erneut gehört ---> DROP
     |
     +---- keine Wiederholung -> TX
```

Dadurch werden Duplikate in Gebieten reduziert, in denen sich die Reichweiten mehrerer Digipeater überlappen.

APRX unterstützt Viscous Delay innerhalb eines Interfaces und auch zwischen verschiedenen Interfaces.

## Mehrere Empfänger

APRX wurde auch für größere Knoten entwickelt.

Es kann Frames gleichzeitig aus mehreren Interfaces empfangen.

```text
Radio RX Nord ---> TNC ---\
                           \
Radio RX Süd ----> TNC ----> APRX ---> Radio TX
                           /
Radio RX Lokal ---> TNC --/
```

So lassen sich Knoten mit mehreren Empfängern und einem gemeinsamen Sender aufbauen.

Interfaces können außerdem gruppiert werden mit:

```text
igate-group
```

Das hilft bei korrekter iGate-Logik in Installationen mit mehreren Empfängern und Sendern auf demselben Kanal.

## Mehrere Kanäle

APRX ist nicht auf einen Funkkanal begrenzt.

Mehrere Interfaces und mehrere Digipeater-Sektionen können eingerichtet werden.

Eine Instanz kann dadurch einen komplexeren Knoten bedienen.

Zum Beispiel:

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
anderes RF  ---> lokaler Kanal
```

Die genaue Konfiguration hängt von den verwendeten Modems und Ports ab.

## KISS-Schnittstellen

APRX kann mit klassischen seriellen TNCs arbeiten.

Unterstützt wird:

```text
KISS
```

sowie mehrere verwandte Erweiterungen.

In der Konfiguration finden sich unter anderem:

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

Damit kann APRX mit verschiedenen Generationen und Typen von Hardware-TNCs und Modems zusammenarbeiten.

## TNC2-Monitormodus

APRX kann Daten auch im:

```text
TNC2
```

Monitorformat empfangen, dem bekannten Textformat klassischer TNCs.

Dadurch können Quellen integriert werden, die kein KISS anbieten, aber empfangene Frames im Monitorformat ausgeben.

## Linux AX.25

Unter Linux kann APRX außerdem direkt Kernel-AX.25-Schnittstellen verwenden.

Die Konfiguration kann benutzen:

```text
ax25-device
```

APRX empfängt dann Frames aus dem AX.25-Stack des Kernels.

Diese Funktion ist nicht erforderlich.

APRX kann auch ohne AX.25-Unterstützung im Kernel betrieben werden.

## DPRS

APRX besitzt außerdem **D-PRS**-Unterstützung.

DPRS-Daten können empfangen und in APRS umgesetzt werden.

Im Projekt ist Gateway-Funktionalität vorhanden:

```text
D-PRS -> APRS
```

Sie wurde hauptsächlich zur Integration von Positionsdaten aus digitalen Funksystemen entwickelt.

## APRS-IS

APRX besitzt einen eigenen APRS-IS-Client.

Es kann sich mit Tier2-Servern verbinden und mehrere Serverdefinitionen verwalten.

Beispiel:

```text
server rotate.aprs2.net
```

Auch APRS-IS-Filter können verwendet werden.

Beispiel:

```text
filter "m/100"
```

für Verkehr innerhalb von 100 km um die Stationsposition.

Weitere Filter entsprechend dem javAPRSSrvr/APRS-IS-Mechanismus sind ebenfalls möglich.

## Verkehrsfilterung

APRX besitzt umfangreiche Filtermechanismen.

Gefiltert werden kann unter anderem nach:

- Quelle,
- Ziel,
- Pfad,
- Payload,
- geografischem Gebiet.

Es gibt reguläre Ausdrucksfilter und geografische Filter.

So kann beispielsweise ein bestimmtes Rufzeichen blockiert oder die Wiederholung auf ein bestimmtes Gebiet begrenzt werden.

Beispiel:

```text
filter -b/CALL
```

Filter können getrennt für verschiedene Digipeater-Quellen angewendet werden.

## Rate Limiting

APRX kann die Zahl der wiederholten Frames begrenzen.

Ein globales Digipeater-Limit wird konfiguriert mit:

```text
ratelimit
```

und ein Limit für einzelne Quellrufzeichen mit:

```text
srcratelimit
```

Damit kann der Funkkanal vor einer Station geschützt werden, die übermäßig viel Verkehr erzeugt.

## Beacons

APRX besitzt einen eigenen Beacon-Generator.

Er kann unter anderem erzeugen:

- Positionen,
- Items,
- Objekte,
- rohe APRS-Frames,
- Daten aus Dateien.

Beispiel:

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

Beacons können über ein bestimmtes Interface und mit einem bestimmten Pfad gesendet werden.

APRX verteilt die Sendezeiten bewusst, anstatt alle Beacons exakt gleichzeitig zu senden.

Die Intervalle werden außerdem leicht randomisiert, um die statistische Kollisionswahrscheinlichkeit zu reduzieren.

## Telemetrie

APRX besitzt ein eigenes System für Infrastrukturtelemetrie.

Das Programm sammelt Verkehrsdaten der einzelnen Interfaces.

Unter anderem werden überwacht:

```text
RX
DROP
TX
```

sowie Paket- und Bytezähler.

Die Daten können zeitlich aggregiert und als APRS-Telemetrie veröffentlicht werden.

APRX kann diese Telemetrie an APRS-IS und optional auch auf RF senden.

## Erlang-Monitor

Ein interessanter Bestandteil von APRX ist der eingebaute **Erlang-Monitor**.

Der Name stammt von der Einheit zur Beschreibung der Kanalbelegung.

APRX misst den tatsächlichen Verkehr auf den Interfaces und sammelt Statistiken in Intervallen wie:

```text
1 Minute
10 Minuten
20 Minuten
```

Damit lässt sich die Auslastung des Funkkanals beobachten.

Die Daten können in einer Statusdatei gespeichert werden, sodass kurze Neustarts nicht zwangsläufig die laufenden Statistiken verlieren.

## Logging

APRX kann getrennte Logs führen für:

- empfangene RF-Frames,
- APRS-IS-Verbindungen,
- DPRS,
- Erlang-Statistiken.

Beispieldateien:

```text
aprx-rf.log
aprx.log
erlang.log
```

Die Logs können normal vom Betriebssystem rotiert werden.

## Konfiguration

Die Hauptkonfigurationsdatei ist normalerweise:

```text
/etc/aprx.conf
```

Die Syntax erinnert an Apache-Konfigurationen.

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

Konfigurationssektionen umfassen unter anderem:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

Es können mehrere Interfaces, Beacons und Telemetrie-Sektionen existieren.

## APRX mit Dire Wolf

Eine sehr praktische Kombination ist APRX für die Infrastruktur-Logik und ein externes Modem für die Funkschicht.

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

Dire Wolf übernimmt AFSK-Modulation und -Demodulation.

APRX übernimmt:

- Digipeating,
- iGate,
- Filter,
- mehrere Quellen,
- Telemetrie,
- Routing.

Diese Trennung passt zur APRX-Architektur, die kein eigenes Audiomodem benötigt.

## APRX mit Hardware-TNC

Analog kann ein Hardware-KISS-Gerät verwendet werden:

```text
Funkgerät
  |
VP-Digi / TNC-X / anderes TNC
  |
KISS
  |
APRX
```

Durch Standard-KISS bleibt APRX vom konkreten Modem unabhängig.

## Geringe Anforderungen

APRX ist in C geschrieben und besitzt keine grafische Oberfläche.

Die Hardwareanforderungen sind entsprechend gering.

Gut geeignet sind:

- Raspberry Pi,
- kleine x86-Rechner,
- ältere Hardware,
- Embedded-Linux-Geräte,
- virtuelle Maschinen.

In typischen Infrastrukturinstallationen kann APRX monatelang ohne Bedienereingriff laufen.

## Stabilität und Reife

APRX hat eine lange Geschichte.

Die grundlegende Architektur entstand vor vielen Jahren und wurde in zahlreichen realen APRS-Installationen erprobt.

Die 2.9.1-Linie ist weiterhin in Debian verfügbar.

Das Repository besitzt weiterhin Issues und Pull Requests, aber APRX sollte heute vor allem als **ausgereifte Infrastruktursoftware** betrachtet werden, nicht als schnell veränderliche Anwendung.

## Was APRX nicht ist

APRX ist kein:

- Audiomodem,
- grafischer APRS-Client,
- APRS-Kartenprogramm,
- Stations-Tracking-Programm,
- täglicher Messaging-Client für Operatoren.

Sein Einsatzgebiet ist hauptsächlich Infrastruktur.

Am einfachsten dargestellt:

```text
Funkgerät
  |
Modem / TNC
  |
APRX
  |
APRS-Infrastrukturlogik
  |
APRS-IS / RF
```

## Für wen ist APRX?

APRX eignet sich besonders, wenn benötigt wird:

- RX iGate,
- TX iGate,
- Digipeater,
- kombiniertes Digi + iGate,
- Knoten mit mehreren Empfängern,
- Installation mit mehreren Funkschnittstellen,
- Routing zwischen Ports,
- Verkehrsfilterung,
- Infrastrukturtelemetrie,
- sehr leichter 24/7-Daemon.

Besonders interessant ist APRX dort, wo der Betreiber die Infrastruktur-Logik vollständig kontrollieren und das Funkmodem als getrennte Schicht behandeln möchte.

## Lizenz

APRX ist Open-Source-Software.

Das Projekt wird unter folgender Lizenz bereitgestellt:

```text
BSD 3-Clause
```

Damit kann der Code gemäß den Lizenzbedingungen analysiert, verändert und genutzt werden.

## Zusammenfassung

APRX ist eines der klassischen Infrastrukturprogramme der APRS-Welt.

Seine größte Stärke ist nicht eine einzelne Funktion, sondern die Möglichkeit, einen vollständigen Knoten um einen Prozess herum aufzubauen:

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

Das Programm kombiniert:

- RX iGate,
- TX iGate,
- New-N Digipeating,
- Viscous Digipeating,
- mehrere Interfaces,
- KISS und Linux AX.25,
- Filterung,
- Rate Limiting,
- Beacons,
- Telemetrie,
- Überwachung der Kanalbelegung,
- DPRS,
- APRS-IS.

Dabei bleibt APRX ein kleiner C-Daemon ohne grafische Umgebung und ohne Bedarf an einem großen System.

Deshalb ist APRX seit vielen Jahren eine beliebte Wahl für dauerhaft arbeitende APRS-Infrastrukturstationen.

## Dokumentation

Wichtige Quellen:

- Repository: https://github.com/PhirePhly/aprx/
- Projektseite: https://thelifeofkenneth.com/aprx/
- Beispielkonfiguration: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- komplexe Konfiguration: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
