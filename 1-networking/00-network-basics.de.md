---
title: Rolle und Elemente des APRS-Netzes, Grundlagen der Funktionsweise
description: Überblick über den Aufbau des APRS-Netzes, die Rollen seiner Elemente, den Informationsfluss und die Arbeitsprinzipien vermittelnder Geräte.
template: doc
tableOfContents: true
---

APRS bildet ein verteiltes Netz zum Austausch von Informationen. Daten können direkt über Funk empfangen, durch Digipeater weitergesendet und zwischen dem Funkteil des Netzes und APRS-IS übertragen werden.

Das Netz benötigt kein zentrales vermittelndes Element. Zwei Stationen innerhalb ihrer direkten Funkreichweite können Informationen unmittelbar austauschen. Digipeater erweitern die nutzbare Reichweite des Funknetzes, während iGates RF-Segmente mit APRS-IS verbinden.

Die verschiedenen Netzelemente erfüllen unterschiedliche Funktionen. Ein einzelnes Gerät kann mehrere davon gleichzeitig übernehmen, dennoch hat jede Rolle ihren eigenen Funktionsumfang.

## Elemente des APRS-Netzes

Die grundlegenden Rollen in einem APRS-Netz sind:

- Endstation,
- Digipeater,
- iGate,
- APRS-IS,
- Anwendungen und Dienste, die APRS-Daten nutzen.

Diese Rollen beschreiben eine im Netz ausgeführte Funktion und keinen bestimmten Gerätetyp.

Eine einzelne Installation kann beispielsweise eigene APRS-Pakete erzeugen, Verkehr als Digipeater weiterleiten und empfangene Pakete als iGate an APRS-IS übergeben. Diese Funktionen bleiben dennoch voneinander getrennt.

## Endstation

Eine Endstation ist Quelle oder Empfänger von APRS-Informationen.

Sie kann unter anderem Positions-, Status-, Wetter-, Telemetrie-, Objekt- oder Nachrichteninformationen erzeugen. Ebenso kann sie Daten empfangen und auswerten, die von anderen Stationen erzeugt wurden.

Die Kommunikation muss nicht über vermittelnde Infrastruktur laufen. Befinden sich zwei Stationen in direkter Funkreichweite zueinander, kann ein Paket ohne Digipeater, iGate oder APRS-IS empfangen werden.

## Digipeater

Ein Digipeater ist ein Element des Funkteils des APRS-Netzes. Seine Aufgabe besteht darin, Pakete entsprechend dem APRS-Pfadmechanismus weiterzusenden.

Dadurch kann ein Paket über die direkte Funkreichweite der Quellstation hinaus gelangen.

Bei der Weiterleitung führt der Digipeater die für den Paketpfad vorgesehenen Änderungen aus. Die Weiterleitung darf nicht dazu verwendet werden, die übrigen Paketdaten beliebig zu verändern. Quelldaten bleiben Daten der Station, die das Paket erzeugt hat.

Erzeugt ein als Digipeater arbeitendes Gerät einen eigenen Beacon, Status oder ein anderes APRS-Paket, handelt es sich um ein neues Paket dieser Station und nicht um eine Ergänzung des Inhalts eines Pakets einer anderen Station.

Die Einzelheiten der Pfadverarbeitung und der Digipeating-Algorithmen werden separat beschrieben.

## iGate

Ein iGate ist ein Gateway zwischen dem Funkteil des APRS-Netzes und APRS-IS.

Die grundlegende Arbeitsrichtung ist:

```text
RF -> APRS-IS
```

Ein iGate kann außerdem ausgewählten Verkehr kontrolliert in der Richtung

```text
APRS-IS -> RF
```

weiterleiten.

Für beide Richtungen gelten unterschiedliche Regeln.

