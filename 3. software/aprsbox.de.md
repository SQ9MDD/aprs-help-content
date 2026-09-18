---
title: APRSBox
description: Moderne APRS-Konsole für Linux und Raspberry Pi mit Packet Routing, DIGI, iGate, APRS-IS, Messaging, Karte, Warnungen, Statistiken und Ausbreitungsanalyse.
template: doc
tableOfContents: true
---

**APRSBox** ist eine quelloffene Software zum Aufbau einer vollständigen APRS-Station unter Linux. Das Projekt wird von **SQ9MDD** entwickelt und unter der Lizenz **GPL-3.0** veröffentlicht.

Es handelt sich nicht nur um ein iGate- oder Digipeater-Programm. APRSBox vereint in einer Anwendung die Funktionen einer APRS-Heimstation, eines Digipeaters, iGates, APRS-IS-Clients, Verkehrsmonitors, Nachrichtensystems, einer Karte, Warnanzeige, Statistik und umfangreicher Diagnosewerkzeuge.

Das Projekt ist vor allem für den Dauerbetrieb auf kleinen Rechnern wie dem Raspberry Pi ausgelegt, kann aber auch auf anderen Linux-Systemen betrieben werden.

Offizielles Repository:

https://github.com/SQ9MDD/APRSBox

## Charakter des Projekts

APRSBox kann als moderne, browserbasierte APRS-Konsole betrachtet werden. Eine Installation kann gleichzeitig Funktionen übernehmen, für die bei einer klassischen Station oft mehrere unabhängige Programme benötigt werden.

APRSBox kann unter anderem eingesetzt werden als:

- APRS-Heimstation,
- Digipeater,
- Fill-in-Digipeater,
- RX-iGate,
- kontrolliertes TX-iGate,
- Messaging-iGate,
- APRS-IS-Client,
- Wetterstation,
- Monitor des lokalen APRS-Kanals,
- Warnterminal,
- Ausbreitungsanalysator,
- Knoten mit mehreren Funkinterfaces.

APRSBox ersetzt kein Funkmodem. Für die Verbindung zum Funkgerät wird ein externer TNC oder ein Modem mit KISS-Schnittstelle verwendet, zum Beispiel Dire Wolf, VP-Digi oder ein anderes kompatibles Gerät.

## Architektur

Die Anwendung ist in Python geschrieben und verwendet FastAPI.

Das System ist in zwei Hauptprozesse aufgeteilt:

- `app.main` - Weboberfläche, Konfiguration und Administration,
- `app.core_main` - APRS-Core-Prozess für den Laufzeitbetrieb.

APRS Core übernimmt unter anderem Empfang und Aussendung von Frames, AX.25- und APRS-Dekodierung, Packet Routing, DIGI, APRS-IS, Messaging, Scheduler, Objekte, Bulletins, Wetter, Statistiken und weitere Echtzeitaufgaben.

Konfiguration und Betriebsdaten werden in SQLite gespeichert. Die Trennung von GUI und APRS Core reduziert den Einfluss der Weboberfläche auf den kritischen Pfad der Frame-Verarbeitung.

## Schnittstellen und Datenquellen

APRSBox kann mehrere Schnittstellen gleichzeitig verwenden.

Unterstützt werden unter anderem:

- KISS TCP,
- KISS Serial,
- APRS-IS,
- OpenWebRX über MQTT.

Eine Installation kann dadurch Daten aus mehreren Quellen empfangen, mehrere Sender verwenden und den Verkehr anhand der Packet-Routing-Regeln zwischen ihnen verteilen.

## KISS TCP und KISS Serial

KISS TCP ermöglicht die Zusammenarbeit mit Modems und TNCs, die KISS über ein IP-Netz bereitstellen. Typische Beispiele sind Dire Wolf, VP-Digi oder ein entfernter Hardware-TNC.

KISS Serial ermöglicht den direkten Anschluss eines TNC über eine serielle Schnittstelle. Die Konfiguration umfasst Gerätepfad und Portgeschwindigkeit, während die Laufzeitlogik Mechanismen für einen stabilen Dauerbetrieb bereitstellt.

Eine KISS-Schnittstelle kann sowohl als RX-Quelle als auch als TX-Ziel verwendet werden.

## OpenWebRX MQTT

APRSBox besitzt eine Empfangsschnittstelle, die MQTT von OpenWebRX verwendet.

