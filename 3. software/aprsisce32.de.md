---
title: APRSISCE/32
description: Leistungsfähiger APRS-Client für Windows und Windows Mobile mit Karten, RF, APRS-IS, Nachrichten, Objekten, MultiLine, NWS-WARN, Direction Finding, iGate und vielen weiteren APRS-Protokollfunktionen.
template: doc
tableOfContents: true
---

APRSISCE/32 ist einer der umfangreichsten klassischen APRS-Clients.

Das Programm kombiniert in einer Anwendung:

- APRS-Karte,
- APRS-IS-Verbindung,
- Funkgerät- und TNC-Unterstützung,
- Nachrichten,
- Objekte und Items,
- Telemetrie,
- Wetter,
- iGate,
- Beaconing,
- Stationsverfolgung,
- Direction Finding,
- MultiLine-Objekte und Flächen,
- NWS-WARN,
- erweiterte APRS-IS-Filter.

Das Projekt wurde von **Lynn Deffenbaugh KJ4ERJ** entwickelt.

Dokumentation:

https://aprsisce.wikidot.com/

Downloads:

https://aprsisce.wikidot.com/downloads

Benutzer- und Supportgruppe:

https://groups.io/g/APRSISCE

APRSISCE/32 ist besonders interessant, weil deutlich mehr vom APRS-Protokoll implementiert wird als nur Positionsmeldungen und einfache Nachrichten.

## APRSISCE und APRSIS32

Der Projektname umfasst zwei Hauptvarianten.

### APRSISCE

APRSISCE war für Geräte mit folgenden Systemen vorgesehen:

- Windows Mobile 5,
- Windows Mobile 6,
- Windows Mobile 6.1,
- Windows Mobile 6.5,
- Windows CE.

Damit war ein vollständiger APRS-Client auf PDAs und mobilen Terminals möglich.

### APRSIS32

APRSIS32 ist die Desktop-Version für Windows.

Trotz des Namens läuft sie auch auf 64-Bit-Windows.

Die aktuelle Downloadseite bietet einen funktionsfähigen Build für 32- und 64-Bit-Windows, einschließlich Windows 10 und Windows 11.

Die Windows-Mobile- und Windows-CE-Versionen sind heute hauptsächlich historisch interessant.

## Vollständiges APRS-Terminal

Ein einfacher Betrieb sieht so aus:

```text
APRS-IS
   |
   |
APRSIS32
   |
  Karte
```

Die vollständigen Möglichkeiten zeigen sich mit angeschlossenem Funkgerät:

```text
Funkgerät
  |
TNC / Modem
  |
APRSIS32
  |
APRS-IS
```

Eine Anwendung kann dann gleichzeitig:

- Stationen über RF empfangen,
- sie auf der Karte darstellen,
- eigene Beacons senden,
- Nachrichten verarbeiten,
- Pakete zu APRS-IS gaten,
- ausgewählten APRS-IS-Verkehr auf RF senden,
- Objekte erstellen und senden.

## Karten

Eine zentrale Funktion ist die Karte.

APRSISCE/32 verwendet Kachelkarten, vor allem OpenStreetMap.

Andere Kartenquellen können ebenfalls definiert werden.

Kacheln werden lokal gespeichert, sodass bereits geladene Gebiete auch bei eingeschränkter Internetverbindung verfügbar bleiben.

## Stationen auf der Karte

Je nach Paketinhalt kann APRSISCE/32 anzeigen:

- Rufzeichen,
- APRS-Symbol,
- Kommentar,
- Höhe,
- Geschwindigkeit,
- Kurs,
- Wetter,
- Bewegungsverlauf,
- Betriebsfrequenz,
- Nachrichteninformationen,
- Positionsgenauigkeit.

Auch **position ambiguity** wird unterstützt.

## Stationsverfolgung

Eine ausgewählte Station kann auf der Karte verfolgt werden.

Zusätzlich gibt es:

```text
MultiTrack
```

für ein separates Tracking-Fenster.

## Bewegungsverlauf

Stationspositionen können als Spur dargestellt und als:

```text
GPX
```

gespeichert werden.

## Scroller

Ein charakteristisches APRSIS32-Element ist der **Scroller** links im Hauptfenster.

Er zeigt schnell, was im Netz geschieht.

Beispiele:

```text
*  direkt über RF empfangen
#  APRS-IS -> RF bezogenes Paket
@  vom lokalen Digipeater wiederholtes Paket
```

Farben und zusätzliche Markierungen helfen bei der Verkehrsanalyse.

## Funkunterstützung

Unterstützt werden unter anderem:

- klassische TNCs,
- KISS TNC,
- Kenwood TM-D700,
- Kenwood TM-D710,
- Kenwood TH-D7,
- Kenwood TH-D72,
- Kenwood TH-D74,
- Argent OT-USB,
- TinyTrak4,
- AGWPE,
- UZ7HO SoundModem,
- Dire Wolf.

