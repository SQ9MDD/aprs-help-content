---
title: Wie funktioniert APRS?
description: Überblick über den Weg von Informationen in APRS, von der Funkübertragung über Digipeater und IGates bis zu APRS-IS und Anwendungen.
template: doc
tableOfContents: true
---

APRS ist ein verteiltes System.

Es gibt keinen einzelnen zentralen Punkt, durch den jede Information laufen muss. Ein Paket kann direkt von einer anderen Station empfangen, von einem Digipeater wiederholt, von einem IGate an APRS-IS weitergeleitet oder gleichzeitig von mehreren Teilen der Infrastruktur empfangen werden.

Deshalb sollte APRS nicht als einzelne lineare Strecke verstanden werden, sondern als **Funknetz, in dem eine Aussendung von vielen Empfängern genutzt werden kann**.

Der einfachste Paketweg kann so aussehen:

```text
Station A
   |
   | RF
   v
Station B
```

In einem größeren Netz kann dasselbe Paket jedoch gleichzeitig mehrere Empfänger erreichen:

```text
                  -> Station B
                 /
Station A -------+-> Digipeater
                 \
                  -> IGate
```

Jedes dieser Elemente hat eine andere Aufgabe.

## Der einfachste Fall: Station zu Station

Für den Betrieb von APRS sind weder Internet noch Server oder Digipeater erforderlich.

Wenn zwei Stationen sich in direkter Funkreichweite befinden, kann eine von ihnen ein APRS-Paket aussenden und die andere es direkt empfangen.

```text
Station A
   |
   | RF
   v
Station B
```

Station A kann zum Beispiel Folgendes übertragen:

- ihre Position,
- Status,
- überwachte Frequenz,
- eine Nachricht,
- Telemetriedaten,
- Wetterinformationen.

Wenn Station B diesen Informationstyp empfangen und interpretieren kann, kann sie ihn sofort nutzen.

In diesem Stadium muss das Paket nirgendwohin weitergeleitet werden.

Das ist bereits ein korrekt funktionierender APRS-Austausch.

## Was wird eigentlich übertragen?

Ein typisches APRS-Paket auf der Funkschicht wird in einem **AX.25-UI-Frame** übertragen, wobei UI für *Unnumbered Information* steht.

Vereinfacht enthält dieser unter anderem:

- das Rufzeichen der Quellstation,
- das Destination-Feld,
- den Pfad,
- das APRS-Informationsfeld.

In Textdarstellung kann ein solches Paket zum Beispiel so aussehen:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

In diesem Stadium ist jedoch nicht der genaue Aufbau des Pakets entscheidend, sondern was nach der Aussendung mit ihm geschieht.

Der detaillierte Aufbau von AX.25-Frames und APRS-Daten wird in späteren Artikeln beschrieben.

## Eine Aussendung kann viele Empfänger haben

Eine Funkübertragung ist nicht auf dieselbe Weise an ein einzelnes physisches Gerät gerichtet wie eine klassische Punkt-zu-Punkt-Verbindung.

Wenn sich mehrere Stationen in Reichweite des Senders befinden, können alle dasselbe Paket empfangen.

Zum Beispiel:

```text
                -> Station B
               /
Station A -----+-> Digipeater
               \
                -> IGate
```

Dieselbe einzelne Aussendung von Station A kann also empfangen werden von:

- einer anderen Benutzerstation,
- einem Digipeater,
- einem IGate,
- mehreren dieser Geräte gleichzeitig.

Das bedeutet nicht, dass das Paket anschließend durch jedes dieser Elemente laufen muss.

Jeder Empfänger kann seine eigene Aufgabe unabhängig von den anderen erfüllen.

## Die Rolle des Digipeaters

Ein **Digipeater** ist eine Station, die ein APRS-Paket empfangen und erneut über Funk aussenden kann.

Seine Hauptaufgabe besteht darin, die Funkreichweite der Information zu vergrößern.

Beispiel:

```text
Station A
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Station B
```

Station B kann sich außerhalb der direkten Reichweite von Station A befinden und deren Paket dank der Wiederholung trotzdem empfangen.

Ein Digipeater wiederholt jedoch nicht automatisch jeden empfangenen Frame.

Die Entscheidung über eine Wiederholung hängt unter anderem ab von:

- dem Paketpfad,
- der Konfiguration des Digipeaters,
- Mechanismen zur Duplikatunterdrückung,
- der lokalen Netzpolitik.

Moderne APRS-Netze verwenden meistens Pfadmechanismen, die unter anderem auf `WIDE1-1` und `WIDE2-n` basieren.

Die detaillierten Regeln für Pfade und Digipeater werden separat beschrieben.