Neben klassischen APRS-Frames können Daten weiterer in OpenWebRX verfügbarer Decoder empfangen werden, darunter:

- SONDE,
- ADS-B.

Radiosonden- und ADS-B-Daten können im System als APRS-Objekte dargestellt und in der Oberfläche angezeigt werden.

OpenWebRX MQTT ist eine Empfangsquelle und kein direkter RF-Sender.

## APRS-IS

APRS-IS ist ein vollständiger Bestandteil der APRSBox-Architektur.

Die Konfiguration umfasst unter anderem:

- Server,
- Port,
- Login,
- Passcode,
- APRS-IS-Filter,
- Verbindungsdiagnose.

APRS-IS kann sowohl Quelle empfangener Daten als auch Ziel des Packet Routings sein.

## Packet Routing

Einer der wichtigsten Bestandteile von APRSBox ist **Packet Routing**.

Der Administrator erstellt Flows nach dem Modell:

```text
Quelle -> Filter -> Aktion / Ziel
```

Damit lassen sich unter anderem folgende Wege realisieren:

```text
RF -> RF
RF -> APRS-IS
APRS-IS -> RF
Local TX -> APRS-IS
```

Diese entsprechen DIGI-Betrieb, RX-iGate, kontrolliertem TX-iGate und der Weiterleitung lokal erzeugter Frames an APRS-IS.

Eine Regel kann auch mit einem Logeintrag oder dem bewussten Verwerfen des Frames enden.

## DIGI- und Packet-Routing-Filter

Flows können mehrere aufeinanderfolgende Filter verwenden.

Verfügbare Mechanismen umfassen unter anderem:

- Duplikaterkennung,
- Viscous Delay,
- Pfadanalyse,
- Strict-Modus,
- Direct-only-Empfang,
- DIGI-Schutz,
- Rufzeichenfilter,
- Pakettyp,
- APRS-Symbol,
- Entfernung,
- Rate Limiting.

Die Reihenfolge ausgewählter Filter wird kontrolliert, um Konfigurationen zu vermeiden, die fehlerhafte oder übermäßige Wiederholungen verursachen.

## Duplicate Filter und Viscous Delay

APRSBox besitzt eine Duplikaterkennung für den DIGI-Betrieb.

Sie kann mit **Viscous Delay** kombiniert werden, also einer kontrollierten Verzögerung vor der Wiederholung. Ein Fill-in-Digipeater kann vor der Aussendung warten und die Wiederholung abbrechen, wenn derselbe Frame in dieser Zeit bereits von einer anderen Station wiederholt wurde.

Dadurch werden unnötige Aussendungen und die Kanalbelegung reduziert.

## DIGI-Schutz und Aktualität von Frames

APRSBox enthält Schutzmechanismen gegen die Wiederholung von Frames, die ein Digipeater nicht erneut aussenden sollte.

Geprüft werden können unter anderem:

- ungültige Pfadelemente,
- bereits von der lokalen Station wiederholte Frames,
- Third-Party-Pakete,
- lokale APRS-Nachrichten und Queries,
- Situationen, in denen keine weitere Wiederholung erfolgen sollte.

Zusätzlich überwacht das System das Alter von Frames in Warteschlangen. Ein Paket, das wegen Last oder eines Transportproblems zu lange gewartet hat, kann verworfen werden, statt später als veraltete Information gesendet zu werden.

## RX-iGate und TX-iGate

In Richtung **RF -> APRS-IS** kann APRSBox als klassisches RX-iGate arbeiten. Dieser Pfad ist auf geringe Verzögerung ausgelegt.

In Richtung **APRS-IS -> RF** steht ein kontrolliertes TX-iGate zur Verfügung. Besonders wichtig sind hier APRS-Nachrichten und die Information, ob die Zielstation lokal gehört wurde.

APRS-IS -> RF ist keine bedingungslose Weiterleitung des gesamten Internetverkehrs. Regeln und Filter schützen den lokalen Funkkanal vor unnötiger Belastung.

## Local TX und Internal TX

Von APRSBox erzeugte Frames werden als logische Quelle **Local TX** behandelt.

Dazu gehören unter anderem:

- Beacon,
- Status,
- WX,
- Objekte,
- Items,
- Bulletins,
- Nachrichten.

