---
title: Was ist APRS?
description: Einführung in APRS als Funksystem zum Austausch kurzer und aktueller Informationen.
template: doc
tableOfContents: true
---

**APRS, Automatic Packet Reporting System**, ist ein digitales Amateurfunksystem zum Austausch von Informationen in Echtzeit. Es wurde dafür entwickelt, dass Benutzer innerhalb eines Gebietes automatisch kurze, aktuelle Informationen austauschen können, die für Funkverbindungen und Aktivitäten im Gelände nützlich sind.

APRS wird häufig vor allem mit der Darstellung von Stationspositionen auf einer Karte verbunden. Das ist jedoch nur eine seiner Funktionen.

Die Position einer Station, eines Fahrzeugs oder eines Objekts ist nur eine Art von Information, die APRS übertragen kann. Das System kann außerdem unter anderem Statusmeldungen, Nachrichten, Informationen über die Betriebsfrequenz, Objekte, Wetterdaten, Telemetrie, Bulletins, Abfragen und Warnungen übertragen.

Die wichtigste Eigenschaft von APRS ist daher nicht die Positionsmeldung selbst, sondern **aktuelle Informationen automatisch anderen Benutzern des Netzes zur Verfügung zu stellen**.

> APRS sollte in erster Linie als Funksystem zum Austausch kurzer, aktueller Informationen verstanden werden, die dabei helfen können, eine Funkverbindung herzustellen, die Situationsübersicht zu verbessern oder eine Handlung zu ermöglichen.

## Mehr als nur Position

Eines der häufigsten Missverständnisse über APRS ist die Gleichsetzung mit einem GPS-Ortungssystem.

Ein Tracker, der seine Position sendet, ist ein typisches APRS-Gerät, nutzt aber nur einen Teil der Möglichkeiten des Protokolls.

Im selben Netz können zum Beispiel folgende Informationen erscheinen:

- Positionen fester und mobiler Stationen,
- Informationen über die aktuell überwachte Frequenz,
- Status des Operators,
- kurze Textnachrichten,
- Gruppennachrichten und Bulletins,
- Relaisfunkstellen und andere Objekte im Gelände,
- Treffpunkte, Veranstaltungen oder Aktivitäten im Gelände,
- Telemetriedaten,
- Daten von Wetterstationen,
- Wetterwarnungen und andere gebietsbezogene Informationen.

Eine Mobilstation kann andere Stationen also nicht nur darüber informieren, **wo sie sich befindet**, sondern auch darüber, welche Frequenz der Operator überwacht oder welches Relais er gerade verwendet.

In der Praxis kann diese Information wesentlich nützlicher sein als nur ein Punkt auf einer Karte.

## Informationen darüber, was gerade geschieht

APRS wurde für Informationen entwickelt, die **jetzt** relevant sind.

Im Gegensatz zu klassischen Packet-Radio-Systemen, die auf den Aufbau einer Verbindung zwischen zwei Stationen und die Übertragung größerer Datenmengen ausgerichtet sind, basiert typischer APRS-Verkehr auf kurzen Broadcast-Aussendungen.

Eine Station sendet Informationen aus, die gleichzeitig von allen Stationen innerhalb der Funkreichweite empfangen werden können.

Dadurch eignet sich APRS sehr gut zum Aufbau eines lokalen Situationsbildes.

Ein Benutzer kann zum Beispiel erfahren:

- wer sich in seiner Nähe befindet,
- wo sich andere Stationen befinden,
- welche Stationen mobil sind,
- auf welchen Frequenzen Operatoren arbeiten,
- welche Relais sich in der Nähe befinden,
- ob in der Umgebung eine Amateurfunkveranstaltung oder Aktivität stattfindet,
- welche Wetterbedingungen lokale Stationen melden,
- ob eine Warnung ausgegeben wurde,
- welche Objekte von anderen Netzbenutzern veröffentlicht wurden.

Das bedeutet natürlich nicht, dass jeder APRS-Empfänger alle diese Datentypen darstellen muss. Der Umfang der angezeigten Informationen hängt von den Möglichkeiten des verwendeten Geräts oder der Software ab.