Wie ein Digipeater darf auch ein iGate die Daten eines weitergeleiteten Pakets nicht beliebig verändern. Informationen über die Einspeisung eines Pakets in APRS-IS werden in dem dafür vorgesehenen Teil des Pfades abgelegt, unter anderem mithilfe eines `q` construct. Sie werden nicht an die Quelldaten der Station angehängt.

Die detaillierten Regeln für Gating, `q` constructs und APRS-IS -> RF-Verkehr werden separat beschrieben.

## APRS-IS

APRS-IS ist der Internet-Teil der APRS-Infrastruktur. Es verbindet Server, Clients, Anwendungen und iGates und ermöglicht dadurch die Verteilung von Paketen über die lokale Funkabdeckung hinaus.

Ein lokal über RF empfangenes Paket kann durch ein iGate in APRS-IS eingespeist und Systemen an anderen Standorten zur Verfügung gestellt werden.

APRS-IS ist kein vom Funkteil des APRS unabhängiges System. Seine Funktion ist mit dem RF-Verkehr verknüpft, und aus APRS-IS stammende Daten können in definierten Fällen wieder über Funk ausgesendet werden.

## Anwendungen und Dienste

APRS-Daten können von Anwendungen und spezialisierten Diensten empfangen und genutzt werden.

Sie können unter anderem:

- den aktuellen Zustand des Netzes darstellen,
- Positionen und Objekte anzeigen,
- Nachrichten verarbeiten,
- Daten aufzeichnen,
- Wetter- und Telemetriedaten verarbeiten,
- weitere auf APRS-Daten basierende Funktionen bereitstellen.

Nicht jede Anwendung, die APRS-IS nutzt, ist Teil der Infrastruktur, die Pakete transportiert. Ein System, das Daten nutzt, ist von einem Digipeater, iGate oder APRS-IS-Server zu unterscheiden, der unmittelbar an ihrer Verteilung beteiligt ist.

## Informationsfluss

Die einfachste Kommunikation erfolgt direkt:

```text
Station A -> RF -> Station B
```

Mit einem Digipeater:

```text
Station A -> RF -> Digipeater -> RF -> Station B
```

Ein von einem iGate empfangenes Paket kann an APRS-IS weitergegeben werden:

```text
Station A -> RF -> iGate -> APRS-IS
```

Innerhalb von APRS-IS kann dasselbe Paket mehreren Clients und Diensten zur Verfügung stehen:

```text
                         -> Client
                        /
RF -> iGate -> APRS-IS ---> Dienst
                        \
                         -> anderes APRS-System
```

In definierten Fällen kann Verkehr auch von APRS-IS auf RF weitergeleitet werden:

```text
APRS-IS -> iGate -> RF -> Station
```

Nicht jedes Paket durchläuft alle Netzelemente. Dies sind unterschiedliche mögliche Wege der Informationsverteilung.

## Informationsquelle und vermittelnde Elemente

Die Quelle einer Information ist von den Elementen zu unterscheiden, die an ihrer Weiterleitung beteiligt sind.

Ein Paket kann einen Digipeater, ein iGate und APRS-IS durchlaufen, ohne dass diese Elemente dadurch zur Quelle der im Paket enthaltenen Daten werden.

Informationen, die für den Betrieb des Netzes erforderlich sind, beispielsweise Angaben zum verwendeten Funkpfad oder zum Einspeisepunkt in APRS-IS, besitzen eigene Mechanismen und dafür vorgesehene Stellen.

Ein vermittelndes Element führt nur die Änderungen aus, die sich aus seiner jeweiligen Funktion ergeben. Eigene technische Informationen dürfen nicht in die Quelldaten einer anderen Station verschoben werden.

## Weiterleitung und Erzeugung von Paketen

Das Weiterleiten eines vorhandenen Pakets und das Erzeugen eines neuen Pakets sind zwei unterschiedliche Operationen.

```text
Weiterleitung eines vorhandenen Pakets
```

ist nicht dasselbe wie:

```text
Erzeugung eines eigenen Pakets
```