Local TX kann unabhängig von einer physischen RF-Aussendung zu APRS-IS geroutet werden.

Zusätzlich gibt es ein logisches **Internal TX**. Es führt keine physische Aussendung durch, ermöglicht aber die Einspeisung eines lokal erzeugten Frames in das weitere Routing.

## Eigene Station, Beacon und Proportional Pathing

APRSBox kann die Position der eigenen Station erzeugen.

Konfigurierbar sind unter anderem:

- Rufzeichen und SSID,
- Position,
- APRS-Symbol,
- Kommentar,
- Pfad,
- Beacon-Intervall,
- Ziel-TX-Interface.

Ein Beacon kann auch manuell gesendet werden.

**Proportional Pathing** ermöglicht häufigere direkte Aussendungen und selteneren Einsatz breiterer DIGI-Pfade, zum Beispiel:

```text
DIRECT -> kurzer Pfad -> vollständiger Pfad
```

Dadurch lässt sich unnötige Kanalbelastung reduzieren.

## APRS Status

APRS Status kann periodisch und unabhängig vom Positions-Beacon gesendet werden.

So lassen sich zusätzliche Stationsinformationen veröffentlichen, ohne sie in jeden Positionsframe aufzunehmen.

## APRS-Nachrichten

APRSBox bietet eine Oberfläche für APRS-Konversationen.

Unterstützt werden unter anderem:

- nummerierte Nachrichten,
- unnummerierte Nachrichten,
- ACK,
- REJ,
- Wiederholungen für Nachrichten, die auf ACK warten,
- Duplikatunterdrückung,
- Nachrichten über RF und APRS-IS,
- Gespräche mit einzelnen Stationen,
- Nachrichtengruppen.

Auch alphanumerische APRS-Nachrichtenkennungen werden unterstützt.

Eine erneut empfangene nummerierte Nachricht muss keinen weiteren Eintrag im Gespräch erzeugen, kann aber erneut mit ACK bestätigt werden.

## Gruppennachrichten und APRS Queries

Es können APRS-Gruppen definiert werden, auf die die Anwendung hört, zum Beispiel:

- `ALL`,
- `QST`,
- `CQ`.

Gruppen für RF und APRS-IS können unabhängig konfiguriert werden.

APRSBox kann außerdem auf ausgewählte standardisierte APRS-Queries reagieren, darunter:

- `?APRS`,
- `?APRSD`,
- `?DX`.

Damit können grundlegende Informationen über die Station und lokale Aktivität direkt über APRS bereitgestellt werden.

## Objekte, Items, Bulletins und Ankündigungen

APRSBox kann APRS-Objekte und Items verwalten.

Möglich sind unter anderem:

- Position und Symbol festlegen,
- Kommentar setzen,
- manuelle Aussendung,
- periodische Aussendung,
- Gültigkeitsdauer definieren,
- Aussendung nach Ablauf beenden,
- Information über das Entfernen eines Objekts senden.

Zusätzlich steht ein Scheduler für Bulletins und Ankündigungen zur Verfügung. Aussendungen können zeitlich verteilt werden, damit mehrere aktive Elemente nicht gleichzeitig einen starken Burst auf dem Funkkanal erzeugen.

## Wetter

APRSBox kann APRS-Wetterdaten erzeugen und verarbeiten.

WX-Daten werden dekodiert und in Stationsansichten, auf der Karte und in Filtern verwendet.

Der Parser unterstützt klassische Wetterfelder sowie zusätzliche in APRS verwendete Erweiterungen.

## Traffic Monitor

Der Traffic Monitor zeigt den durch APRSBox laufenden Verkehr in Echtzeit.

Er kann unter anderem unterscheiden zwischen:

- RX,
- TX,
- Verkehr verschiedener Interfaces,
- RF -> APRS-IS,
- APRS-IS -> RF,
- lokal erzeugten Frames.

Filter nach Richtung, Interface und Frame-Inhalt machen diese Ansicht zu einem praktischen Analysator des lokalen APRS-Verkehrs.

## Stationsliste

Empfangene Frames werden zum aktuellen Zustand einer Station verarbeitet.

Die Ansicht kann unter anderem enthalten:

- Rufzeichen,
- letzte Aktivität,
- Position,
- Entfernung,
- Symbol,
- Frame-Quelle,
- WX-Daten,
- Geschwindigkeit und Kurs,
- Mic-E-Informationen,
- erkanntes Gerät oder erkannte Software.