## Ein Digipeater ist kein IGate

Digipeater und IGate sind zwei unterschiedliche Funktionen.

Ein Digipeater arbeitet hauptsächlich auf der Funkseite:

```text
RF -> RF
```

Er empfängt ein Funkpaket und sendet es unter bestimmten Bedingungen erneut über Funk aus.

Ein IGate verbindet dagegen das Funknetz mit APRS-IS:

```text
RF -> Internet
```

Ein Gerät kann beide Rollen gleichzeitig erfüllen, muss es aber nicht.

Eine Station kann daher sein:

- nur Digipeater,
- nur IGate,
- gleichzeitig Digipeater und IGate.

Diese Unterscheidung ist wichtig, um die gesamte APRS-Infrastruktur zu verstehen.

## Die Rolle des IGate

Ein **IGate**, also Internet Gateway, empfängt Pakete vom lokalen Funkkanal und kann sie an das APRS-IS-Netz weiterleiten.

Beispiel:

```text
Station A
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

Wenn ein IGate ein Paket von Station A empfängt, kann es dieses an APRS-IS senden.

Von diesem Zeitpunkt an kann die Information verfügbar sein für:

- APRS-Clients,
- Kartendienste,
- Datenbanken,
- Monitoring-Anwendungen,
- andere Systeme, die APRS-IS nutzen.

Ein IGate ist nicht erforderlich, damit APRS lokal funktioniert.

Seine Hauptaufgabe besteht darin, das lokale Funknetz mit der Internetinfrastruktur zu verbinden.

## Die Rolle von APRS-IS

**APRS-IS, APRS Internet System**, ist eine globale Internetinfrastruktur zur Verteilung von APRS-Daten.

Unter anderem gelangen Pakete zu APRS-IS, die von IGates weitergeleitet wurden.

Ein vereinfachtes Schema kann so aussehen:

```text
RF-Netz
   |
   v
IGate
   |
   v
APRS-IS
   |
   +-> APRS-Anwendungen
   |
   +-> Kartendienste
   |
   +-> Monitoring-Systeme
   |
   +-> andere Dienste
```

APRS-IS ermöglicht Anwendungen, Daten aus vielen verschiedenen geografischen Gebieten zu empfangen.

Das bedeutet jedoch nicht, dass APRS-IS das Funknetz ersetzt.

Es erweitert dieses.

## Ein typischer Paketweg

Betrachten wir ein einfaches Beispiel.

Die Station:

```text
SQ9MDD-7
```

sendet ein APRS-Paket über Funk aus.

Das Paket kann von einer lokalen Station empfangen werden:

```text
SQ9MDD-7
   |
   | RF
   v
Station B
```

In diesem Moment ist die Information bereits nutzbar.

Gleichzeitig kann dasselbe Paket von einem Digipeater empfangen werden:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
```

Wenn Pfad und Konfiguration es erlauben, wiederholt der Digipeater das Paket:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Station C
```

Das Paket kann außerdem von einem IGate empfangen werden:

```text
SQ9MDD-7
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

Anschließend können die Daten von einer Anwendung abgerufen werden:

```text
SQ9MDD-7
   |
   v
IGate
   |
   v
APRS-IS
   |
   v
APRS-Anwendung
```

Es kann sich dabei weiterhin um dasselbe ursprüngliche Paket handeln.

## Ein Paket muss nicht alle Stufen durchlaufen

Es ist sehr wichtig, diesen Weg nicht als zwingende Kette zu verstehen:

```text
Station -> Digipeater -> IGate -> APRS-IS
```

Das ist nur eine mögliche Route.

Ein Paket kann direkt empfangen werden:

```text
Station A -> Station B
```

Es kann von einem IGate ohne Digipeater empfangen werden:

```text
Station A -> IGate -> APRS-IS
```

Es kann vollständig im Funknetz bleiben:

```text
Station A -> Digipeater -> Station B
```

Es kann außerdem gleichzeitig mehrere Empfänger erreichen:

```text
                     -> Station B
                    /
Station A -> Digipeater -> Station C
       \            \
        \            -> IGate 2
         \
          -> IGate 1
```

APRS hat daher keinen einzigen obligatorischen Paketweg.

## Ein Frame kann über viele Wege ankommen

In einem realen Netz kann derselbe Frame von mehreren Digipeatern und mehreren IGates empfangen werden.

Zum Beispiel:

```text
                    -> IGate 1
                   /
Station A -> DIGI -+-> IGate 2
        \          \
         \          -> Station B
          \
           -> IGate 3
```

Zusätzlich können einige IGates auch die ursprüngliche Aussendung direkt empfangen.