Serielle und Netzwerkverbindungen werden unterstützt.

## KISS

Direkte KISS-Unterstützung umfasst:

```text
KISS
Simply KISS
```

`Simply KISS` ist für Geräte vorgesehen, die direkt im KISS-Modus starten.

## AGW

APRSIS32 kann die AGW-Schnittstelle verwenden.

Dadurch ist die Zusammenarbeit mit Softwaremodems wie:

- UZ7HO SoundModem,
- Dire Wolf

möglich.

```text
Funkgerät
  |
Soundkarte
  |
Dire Wolf
  |
AGW
  |
APRSIS32
```

## Kenwood TM-D710

Die Integration mit Kenwood-APRS-Funkgeräten ist besonders umfangreich.

APRSISCE/32 kann beim TM-D710 auch Daten darstellen, die das Funkgerät selbst nicht zeigt, darunter:

- Direction-Finding-Berichte,
- MultiLine-Objekte,
- vollständige Track-Historie.

## APRS-IS

APRSISCE/32 kann gleichzeitig Daten aus:

```text
RF + APRS-IS
```

empfangen und zu einem gemeinsamen Lagebild zusammenführen.

## Erweiterte APRS-IS-Filter

Filter sind möglich nach:

- Gebiet,
- Radius,
- Rufzeichen,
- Präfix,
- Digipeater,
- iGate,
- Objekten,
- Symbol,
- Pakettyp,
- Gruppennachrichten.

Beispiel:

```text
m/50
```

## iGate

APRSIS32 kann als iGate arbeiten.

```text
RF
 |
Funkgerät
 |
APRSIS32
 |
APRS-IS
```

Portfunktionen umfassen unter anderem:

- RF to IS,
- IS to RF,
- Messages,
- Bulletins/Objects,
- Beacon,
- Telemetry,
- transmit enable.

## Digipeater

APRSIS32 besitzt auch eine Digipeater-Funktion.

Zum Beispiel:

```text
WIDE1-1=WIDE1*
```

## APRS-Nachrichten

Unterstützt werden:

- Textnachrichten,
- Nachrichten-IDs,
- ACK,
- Wiederholungen,
- Gespräche mit mehreren Stationen.

Auch Wege wie:

```text
RF
 |
iGate
 |
APRS-IS
 |
iGate
 |
RF
```

werden korrekt verarbeitet.

## Message-able

Mit:

```text
View Message-able
```

können vermutlich nachrichtenfähige Stationen hervorgehoben werden.

## Objekte

APRSISCE/32 besitzt umfangreiche Objektfunktionen.

Objekte können darstellen:

- Relais,
- Frequenzen,
- Treffpunkte,
- Hilfspunkte,
- Gefahren,
- technische Stationen,
- Aktivitätsorte.

Mögliche Felder:

- Position,
- Symbol,
- Kommentar,
- Pfad,
- Intervall,
- Frequenz,
- CTCSS,
- QSY-Informationen.

## Frequenzobjekte

APRS Frequency Specification wird korrekt interpretiert.

Beispiel:

```text
145.650MHz T077 -060
```

## QRU, APRS Info-kiosk

**QRU** erlaubt das lokale Speichern vieler Objekte und deren Übertragung nur auf Anfrage.

```text
mobile Station
     |
     | INFO
     v
QRU-Server
     |
     | Gruppenliste
     v
mobile Station
```

Anfragen können zum Beispiel sein:

```text
FUEL
FOOD
HOSP
RP2M
```

## MultiLine

Die Erweiterung **MultiLine** ermöglicht:

- Linien,
- Routen,
- Grenzen,
- Polygone,
- Flächen.

APRSISCE/32 kann solche Daten empfangen und erzeugen.

Komprimierte Koordinaten werden ebenfalls unterstützt.

## Flächenobjekte

Mit MultiLine können ganze Gebiete dargestellt werden, zum Beispiel:

- Einsatzgebiet,
- Sperrzone,
- Suchgebiet,
- Veranstaltungsgelände,
- Gefahrenbereich,
- Route.

```text
+-------------------+
|                   |
|    EINSATZGEBIET  |
|                   |
+-------------------+
```

## NWS-WARN

Eine der fortgeschrittensten Funktionen ist die Unterstützung von **National Weather Service**-Warnungen.

APRSISCE/32 kann:

- NWS-Objekte empfangen,
- Warngebiete identifizieren,
- Warnungen auf der Karte anzeigen,
- lokale Shapefiles verwenden,
- IDs realen Grenzen zuordnen.

Verwendet werden:

```text
SHP
SHX
DBF
```

## Warum NWS-WARN wichtig ist

```text
kurze APRS-Information
        |
        v
Gebietskennung
        |
        v
lokales Shapefile
        |
        v
Warngebiet auf der Karte
```