Stationen können nach Typ und Quelle gefiltert werden.

## Karte

APRSBox besitzt eine umfangreiche Karte auf Basis von Leaflet.

Dargestellt werden können:

- Stationen,
- Objekte und Items,
- Spuren mobiler Stationen,
- PHG-Abdeckung,
- Maidenhead-Gitter,
- NWS-WARN-Gebiete,
- CAWF-Warngebiete,
- PL-WARN,
- ES-WARN.

Die Ansicht kann nach Quellinterfaces gefiltert werden.

## PHG, Spuren und überlappende Stationen

Wenn eine Station PHG-Daten sendet, kann APRSBox einen angenäherten Funkabdeckungsbereich darstellen.

Für mobile Stationen können Bewegungsspuren angezeigt werden.

Wenn mehrere Stationen an derselben oder nahezu derselben Position liegen, kann APRSBox Marker gruppieren und bei passender Zoomstufe überlappende Symbole auseinanderziehen, damit jede Station erreichbar bleibt.

## Maidenhead-Gitter

Die Karte kann ein Maidenhead-Locator-Gitter anzeigen.

Der Detailgrad ändert sich mit der Zoomstufe, sodass die Ebene sowohl zur allgemeinen Orientierung als auch für genauere Arbeit geeignet ist.

## APRS Emergency

APRSBox erkennt Frames im Zusammenhang mit **APRS Emergency** und zeigt sie in einem eigenen Bereich der Oberfläche an.

Das System kann:

- Alarme nach vollständigem Quellrufzeichen gruppieren,
- die Historie zugehöriger Frames speichern,
- die Anzahl weiterer Ereignisse anzeigen,
- globale Benachrichtigungen darstellen,
- ein akustisches Signal wiedergeben,
- einen bestimmten Alarm zeitweise oder dauerhaft stummschalten.

Das Entfernen eines Alarms aus der Liste muss die ursprünglichen Frames im Traffic Monitor nicht löschen, sodass das Ereignis weiterhin analysiert werden kann.

## NWS-WARN

APRSBox unterstützt **NWS-WARN**, den im US-amerikanischen APRS-Umfeld verwendeten Mechanismus für gebietsbezogene Warnungen des National Weather Service.

Eine Warnung kann Gefahrenart, Gültigkeitszeitraum und Informationen über das betroffene Gebiet enthalten.

APRSBox kann diese Daten verwenden, um die Warnung direkt auf der Karte zu visualisieren, statt nur den Meldungstext anzuzeigen.

Die NWS-WARN-Unterstützung gewährleistet Kompatibilität mit dem bestehenden APRS-Ökosystem und Anwendungen, die diesen Mechanismus seit vielen Jahren verwenden.

## CAWF, Common APRS Warning Format

APRSBox unterstützt außerdem **CAWF, Common APRS Warning Format**.

CAWF ist für die Verteilung gebietsbezogener Warnungen über APRS ausgelegt, unabhängig vom jeweiligen nationalen Quellsystem.

Das Format kann standardisiert unter anderem folgende Informationen übertragen:

- Art der Gefahr,
- Warnstufe,
- betroffenes Gebiet,
- Gültigkeitszeitraum,
- Warnungskennung,
- Datenquelle.

Daten nationaler Warnsysteme können von einem warnHUB-Server normalisiert, über APRS übertragen und von einem CAWF-kompatiblen Client einheitlich interpretiert werden.

## PL-WARN

In Polen wird CAWF von **PL-WARN** verwendet.

warnHUB ruft offizielle Warnungen ab, normalisiert sie in CAWF und leitet sie in das APRS-Netz weiter.

APRSBox enthält implementierte Verwaltungsgebiete Polens und kann eine Gebietskennung mit der passenden Geometrie auf der Karte verknüpfen.

Dadurch kann eine PL-WARN-Meldung als hervorgehobenes geografisches Gebiet dargestellt werden und nicht nur als Nachrichtentext.

## ES-WARN

Dasselbe Verfahren wird für Spanien über **ES-WARN** unterstützt.

APRSBox enthält die für die Darstellung von CAWF-Warnungen erforderlichen Gebiete Spaniens.

PL-WARN und ES-WARN verwenden dasselbe CAWF-Modell, sodass Warnungen aus verschiedenen Ländern einheitlich dargestellt werden können.