## APRS ist ein Funksystem

APRS kann vollständig ohne Internetzugang funktionieren.

Der einfachste Informationsaustausch kann so aussehen:

```text
Station A
   |
   | RF
   v
Station B
```

Station B empfängt die Aussendung von Station A direkt und kann die darin enthaltenen Informationen verwenden.

Wenn die direkte Funkreichweite nicht ausreicht, können im Netz **Digipeater** eingesetzt werden, die ausgewählte Pakete erneut aussenden:

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

Ein Internetzugang ist daher für den Betrieb von APRS nicht erforderlich.

Das ist eine wichtige Eigenschaft des Systems. Informationen können lokal per Funk ausgetauscht werden, auch wenn keine der Stationen Zugang zu einer Internetinfrastruktur hat.

## APRS-IS erweitert das Funknetz

Modernes APRS ist häufig mit dem internetbasierten Netz **APRS-IS** verbunden.

Diese Verbindung wird durch Stationen hergestellt, die als **IGate**, also Internet Gateway, bezeichnet werden.

Ein typischer Paketweg kann so aussehen:

```text
Station
  |
  | RF
  v
Digipeater
  |
  | RF
  v
IGate
  |
  | Internet
  v
APRS-IS
  |
  v
APRS-Anwendungen und -Dienste
```

Ein IGate kann über Funk empfangene Informationen an APRS-IS weiterleiten. Dadurch können die Daten auch für Anwendungen außerhalb der lokalen Funkreichweite verfügbar werden.

Auf diese Weise können Pakete, die von der lokalen Infrastruktur empfangen wurden, später in Internetdiensten für APRS erscheinen.

Die Begriffe sollten jedoch in der richtigen Reihenfolge verstanden werden:

**APRS-IS ist ein Bestandteil der APRS-Infrastruktur, aber nicht APRS selbst.**

Der grundlegende Informationsaustausch kann weiterhin direkt per Funk stattfinden.

## APRS ist nicht APRS.fi

Internetdienste wie APRS.fi sind eine sehr nützliche Möglichkeit, Daten aus dem APRS-Netz darzustellen, sie sind aber nicht das Netz selbst.

Die dort sichtbare Karte ist lediglich eine Visualisierung eines Teils der Informationen, die an APRS-IS weitergeleitet wurden.

Ein Paket kann seine Aufgabe im Funknetz vollständig erfüllen und niemals das Internet erreichen.

Zum Beispiel kann eine Mobilstation ihre Position zusammen mit der Information über die überwachte Frequenz aussenden. Ein einige Kilometer entfernter Operator kann dieses Paket empfangen und die Information nutzen, um eine Sprachverbindung herzustellen.

Wenn in der Umgebung kein IGate arbeitet, erscheint das Paket nicht in einem Internetdienst.

Das bedeutet nicht, dass APRS nicht funktioniert hat.

Im Gegenteil: Die Information hat einen Benutzer erreicht, für den sie nützlich war.

Deshalb gilt:

**Das Erscheinen einer Station auf einer Internetkarte sollte nicht als einziges Kriterium dafür gelten, ob APRS korrekt funktioniert.**

## Der lokale Wert von Informationen

APRS wurde in erster Linie als taktisches Informationssystem entwickelt.

Das Wort *taktisch* bedeutet in diesem Zusammenhang keine militärische Anwendung. Gemeint sind Informationen, die für einen Benutzer in seiner aktuellen Situation und Umgebung nützlich sind.

Für einen Operator, der mit dem Auto unterwegs ist, kann die Information:

```text
SP9XYZ
145.550 MHz
```

nützlicher sein als eine genaue Historie der Bewegung dieser Station während der letzten Stunden.

Ebenso hat ein Objekt, das auf ein lokales Relais, einen Treffpunkt, einen Kontrollpunkt oder eine Aktivität im Gelände hinweist, den größten Wert für Benutzer in der Nähe.