Ein Digipeater, der ein Paket weiterleitet, oder ein iGate, das es an APRS-IS übergibt, arbeitet als vermittelndes Element.

Erzeugt ein Gerät eigene APRS-Informationen, entsteht ein neues Paket mit diesem Gerät als Quelle.

Diese Unterscheidung bleibt auch dann wichtig, wenn mehrere Funktionen durch dieselbe Software, denselben Computer oder dasselbe Funkgerät realisiert werden.

## Rollen und Funktionsgrenzen

Endstation, Digipeater, iGate und APRS-IS erfüllen unterschiedliche Funktionen.

Eine Endstation erzeugt oder empfängt APRS-Informationen.

Ein Digipeater sendet Pakete innerhalb des Funknetzes weiter und führt die für den Weiterleitungspfad vorgesehenen Änderungen aus.

Ein iGate leitet Pakete zwischen RF und APRS-IS entsprechend den für diese Funktion festgelegten Regeln weiter. Informationen über den Transport innerhalb von APRS-IS werden in den dafür vorgesehenen Pfadelementen gespeichert, unter anderem mithilfe von `q` constructs.

APRS-IS verteilt Pakete innerhalb des Internet-Teils des Systems.

Dass ein Gerät technisch mehrere Funktionen erfüllen kann, führt nicht zu einer Zusammenführung ihrer Betriebsregeln. Für jedes Paket ist entscheidend, in welcher Rolle das Gerät dieses Paket verarbeitet.

Insbesondere darf vermittelnde Infrastruktur Daten der Quellstation nicht beliebig verändern. Änderungen während der Weiterleitung eines Pakets sind auf die Felder und Mechanismen beschränkt, die für die ausgeführte Netzfunktion vorgesehen sind.

## Gemeinsam genutzter Funkkanal

Der Funkteil von APRS nutzt ein gemeinsam genutztes Medium mit begrenzter Kapazität.

Jede Aussendung belegt den Kanal und beeinflusst die Sende- und Empfangsmöglichkeiten anderer Stationen. Dies gilt für von Stationen erzeugte Pakete, für Weiterleitungen durch Digipeater und für Verkehr, der durch iGates auf RF eingebracht wird.

Die Konfiguration einzelner Elemente beeinflusst daher nicht nur die Reichweite eines einzelnen Pakets, sondern auch den Betrieb des gesamten lokalen Netzes.

Detaillierte Mechanismen zur Begrenzung des Verkehrs und Grundsätze für die Planung der Infrastruktur werden in späteren Artikeln des Abschnitts **Netz** beschrieben.

## Das APRS-Netz als Ganzes

Ein möglicher Weg eines Pakets kann wie folgt dargestellt werden:

```text
                     +-> Station
                     |
Station -> Digipeater +-> iGate -> APRS-IS -> Anwendungen und Dienste
                     |
                     +-> andere RF-Stationen
```

Dies ist keine vorgeschriebene Topologie.

APRS kann lokal ausschließlich über RF arbeiten, einen oder mehrere Digipeater verwenden, mit APRS-IS verbunden sein oder mehrere dieser Mechanismen gleichzeitig nutzen.

Die grundlegende Aufteilung der Funktionen bleibt:

- eine Station erzeugt oder empfängt Informationen,
- ein Digipeater erweitert ihre Verteilung über RF,
- ein iGate verbindet RF mit APRS-IS,
- APRS-IS verteilt Informationen im Internet-Teil des Netzes,
- Anwendungen und Dienste nutzen die verfügbaren Daten.

Jedes dieser Elemente führt die für seine Rolle vorgesehenen Operationen aus. Die detaillierten Regeln für Digipeater, iGates und APRS-IS werden in den folgenden Teilen des Abschnitts **Netz** beschrieben.

## Quellen

- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
- [APRS-IS](https://www.aprs-is.net/)
- [APRS-IS IGate Details](https://www.aprs-is.net/IGateDetails.aspx)