## Warngebiete auf der Karte

NWS-WARN- und CAWF-Warnungen können als Kartenebenen dargestellt werden.

In einer Ansicht kann der Benutzer daher gleichzeitig sehen:

- APRS-Stationen,
- Objekte,
- Spuren mobiler Stationen,
- lokale Aktivität,
- von Warnungen betroffene Gebiete.

Für CAWF enthält APRSBox derzeit implementierte Gebiete für Polen und Spanien, die entsprechend von PL-WARN und ES-WARN verwendet werden.

Damit kann APRSBox als lokales Warninformationsterminal dienen, das Daten über APRS-IS oder andere konfigurierte Quellen empfängt.

## Stationsradar, Benachrichtigungen und Webhooks

APRSBox besitzt eine Stationsradar-Funktion.

Ausgewählte Rufzeichen, auch mit Masken, können überwacht werden. Das System kann reagieren, wenn sie innerhalb eines festgelegten Bereichs erscheinen.

Ausgewählte Ereignisse können außerdem über Benachrichtigungs- und Integrationsmechanismen außerhalb der GUI weitergegeben werden, darunter Webhooks und Telegram.

## Bandbedingungen

APRSBox bietet eine automatische Analyse der Ausbreitungsbedingungen anhand des lokalen APRS-Verkehrs.

Das System baut eine lokale Referenz auf, die beschreibt, welche Stationen und Entfernungen von einem Standort normalerweise gehört werden. Das ungewöhnliche Auftreten weiter entfernter Stationen oder neuer geografischer Gebiete kann auf verbesserte Ausbreitungsbedingungen hinweisen.

Die Analyse kann für einzelne Interfaces unabhängig erfolgen.

## W0-W5-Skala und Ausbreitungshistorie

Die Bedingungen werden auf einer vereinfachten **W0-W5**-Skala dargestellt.

Die Bewertung kann berücksichtigen:

- Anzahl gehörter Stationen,
- typische lokale Reichweite,
- Entfernungen,
- Wiederholbarkeit von Fernempfang,
- Auftreten neuer Gebiete,
- Reife der gesammelten Daten.

Das Modell basiert nicht ausschließlich auf einer einzigen am weitesten entfernten Station.

APRSBox speichert außerdem den Verlauf der Ausbreitungsbewertungen und Diagnosedaten, die helfen, die aktuelle Bewertung nachzuvollziehen.

## Statistiken und TOP-Listen

APRSBox führt Verkehrs- und Aktivitätsstatistiken.

Verfügbar sind unter anderem Daten zu:

- APRS-Frame-Typen,
- direktem und gesamtem Verkehr,
- Routing-Richtungen,
- Funkaktivität,
- aktivsten Benutzern,
- erkannten Geräten und Anwendungen.

Das System kann TOP-Benutzer nach `CALLSIGN-SSID` und TOP-Geräte darstellen, die unter anderem anhand von TOCALL und Mic-E erkannt wurden.

## Dashboard und Diagnose

Das Dashboard zeigt den aktuellen Zustand der gesamten Installation.

Angezeigt werden können:

- RF-Aktivität,
- Verkehr,
- Dienststatus,
- Interfaces,
- APRS-IS,
- konfigurierte Routing-Richtungen,
- eigene Station.

Packet Routing bietet eine schrittweise Diagnose. Es lässt sich prüfen, welche Regel ausgelöst wurde, welche Filter ausgeführt wurden, wo ein Paket verworfen wurde, ob es TX erreichte und welche Ausführungs-, Queue- und Worker-Zeiten auftraten.

## Mehrere Interfaces, Queues und Pacing

APRSBox geht nicht davon aus, dass eine Station nur ein Modem besitzt.

Eine Installation kann mehrere KISS-Interfaces verwenden, während Packet Routing festlegt, welcher Empfänger Verkehr an welchen Sender weitergeben darf.

TX-Queues sind pro Interface getrennt, sodass ein langsamer oder problematischer TNC die übrigen nicht blockieren muss.

APRSBox steuert außerdem die zeitlichen Abstände lokal erzeugter Frames. Beacon, Status, WX, Objekte, Bulletins und Nachrichten können über die Zeit verteilt werden, statt als ein einzelner Burst gesendet zu werden.

## APRS-Parser