Dadurch kann dieselbe Information über mehr als einen Weg die Infrastruktur erreichen.

Das ist bei APRS normal.

## Duplikate

Da eine einzelne Aussendung von vielen Teilen der Infrastruktur empfangen und weitergeleitet werden kann, muss APRS mit Duplikaten umgehen.

Derselbe Frame kann zum Beispiel:

- direkt von einem IGate empfangen werden,
- nach der Wiederholung durch einen Digipeater erneut empfangen werden,
- von einem zweiten IGate empfangen werden,
- von mehreren Orten an APRS-IS weitergeleitet werden.

Deshalb können Digipeater, IGates und Server Mechanismen einsetzen, um Wiederholungen zu erkennen und zu verwerfen.

Ohne solche Mechanismen könnte sich eine einzelne Aussendung unnötig im Netz vervielfachen.

Die detaillierten Regeln zur Duplikatbehandlung hängen vom jeweiligen Infrastrukturelement ab und werden später in der Dokumentation beschrieben.

## Von RF zu APRS-IS

Die Richtung:

```text
RF -> APRS-IS
```

ist eine der Hauptaufgaben eines IGate.

Lokal über Funk empfangene Pakete können an APRS-IS weitergeleitet werden, wo sie für Internetanwendungen verfügbar werden.

Das bedeutet jedoch nicht, dass jeder empfangene Frame weitergeleitet werden muss.

Ein IGate kann abhängig von Konfiguration und Verkehrstyp bestimmte Regeln und Filter anwenden.

## Von APRS-IS zu RF

Die Gegenrichtung:

```text
APRS-IS -> RF
```

erfordert deutlich mehr Vorsicht.

Der Funkkanal hat nur begrenzte Kapazität, daher kann nicht einfach der gesamte APRS-IS-Verkehr auf RF übertragen werden.

Nur ausgewählte Informationen dürfen entsprechend den Betriebsregeln eines IGate auf Funk weitergeleitet werden.

Ein typisches Beispiel ist eine Nachricht an eine lokale Station, die kürzlich von diesem IGate gehört wurde.

Der Weg kann dann so aussehen:

```text
APRS-IS
   |
   v
IGate
   |
   | RF
   v
Lokale Station
```

Verkehr in Richtung Internet -> RF muss kontrolliert werden, weil jedes solche Paket Sendezeit auf dem gemeinsam genutzten Funkkanal belegt.

Die detaillierten Regeln für APRS-IS -> RF Gating werden separat beschrieben.

## Third-party traffic

In bestimmten Situationen muss ein Paket, das aus einem anderen Teil des Systems stammt, erneut auf RF gebracht werden.

APRS verfügt dafür über einen speziellen Mechanismus namens **third-party traffic**.

Dabei wird nicht einfach ein Textpaket aus APRS-IS kopiert und unverändert über Funk ausgesendet.

Die ursprüngliche Information wird in eine spezielle Struktur eingebettet, die Informationen über ihre Herkunft erhält.

Dieser Mechanismus ist unter anderem wichtig für die kontrollierte Weiterleitung ausgewählten Verkehrs von APRS-IS zu RF.

Die detaillierte Syntax von third-party traffic wird im Abschnitt über spezielle Formate beschrieben.

## Was funktioniert ohne Internet?

Sehr viel.

Beispiel:

```text
HT
 |
 | RF
 v
Digipeater
 |
 | RF
 v
Mobilfunkgerät
```

Wenn der Operator des Mobilfunkgeräts das Paket der portablen Station empfängt, hat APRS seine Aufgabe erfüllt.

APRS-IS ist nicht erforderlich.

Eine Internetkarte ist nicht erforderlich.

Kein Server ist erforderlich.

**Kein Internet bedeutet nicht kein APRS.**

## Was funktioniert ohne Digipeater?

Wenn sich Stationen in direkter Reichweite befinden, ist kein Digipeater erforderlich.

```text
Station A
   |
   +-----> Station B
   |
   +-----> IGate
```

Station B kann die Information lokal verwenden, während das IGate sie unabhängig davon an APRS-IS weiterleiten kann.

Ein Digipeater ist erst dann erforderlich, wenn die Wiederholung die nutzbare Reichweite des Netzes tatsächlich vergrößert.

## Was funktioniert ohne IGate?

Auch ein vollständiges lokales Funknetz kann funktionieren.

```text
Station A
   |
   v
Digipeater
   |
   v
Station B
```

Stationen können Positionen, Statusmeldungen, Nachrichten und andere Informationen ohne jede Verbindung zu APRS-IS austauschen.

