---
title: Wie funktioniert APRS?
description: Wie APRS-Pakete über Funk, Digipeater, IGates und APRS-IS verteilt werden.
template: doc
tableOfContents: true
---

APRS ist ein Broadcast-Netz. Eine Station sendet ein Paket über Funk aus; jeder Empfänger in Reichweite kann es unabhängig empfangen und nutzen. Es gibt keinen zentralen Knoten und keine vorgeschriebene Route: Ein Paket kann lokal bleiben, von einem Digipeater erneut ausgesendet werden, über ein IGate das Internet erreichen — oder mehrere dieser Wege gleichzeitig nehmen.

## Von Station zu Station: APRS funktioniert über Funk

Der einfachste Fall benötigt weder Internet noch Infrastruktur. Eine Mobilstation sendet ein Paket aus, das eine Heimstation direkt über RF empfängt.

![Direkter Empfang eines APRS-Pakets über Funk](./_img/diagram1.png)

Das empfangene Paket kann beispielsweise eine Position, einen Status, eine Nachricht, Wetterdaten oder Telemetrie enthalten. Versteht der Empfänger das Format, ist die Information sofort nutzbar. Das ist ein vollständiger, korrekt funktionierender APRS-Austausch.

Über Funk werden APRS-Daten üblicherweise in einem **AX.25-UI**-Frame (*Unnumbered Information*) übertragen. Seine Textdarstellung kann so aussehen:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Quellrufzeichen, Zieladresse, Pfad und Informationsfeld beschreiben den Frame und seinen APRS-Inhalt. Die folgenden Artikel behandeln den Paketaufbau; hier ist entscheidend, was nach der Aussendung passiert.

## Eine Aussendung, viele Empfänger

RF ist ein gemeinsam genutztes Medium. Dieselbe Aussendung kann gleichzeitig von einer Nutzerstation, einem Digipeater und einem IGate empfangen werden.

![Eine APRS-Aussendung wird von einer Station, einem Digipeater und einem IGate empfangen](./_img/diagram2.png)

Der Empfang erzeugt keine Warteschlange und keine Weiterleitungskette. Jeder Empfänger trifft seine eigene Entscheidung: Er zeigt die Daten an, sendet das Paket erneut über Funk aus oder leitet es an APRS-IS weiter. Deshalb sind mehrere Wege derselben Information in APRS normal.

## Digipeater: Reichweite auf RF erweitern

Ein **Digipeater** empfängt ein Funkpaket und sendet es erneut aus, wenn Pfad und Konfiguration dies erlauben. So kann die Information über die direkte Reichweite der Quellstation hinaus gelangen.

![Erneute Aussendung eines APRS-Pakets durch einen Digipeater](./_img/diagram3.png)

Ein Digipeater sollte nicht alles wiederholen. Seine Entscheidung hängt unter anderem von den Adressen im Pfad, der lokalen Betriebsrichtlinie des Netzes und dem Schutz vor Duplikaten ab. Häufig werden Pfade wie `WIDE1-1` und `WIDE2-n` verwendet; ihre Semantik und Konfigurationsregeln werden separat behandelt.

Die Aufgabe eines Digipeaters lässt sich so zusammenfassen:

```text
RF → RF
```

Das bedeutet nicht automatisch Internetzugang.

## IGate und APRS-IS: die Grenze zwischen RF und Internet

Ein **IGate** (*Internet Gateway*) hört lokalen RF-Verkehr und leitet ausgewählte Pakete an **APRS-IS** weiter, das globale Netzwerk von Servern zur Verteilung von APRS-Daten. Dadurch stehen lokal empfangene Pakete Anwendungen, Karten und Überwachungsdiensten zur Verfügung.

![Datenweiterleitung vom RF-Netz über ein IGate zu APRS-IS](./_img/diagram4.png)

Die primäre Richtung eines IGate ist:

```text
RF → APRS-IS
```