APRSBox besitzt eine eigene APRS-Dekodierschicht, die von Karte, Stationsansichten, Messaging, Wetter und Statistiken verwendet wird.

Unterstützt werden unter anderem:

- klassische APRS-Positionen,
- komprimierte Positionen,
- Mic-E,
- Position Ambiguity,
- Objekte,
- Items,
- Nachrichten,
- ACK und REJ,
- Query,
- Status,
- Telemetrie,
- Wetter,
- PHG.

## Symbole und Weboberfläche

Die Oberfläche unterstützt Symbole aus der primären und alternativen APRS-Symboltabelle zusammen mit ihren Beschreibungen.

Die GUI bietet unter anderem:

- helles und dunkles Thema,
- responsive Navigation,
- kontextbezogene Hilfe,
- Diagnoseansichten,
- dynamisch aktualisierten Verkehr und Stationszustand.

Die Oberfläche ist unter anderem ins Polnische, Englische, Spanische und Deutsche übersetzt.

## Backup, Update, SQLite und HTTPS

Die APRSBox-Konfiguration kann exportiert und wiederhergestellt werden.

Native Installationen bieten einen Update-Mechanismus über die GUI, und vor einem Update kann eine Datenbanksicherung erstellt werden.

Die SQLite-Diagnose umfasst Informationen wie Datenbankgröße, WAL, Seitenzahlen und Integritätsprüfungen.

Runtime-Daten können gelöscht werden, ohne die gesamte Stationskonfiguration zu entfernen.

Eine native Installation kann außerdem mit eigenem Zertifikat und privatem Schlüssel für HTTPS konfiguriert werden.

## Installation und Docker

Das Projekt stellt Installationsskripte bereit für:

- Debian,
- Raspberry Pi OS und andere Debian-kompatible Systeme,
- Alpine Linux.

Sowohl `systemd` als auch `OpenRC` werden unterstützt.

APRSBox kann auch in Docker betrieben werden. Daten und Logs können in Volumes gespeichert werden. Im Container sind einige direkte Host-Verwaltungsfunktionen deaktiviert, und Updates erfolgen durch Austausch des Images unter Beibehaltung der Daten.

## Raspberry Pi und leistungsschwächere Hardware

Das Projekt wird mit Blick auf kleine Rechner und Dauerbetrieb entwickelt.

Im Code werden unter anderem folgende Mechanismen eingesetzt:

- Bounded Queues,
- separate TX-Worker,
- Konfigurations-Cache,
- Verlagerung schwerer Operationen aus dem kritischen RX-Pfad,
- Aggregation von Statistiken,
- Begrenzung mehrfacher Verarbeitung derselben Daten.

Dadurch kann APRSBox auch auf deutlich schwächerer Hardware als einem typischen modernen PC betrieben werden.

## Wichtigste Funktionen

Zu den charakteristischen Funktionen von APRSBox gehören:

- Packet Routing,
- Unterstützung mehrerer TNCs,
- fortgeschrittene DIGI-Filter,
- Duplicate Filter und Viscous Delay,
- RX-iGate und kontrolliertes TX-iGate,
- APRS-Messaging,
- Proportional Pathing,
- Karte mit PHG, Spuren und Maidenhead-Gitter,
- NWS-WARN,
- CAWF,
- PL-WARN mit Gebieten Polens,
- ES-WARN mit Gebieten Spaniens,
- APRS Emergency,
- Stationsradar,
- Webhooks und Benachrichtigungen,
- lokale Ausbreitungsanalyse,
- Geräte- und Benutzerstatistiken,
- umfangreiche Stationsdiagnose.

Das Projekt wird aktiv weiterentwickelt, daher kann sich der Funktionsumfang schneller ändern als Beschreibungen in externer Dokumentation.

## Grundinformationen

**Name:** APRSBox  
**Autor / Hauptmaintainer:** SQ9MDD  
**Lizenz:** GPL-3.0  
**System:** Linux  
**Oberfläche:** Web  
**Datenbank:** SQLite  
**Funkschnittstelle:** KISS TCP / KISS Serial  
**Zusätzliche RX-Quelle:** OpenWebRX MQTT  
**APRS-IS:** RX/TX  
**Installation:** Debian, Raspberry Pi OS, Alpine Linux, Docker  
**Repository:** https://github.com/SQ9MDD/APRSBox