Die Pakete erscheinen dann nicht in Internetdiensten, die lokale APRS-Funktion bleibt aber vollständig nutzbar.

## Direkter Empfang ist wichtig

In der Praxis kann ein direkt empfangenes Paket wertvoller sein als eine über das Internet verfügbare Information.

Wenn eine Station in der Nähe sendet:

```text
SP9XYZ
145.550 MHz
```

kann ein Operator diese Information sofort verwenden, um eine Funkverbindung herzustellen.

Er muss nicht warten, bis das Paket:

- von einem IGate empfangen wird,
- APRS-IS erreicht,
- von einem Internetdienst gespeichert wird,
- von einer Anwendung abgerufen wird.

Der lokale Funkweg ist ein grundlegender Bestandteil von APRS.

## Häufige Missverständnisse

### Jedes Paket muss über einen Digipeater laufen

Nein.

Wenn sich der Empfänger in direkter Reichweite des Senders befindet, kann er das Paket ohne jede Wiederholung empfangen.

### Ein Digipeater leitet Pakete ins Internet weiter

Nicht unbedingt.

Die Hauptfunktion eines Digipeaters ist die Wiederholung von Paketen über Funk.

Für die Verbindung mit APRS-IS ist die IGate-Funktion zuständig.

### Ein IGate muss auch ein Digipeater sein

Nein.

Ein IGate kann ausschließlich RF-Verkehr empfangen und an APRS-IS weiterleiten.

### Wenn ein Paket nicht auf APRS.fi erscheint, hat APRS nicht funktioniert

Nein.

Das Paket kann korrekt empfangen und lokal von anderen Stationen genutzt worden sein.

### APRS-IS sendet den gesamten Verkehr zurück auf Funk

Nein.

Verkehr von APRS-IS zu RF muss begrenzt und kontrolliert werden.

### Ein Paket hat genau eine festgelegte Route

Nein.

Dieselbe Aussendung kann von vielen Stationen empfangen werden und die Infrastruktur über verschiedene Wege erreichen.

## Das Gesamtbild

Ein vereinfachtes Modell der APRS-Funktion kann so dargestellt werden:

```text
                         +-> Lokale Station
                         |
[APRS-Station] -- RF ----+-> [Digipeater] -- RF --> andere Stationen
                         |
                         +-> [IGate]
                               |
                               | Internet
                               v
                            [APRS-IS]
                               |
                 +-------------+-------------+
                 |             |             |
                 v             v             v
             Anwendungen      Karten       Dienste
```

Entscheidend ist jedoch, dass keiner dieser Wege zwingend erforderlich ist.

APRS kann funktionieren als:

```text
Station -> Station
```

```text
Station -> Digipeater -> Station
```

```text
Station -> IGate -> APRS-IS
```

oder über alle diese Wege gleichzeitig.

## Die wichtigsten Punkte

**APRS ist ein verteiltes System.**

Eine Aussendung kann von vielen Stationen gleichzeitig empfangen werden.

**Ein Digipeater vergrößert die Funkreichweite.**

Seine Hauptaufgabe ist die Wiederholung von RF-Paketen.

**Ein IGate verbindet das lokale Funknetz mit APRS-IS.**

Es muss nicht gleichzeitig Digipeater sein.

**APRS-IS erweitert die Reichweite von Informationen über das lokale Funknetz hinaus.**

Es ersetzt jedoch nicht die grundlegende RF-Kommunikation.

**Ein Paket muss das Internet nicht erreichen, um seine Aufgabe zu erfüllen.**

Der lokale Empfang der Information kann sein wichtigstes Ziel sein.

**Ein Frame kann über viele Wege ankommen.**

Duplikate und ihre Unterdrückung sind daher ein natürlicher Teil des Netzbetriebs.

**Verkehr vom Internet zu RF muss kontrolliert werden.**

Der Funkkanal hat begrenzte Kapazität und kann nicht als Kopie von APRS-IS behandelt werden.

## Weiter

Nach dem Verständnis des Informationswegs durch das Netz sollten als Nächstes folgende Elemente des Protokolls betrachtet werden:

- die Beziehung zwischen APRS und AX.25,
- der Aufbau eines AX.25-Frames,
- die Struktur eines APRS-Pakets,
- Quelladressen und SSIDs,
- Destination Address und TOCALL,
- Digipeater-Pfade,
- Unterschiede zwischen RF und APRS-IS,
- Mechanismen für third-party traffic,
- q-constructs,
- detaillierte Betriebsregeln für IGates.

Erst durch die Kombination dieser Elemente entsteht das vollständige Bild davon, wie APRS-Informationen zwischen Stationen und den verschiedenen Teilen der Infrastruktur übertragen werden.