Dies ist eine sehr effiziente Methode zur Übertragung komplexer Flächeninformationen über einen langsamen APRS-Kanal.

## Direction Finding

APRS definiert auch Formate für Funkpeilung.

APRSISCE/32 kann **Direction-Finding**-Pakete interpretieren und darstellen.

Unter anderem:

```text
DF circles
```

## Triangulation

Messungen aus mehreren Standorten können gemeinsam dargestellt werden.

Nützlich für:

- Störungssuche,
- Fox Hunting,
- Senderlokalisierung,
- Feldübungen.

## Wetter

APRS-Wetterstationen werden unterstützt.

Eine externe Anwendung kann:

```text
wxnow.txt
```

aktualisieren, worauf APRSISCE/32 ein gültiges APRS-Wetterpaket erzeugen und über APRS-IS, RF oder beide Wege senden kann.

## Telemetrie

Standard-APRS-Telemetrie wird unterstützt.

## APRS Queries

Unterstützt werden unter anderem:

```text
?APRS?
?IGATE?
?WX?
```

## Item-In-Message

Unterstützt wird außerdem:

```text
Item-In-Message
```

## Satelliten

APRSISCE/32 kann TLE-Daten nutzen, Satellitenpositionen berechnen und Footprints als MultiLine darstellen.

## Duplikate

Duplikaterkennung ist besonders wichtig beim Tracking mobiler Stationen.

## Beaconing

Positionsquellen können sein:

- feste Konfiguration,
- GPS,
- NMEA vom Funkport,
- USB-GPS,
- Bluetooth-GPS.

## GPS

Unterstützt wird:

```text
NMEA
```

## Windows, Linux und Wine

APRSIS32 ist eine Windows-Anwendung.

Unter Linux kann es mit:

```text
Wine
```

oder in einer Windows-VM betrieben werden.

## Aktueller Projektstatus

APRSISCE/32 besitzt eine lange Geschichte.

Die Downloadseite wurde 2025 aktualisiert und stellt weiterhin einen funktionsfähigen APRSIS32-Build für moderne Windows-Versionen bereit.

## APRS-Protokollkompatibilität

Unterstützt werden unter anderem:

- Positionen,
- komprimierte Positionen,
- Symbole und Overlays,
- Kommentare,
- Nachrichten,
- ACK,
- Status,
- Telemetrie,
- Wetter,
- Objekte,
- Items,
- Frequenzen,
- APRS Queries,
- NWS-Wetterobjekte,
- MultiLine,
- Flächenobjekte,
- Direction-Finding-Daten,
- Item-In-Message.

## Für wen ist APRSISCE/32?

Für Operatoren, die:

- lokalen APRS-Verkehr beobachten,
- Karten nutzen,
- ein eigenes Funkgerät anschließen,
- APRS-Nachrichten austauschen,
- ein iGate betreiben,
- Objekte erstellen,
- Flächen und Routen erzeugen,
- Direction Finding ausprobieren,
- Wetter und Warnungen analysieren,
- mehrere RF-Ports nutzen,
- fortgeschrittene APRS-Funktionen kennenlernen möchten.

## Zusammenfassung

APRSISCE/32 gehört zu den vollständigsten klassischen APRS-Clients.

Es kombiniert Bedien-, Karten- und Infrastrukturfunktionen.

Unterstützt werden auch seltenere APRS-Funktionen:

- Flächenobjekte,
- MultiLine,
- Direction Finding,
- QRU,
- APRS Queries,
- NWS-WARN,
- Shapefiles,
- Telemetrie,
- Wetter,
- Satelliten.

## Dokumentation

Wichtige Quellen:

- Projektseite: https://aprsisce.wikidot.com/
- Downloads: https://aprsisce.wikidot.com/downloads
- Funktionsdokumentation: https://aprsisce.wikidot.com/en-contexthelp
- Hardware und Software: https://aprsisce.wikidot.com/doc:compatible-hardware-software
- APRS-IS-Filter: https://aprsisce.wikidot.com/aprs-is-filters
- NWS: https://aprsisce.wikidot.com/en-nws
- Supportgruppe: https://groups.io/g/APRSISCE

Praktische Materialien auf Polnisch:

- APRSIS32-Suche: https://hamspirit.pl/SQ9MDD/?s=aprsis
- erste Schritte und Funkgerät: https://hamspirit.pl/SQ9MDD/?p=1239
- temporärer Digipeater: https://hamspirit.pl/SQ9MDD/?p=1202
- Objekte: https://hamspirit.pl/SQ9MDD/?p=1140
- Flächenobjekte: https://hamspirit.pl/SQ9MDD/?p=1170
- QRU Info-kiosk: https://hamspirit.pl/SQ9MDD/?p=1384
- Direction Finding und Triangulation: https://hamspirit.pl/SQ9MDD/?p=1090