Aus diesem Grund sollte APRS nicht als System verstanden werden, das jede denkbare Information an jeden Benutzer übertragen soll.

Das Ziel ist, **die richtige Information, den richtigen Benutzern, zur richtigen Zeit** bereitzustellen.

## Ein gemeinsamer Kanal

Klassisches APRS auf VHF arbeitet meistens mit **1200 Baud**.

Es handelt sich um einen Funkkanal, den alle Stationen innerhalb der Reichweite gemeinsam nutzen.

Jede Aussendung belegt einen Teil der verfügbaren Sendezeit. Ein Paket kann zusätzlich von einem oder mehreren Digipeatern wiederholt werden.

Aus diesem Grund erfordert APRS einen vernünftigen Umgang mit der verfügbaren Kanalkapazität.

Mehr Aussendungen bedeuten nicht immer mehr Information.

Zu häufiges Beaconing, unnötig lange Digipeater-Pfade oder zu lange Pakete können die Nutzung des Kanals durch andere Benutzer einschränken.

In einem gut geplanten Netz sollten Aussendungen **so häufig wie nötig, aber nicht häufiger als nötig** erfolgen.

Detaillierte Regeln zu Pfaden, Digipeatern, Kollisionen und Kanalauslastung werden in späteren Teilen der Dokumentation beschrieben.

## Broadcast statt klassischer Verbindung

Die meisten APRS-Aussendungen verwenden AX.25-UI-Frames, wobei UI für *Unnumbered Information* steht.

Daher wird vorher keine klassische Verbindung zwischen Sender und Empfänger aufgebaut.

Eine Station sendet ein Paket aus, und alle Stationen innerhalb der Reichweite können es empfangen.

Dieses Verfahren passt sehr gut zum Charakter von APRS.

Informationen wie:

```text
Ich bin hier
```

```text
Ich höre auf dieser Frequenz
```

```text
an diesem Ort befindet sich ein Relais
```

```text
eine Wetterwarnung wurde ausgegeben
```

können gleichzeitig für viele Benutzer interessant sein.

Das bedeutet jedoch nicht, dass APRS keine Kommunikation mit einer bestimmten Station unterstützt.

Das Protokoll verfügt über ein System kurzer Textnachrichten sowie Mechanismen zur Bestätigung ihres Empfangs. Diese Funktionen bauen jedoch auf dem Broadcast-Charakter des grundlegenden Netzes auf.

## Was kann eine APRS-Station sein?

Ein APRS-Knoten muss nicht ausschließlich ein GPS-Tracker in einem Fahrzeug sein.

Im Netz können unter anderem folgende Geräte und Stationen arbeiten:

- Handfunkgeräte,
- Mobilfunkgeräte,
- Basisstationen,
- Tracker,
- Computer mit Softwaremodem,
- Digipeater,
- IGates,
- Wetterstationen,
- Telemetriegeräte,
- Ballons,
- portable Stationen,
- spezialisierte APRS-Geräte.

Einige Geräte senden nur Daten. Andere können sie zusätzlich empfangen, analysieren und dem Operator darstellen.

Die umfassendste Nutzung von APRS entsteht dann, wenn ein Operator nicht nur seine eigene Position sendet, sondern auch die Informationen anderer Netzbenutzer verwendet.

## Kurze Geschichte

APRS wurde von **Bob Bruninga, WB4APR** entwickelt.

Die Wurzeln des Systems reichen in die 1980er Jahre zurück. Eine seiner frühen Hauptanwendungen war die Darstellung der Position und des Status von Teilnehmern an Aktivitäten im Gelände.

Ursprünglich wurde die Abkürzung APRS ausgeschrieben als:

**Automatic Position Reporting System**

Mit der Weiterentwicklung des Systems wurde immer deutlicher, dass die Position nur eine von vielen ausgetauschten Informationsarten ist.

Daher wurde der Name später als:

**Automatic Packet Reporting System**

ausgeschrieben.

Diese Änderung beschreibt sehr gut, wie APRS verstanden werden sollte.