Ein IGate kann ohne Digipeating arbeiten, und ein Digipeater ohne IGate. Eine Station kann selbstverständlich beide Aufgaben übernehmen, doch es sind unabhängige Funktionen:

| Element | Aufgabe | Primäre Richtung |
| --- | --- | --- |
| Digipeater | Erweitert die Reichweite des lokalen Funknetzes | `RF → RF` |
| IGate | Verbindet lokales RF mit APRS-IS | `RF → APRS-IS` |
| APRS-IS | Verteilt Pakete über das Internet | Internet |

APRS-IS erweitert die Reichweite von Informationen, ersetzt aber den Funkkanal nicht. Ein Paket, das nicht in einem Internetdienst erscheint, kann dennoch lokal korrekt empfangen und genutzt worden sein.

## Warum derselbe Frame mehrfach erscheint

In einem realen Netz können mehrere IGates die ursprüngliche Aussendung und ihre Wiederholung empfangen. Jedes kann den Frame an APRS-IS weiterleiten.

![Mehrere Empfangswege derselben APRS-Aussendung](./_img/diagram5.png)

Das ist kein Übertragungsfehler, sondern eine Folge des Broadcast-Charakters von RF. Digipeater, IGates und APRS-IS-Server erkennen Duplikate, damit derselbe Frame nicht weiter vervielfacht wird. Die Details hängen von Implementierung und Konfiguration des jeweiligen Knotens ab.

## Vom Internet zurück zum Funk

Die Richtung `APRS-IS → RF` ist bewusst begrenzt. Der Funkkanal hat eine geringe Kapazität und wird von allen Stationen geteilt; ein IGate kann ihn daher nicht als vollständige Kopie von APRS-IS behandeln.

Ein typischer kontrollierter Fall ist eine Nachricht an eine lokale Station, die das IGate kürzlich auf RF gehört hat. Wenn ausgewählter Verkehr von APRS-IS auf Funk übertragen wird, kann der Mechanismus **third-party traffic** verwendet werden, um Informationen über die Herkunft des Pakets zu bewahren. Gating-Regeln, q-constructs und das third-party-traffic-Format benötigen eine eigene Erklärung.

## Das Gesamtbild

Das folgende Diagramm zeigt das Zusammenspiel dieser Rollen. RF-Kommunikation breitet sich lokal aus; IGates übertragen Daten zwischen lokalem Funk und APRS-IS; Anwendungen und Dienste nutzen die im Internet verfügbaren Daten.

![Datenfluss zwischen RF-Stationen, Digipeatern, IGates und APRS-IS](./_img/diagram6.png)

Für ein einzelnes Paket können daher parallel drei Ergebnisse eintreten:

- lokaler Empfang durch andere Stationen,
- größere Reichweite durch Digipeating,
- Veröffentlichung in APRS-IS über ein oder mehrere IGates.

Keines davon ist nötig, damit die anderen eintreten können. Lokales APRS funktioniert ohne Internet, und ein IGate kann ein Paket ohne Digipeater an APRS-IS weiterleiten.

## Die wichtigsten Punkte

- APRS ist keine einzelne Route: `Station → Digipeater → IGate → Internet`.
- Eine RF-Aussendung kann für viele Empfänger nützlich sein und sie über unterschiedliche Wege erreichen.
- Ein Digipeater sendet Funkverkehr erneut aus; ein IGate verbindet RF mit APRS-IS.
- Duplikate sind in einem Broadcast-Netz natürlich und werden von seinen Komponenten gefiltert.
- Das Internet verbessert die Verfügbarkeit der Daten, ist aber keine Voraussetzung für lokales APRS.
- Verkehr aus dem Internet zu RF muss gezielt ausgewählt werden, damit er den gemeinsamen Kanal nicht belastet.

## Weiter

Als Nächstes folgen der Aufbau von AX.25-Frames und APRS-Paketen, Quelladressen und SSIDs, das destination/TOCALL-Feld, Digipeater-Pfade sowie die Unterschiede zwischen RF- und APRS-IS-Verkehr.