Es ist nicht nur ein System zur Positionsmeldung.

Es ist ein System zur Meldung von **Informationen**.

## APRS als Werkzeug zur Unterstützung von Funkverbindungen

Eine der interessantesten Anwendungen von APRS ist die Unterstützung klassischer Sprachkommunikation.

Eine Station kann zusammen mit ihrer Position Informationen über die aktuell überwachte Frequenz übertragen.

Ein Operator in der Nähe sieht dann nicht nur das Rufzeichen der Station, sondern auch eine Information, mit der direkt eine Funkverbindung hergestellt werden kann.

Auf die gleiche Weise kann APRS über lokale Relais, Veranstaltungen, Aktivitäten oder andere Stationen in der Umgebung informieren.

In diesem Sinne ist APRS kein System, das getrennt von klassischer Amateurfunkkommunikation existiert.

Es kann eine Informationsschicht sein, die dabei hilft, diese Kommunikation zu beginnen.

## Wie sollte man APRS verstehen?

Am einfachsten kann APRS als gemeinsamer Funkkanal verstanden werden, über den kurze Meldungen zur aktuellen Situation verbreitet werden.

Jede Station kann einen kleinen Teil zum Gesamtbild beitragen:

```text
Ich bin hier.
```

```text
Ich höre hier.
```

```text
Hier befindet sich ein Relais.
```

```text
Hier findet eine Veranstaltung statt.
```

```text
Das sind die aktuellen Wetterbedingungen.
```

```text
Dieses Gerät hat diesen Status.
```

```text
Für dieses Gebiet gilt eine Warnung.
```

Die Kombination dieser Informationen ergibt ein aktuelles Bild davon, was im lokalen Netz geschieht.

Genau das ist der Kern von APRS.

## Die wichtigsten Punkte

**APRS ist nicht nur ein System zur Positionsverfolgung.**

Die Position ist nur eine von vielen Datenarten.

**APRS kann ohne Internet funktionieren.**

Ein lokales Funknetz kann das grundlegende Kommunikationsmedium sein.

**APRS-IS erweitert die Möglichkeiten des Systems, ist aber nicht sein Kern.**

Internetdienste nutzen Informationen aus dem APRS-Netz.

**APRS.fi ist nicht APRS.**

Es ist eine Möglichkeit, Daten aus APRS-IS darzustellen.

**APRS dient zur Verbreitung kurzer, aktueller Informationen.**

Ihr Wert hängt vor allem davon ab, ob sie für andere Benutzer des Netzes nützlich sind.

**APRS kann klassische Funkkommunikation unterstützen.**

Informationen über Position, Status und überwachte Frequenz können direkt dabei helfen, Kontakt herzustellen.

## Weiter

Um die Funktionsweise des Systems genauer zu verstehen, sollten als Nächstes folgende Themen behandelt werden:

- der Weg eines APRS-Pakets zwischen Stationen,
- die Beziehung zwischen APRS und AX.25,
- die Struktur eines APRS-Pakets,
- die grundlegenden APRS-Datentypen,
- die Rolle von Digipeatern,
- die Rolle von IGates und APRS-IS,
- die Grenzen des APRS-Funkkanals.

Diese Themen werden in den folgenden Artikeln im Abschnitt **Überblick über das APRS-Protokoll** beschrieben.

## Quellen und weiterführende Literatur

Für die Ausarbeitung dieses Artikels wurden vor allem folgende Quellen verwendet:

- APRS-Materialien und Dokumentation von Bob Bruninga, WB4APR,
- *APRS Protocol Reference*,
- das APRS-Specification-Dokumentationsprojekt von John Langner, WB2OSZ,
- *What is APRS?*,
- *Understanding APRS Packets*,
- Materialien zur modernen APRS-1.2-Spezifikation.

Die im APRSBox-Projekt verwendeten Referenzmaterialien befinden sich außerdem im Verzeichnis `APRS-SPEC` des Repositories:

`https://github.com/SQ9MDD/APRSBox/tree/main/APRS-SPEC`
