---
title: APRS-Handfunkstation
description: APRS im Handfunkgerät, Einsatz im Gelände, verfügbare Geräte und der Unterschied zwischen vollständiger APRS-Unterstützung und einem einfachen Tracker.
template: doc
tableOfContents: true
---

APRS im Handfunkgerät ist eine der Anwendungen, bei denen die Möglichkeiten des Systems besonders deutlich werden.

Anstatt einen separaten Tracker, Computer und ein Funkgerät mitzunehmen, kann ein einziges kleines Gerät sowohl für normale Sprachverbindungen als auch für den APRS-Datenaustausch genutzt werden.

Je nach Funktionsumfang des Funkgeräts können wir:

- unsere eigene Position senden,
- Positionen anderer Stationen empfangen,
- Entfernung und Richtung zu einem anderen Operator anzeigen,
- APRS-Nachrichten senden und empfangen,
- Objekte und lokale Informationen empfangen,
- die Frequenz angeben, auf der wir erreichbar sind,
- APRS-Aktivität in der Umgebung beobachten.

Damit ist APRS im Handfunkgerät weit mehr als nur eine Ergänzung zu einer Karte. Es kann einen zusätzlichen Informationskanal bereitstellen, der unabhängig vom Mobilfunknetz funktioniert.

## Wo ist APRS mit dem Handfunkgerät besonders nützlich?

Die Vorteile von APRS zeigen sich besonders beim Betrieb im Gelände.

### Berge und Wandern

Während einer Wanderung können wir regelmäßig unsere Position senden, sodass andere Funkamateure mit APRS sehen können, wo wir uns befinden.

Befindet sich eine andere APRS-Station in der Nähe, kann das Funkgerät deren Position, Entfernung und Richtung anzeigen.

Das kann besonders hilfreich sein bei:

- gemeinsamen Wanderungen,
- SOTA-Aktivierungen,
- Treffen im Gelände,
- Wettbewerben,
- Amateurfunkexpeditionen,
- der Suche nach anderen Operatoren in der Umgebung.

APRS ersetzt natürlich weder Navigationsgeräte noch Notrufsysteme, kann aber eine sehr nützliche zusätzliche Informationsquelle sein.

### Auf dem Wasser

Ähnliches gilt beim Segeln, Kajakfahren und anderen Aktivitäten auf dem Wasser.

Die Position einer Station kann per Funk übertragen werden, ohne dass Mobilfunkempfang erforderlich ist.

Operatoren innerhalb einer Gruppe können gegenseitig ihre Positionen sehen und dasselbe Handfunkgerät gleichzeitig für normale Sprachverbindungen nutzen.

### Allgemeiner Feldeinsatz

APRS ist auch bei vielen anderen Amateurfunkaktivitäten außerhalb des eigenen Standorts sehr praktisch.

Wenn wir beispielsweise an einem Test- oder Treffpunkt ankommen, können wir unsere Position zusammen mit einer Information wie dieser aussenden:

```text
QRV 145.500 MHz
```

Ein anderer Funkamateur, der unsere APRS-Station sieht, weiß dann nicht nur **wo wir sind**, sondern auch **auf welcher Frequenz er uns rufen kann**.

Das ist einer der wichtigen Unterschiede zwischen APRS und einem einfachen Positionsverfolgungssystem.

APRS soll Operatoren dabei helfen, einander zu finden und Funkverbindungen aufzubauen.

## APRS ohne Internetzugang

Für den direkten Austausch von APRS-Informationen zwischen Stationen ist kein Internetzugang erforderlich.

```text
Funkgerät A
   |
   |  RF
   |
Funkgerät B
```

Befinden sich beide Stationen in Funkreichweite, können sie Daten direkt austauschen.

Befindet sich ein Digipeater zwischen ihnen:

```text
Funkgerät A
   |
   v
Digipeater
   |
   v
Funkgerät B
```

kann das Paket wiederholt werden und ein deutlich größeres Gebiet erreichen.

Internet, APRS-IS und iGates erweitern die Möglichkeiten des Systems, sind aber für lokalen APRS-Betrieb nicht erforderlich.

Gerade deshalb ist APRS für den Einsatz im Gelände interessant.

## Was kann ein APRS-Handfunkgerät?

Hier gibt es einen sehr wichtigen Unterschied zwischen verschiedenen Geräten.

Die Aussage:

> **dieses Funkgerät unterstützt APRS**

sagt noch nicht, welche Funktionen tatsächlich vorhanden sind.

Der Funktionsumfang kann sehr unterschiedlich sein.

### Vollständiges APRS-Terminal

Fortgeschrittene Handfunkgeräte können einen großen Teil der APRS-Funktionen selbstständig bereitstellen.

Je nach Modell können dazu gehören:

- integriertes GPS,
- automatische Positionsaussendung,
- SmartBeaconing,
- Empfang und Dekodierung von Paketen anderer Stationen,
- Liste empfangener Stationen,
- Anzeige von Entfernung und Richtung,
- APRS-Nachrichten,
- Statusinformationen,
- APRS-Objekte,
- Frequenzinformationen,
- Konfiguration des APRS-Pfads,
- Zugriff auf ein integriertes TNC.

In diesem Fall ist das Funkgerät ein echtes, eigenständiges APRS-Terminal.

Ein Smartphone oder Computer ist nicht erforderlich, um andere Stationen zu sehen oder auf eine Nachricht zu antworten.

## Die Klassiker: Kenwood und Yaesu

Viele Jahre lang wurde APRS im Handfunkgerät vor allem mit Geräten von **Kenwood** und **Yaesu** verbunden.

Kenwood hat mehrere Generationen von Funkgeräten mit sehr umfangreicher APRS-Integration entwickelt. Ein modernes Beispiel ist die TH-D75-Serie.

Solche Geräte können nicht nur ihre eigene Position senden. Sie können auch Daten anderer Stationen empfangen, deren Position anzeigen, Nachrichten verarbeiten und weitere APRS-Funktionen bereitstellen.

Auch Yaesu bietet seit vielen Jahren Handfunkgeräte mit umfangreicher APRS-Unterstützung an, unter anderem verschiedene Modelle der FT-Serie.

Mit solchen Geräten kann APRS vollständig innerhalb des Funkgeräts genutzt werden.

## Immer mehr Geräte bieten APRS

Der Markt für Handfunkgeräte hat sich deutlich verändert.

APRS ist nicht mehr ausschließlich eine Funktion einiger weniger teurer Geräte japanischer Hersteller.

Immer mehr Handfunkgeräte, darunter auch viele Geräte chinesischer Hersteller, bieten GPS und Funktionen, die von den Herstellern als APRS bezeichnet werden.

Das ist grundsätzlich eine positive Entwicklung.

Eine größere Auswahl bedeutet:

- niedrigere Einstiegskosten,
- mehr Auswahl bei der Hardware,
- mehr APRS-Nutzer,
- größeres Interesse an Datenübertragung mit Handfunkgeräten.

Dabei gibt es jedoch einen sehr wichtigen Punkt zu beachten.

## "APRS unterstützt" bedeutet nicht immer dasselbe

Beim Kauf eines Funkgeräts sollte man sich nicht allein auf eine Angabe wie:

> **APRS supported**

in der technischen Beschreibung verlassen.

Bei einem Gerät kann dies ein vollständiges APRS-Terminal bedeuten.

Bei einem anderen lediglich die Möglichkeit, in regelmäßigen Abständen die eigene Position zu senden.

Das ist ein erheblicher Unterschied.

### APRS-Tracker

Die einfachste Variante sieht so aus:

```text
GPS
 |
Funkgerät
 |
APRS-Position
 |
RF
```

Das Funkgerät ermittelt seine Position per GPS und sendet regelmäßig einen APRS-Beacon.

Andere Stationen und iGates können diesen empfangen.

Für den Benutzer des Funkgeräts kann der APRS-Funktionsumfang damit jedoch bereits enden.

Das Funkgerät kann möglicherweise nicht:

- APRS-Pakete anderer Stationen dekodieren,
- eine Stationsliste anzeigen,
- deren Positionen darstellen,
- Nachrichten empfangen,
- Nachrichten senden,
- APRS-Objekte verarbeiten,
- weiterführende Protokollfunktionen unterstützen.

Ein solches Gerät ist praktisch vor allem ein **in ein Handfunkgerät integrierter APRS-Tracker**.

Daran ist nichts auszusetzen, wenn genau diese Funktion benötigt wird.

Problematisch wird es erst, wenn man beim Kauf ein vollständiges APRS-Terminal erwartet.

## Analoges APRS und Positionsdaten in digitalen Systemen

Es sollte außerdem geprüft werden, **wie das Funkgerät APRS umsetzt**.

Einige Geräte können klassisches AFSK-APRS über einen analogen Funkkanal senden.

Andere übertragen Positionsdaten innerhalb eines digitalen Funksystems oder benötigen zusätzliche Infrastruktur.

Aus Sicht des Benutzers kann das Ergebnis ähnlich aussehen: Die Position erscheint auf einer Karte.

Technisch können es jedoch völlig unterschiedliche Lösungen sein.

Wer das klassische lokale APRS-Netz im 2-m-Band nutzen möchte, sollte sicherstellen, dass das Funkgerät APRS tatsächlich über einen analogen Funkkanal unterstützt.

## Was sollte man vor dem Kauf prüfen?

Wenn APRS ein wichtiger Grund für den Kauf eines Funkgeräts ist, sollten vorher einige Fragen geklärt werden.

### Sendet das Funkgerät klassisches APRS über RF?

Nicht nur über Internet, eine App oder ein digitales Funknetz.

### Empfängt es APRS?

Das ist einer der wichtigsten Unterschiede zwischen einem APRS-Terminal und einem Tracker.

### Zeigt es empfangene Stationen an?

Idealerweise sollten sichtbar sein:

- Rufzeichen,
- Entfernung,
- Richtung,
- Symbol,
- Kommentar oder Status.

### Unterstützt es APRS-Nachrichten?

Dabei sollte getrennt geprüft werden:

- Empfang von Nachrichten,
- Senden von Nachrichten,
- ACK-Bestätigungen.

### Kann der APRS-Pfad eingestellt werden?

Zum Beispiel:

```text
WIDE1-1,WIDE2-1
```

Nicht jedes Gerät bietet dem Benutzer vollständige Kontrolle über die APRS-Parameter.

### Unterstützt es SmartBeaconing?

Beim Fuß- oder Mobilbetrieb kann eine intelligente Beacon-Steuerung unnötigen Funkverkehr deutlich reduzieren.

### Funktioniert APRS ohne Smartphone?

Wenn ein unabhängiger Betrieb im Gelände wichtig ist, ist dies eine besonders relevante Frage.

Einige Geräte benötigen für Teile ihrer APRS-Funktionen eine Smartphone-App.

### Dokumentiert der Hersteller die APRS-Implementierung wirklich?

Es lohnt sich, vor dem Kauf die Bedienungsanleitung zu lesen.

Das Wort `APRS` in einer Produktbeschreibung reicht als Information nicht aus.

## Das Smartphone als Ergänzung zum Funkgerät

Smartphone und APRS schließen sich nicht gegenseitig aus.

Einige Funkgeräte können per Bluetooth mit einem Smartphone zusammenarbeiten.

Das Smartphone kann dann eine komfortablere Möglichkeit bieten für:

- Bedienoberfläche,
- Kartenanzeige,
- Eingabe von Nachrichten,
- Anzeige empfangener Stationen.

Die eigentliche Funkübertragung erfolgt weiterhin über das Funkgerät.

Das kann ein sehr praktischer Kompromiss zwischen einem klassischen eigenständigen APRS-Handfunkgerät und einer umfangreicheren Feldausrüstung sein.

## APRS im Handfunkgerät ist mehr als ein Punkt auf der Karte

Die einfachste Nutzung von APRS im Handfunkgerät ist:

```text
GPS -> Beacon -> Karte
```

die Möglichkeiten des Systems gehen jedoch deutlich darüber hinaus.

Ein vollständiges APRS-Terminal kann einem Operator irgendwo in den Bergen beispielsweise anzeigen:

```text
SQ9ABC-7
3.2 km
Richtung: NE
QRV 145.500 MHz
```

und kurz darauf kann diese Station per Sprache gerufen werden.

Es kann eine kurze Nachricht von einem anderen Operator empfangen.

Es kann ein Objekt anzeigen, das einen Treffpunkt markiert.

Es kann zeigen, dass sich nur wenige Kilometer entfernt ein anderer Funkamateur befindet.

Dann ist APRS nicht mehr nur ein System zur Positionsverfolgung.

Es wird zu dem, wofür es gedacht ist: **einem lokalen Echtzeit-Informationssystem für Funkamateure**.

## Wo sollte man anfangen?

Wenn bereits ein Funkgerät mit vollständiger APRS-Unterstützung vorhanden ist:

1. Rufzeichen und SSID konfigurieren,
2. GPS einschalten,
3. die in der Region verwendete APRS-Frequenz einstellen,
4. einen passenden Pfad konfigurieren,
5. ein sinnvolles Beacon-Intervall oder SmartBeaconing einstellen,
6. zunächst empfangene Stationen beobachten,
7. APRS-Nachrichten ausprobieren,
8. anschließend die Konfiguration an die tatsächliche Nutzung anpassen.

In Polen und weiten Teilen Europas arbeitet klassisches APRS im 2-m-Band auf:

```text
144.800 MHz FM
```

Wer noch ein Gerät auswählt, sollte zuerst festlegen, was von APRS erwartet wird.

Wenn lediglich die eigene Position für andere sichtbar sein soll, kann ein einfacher Tracker völlig ausreichen.

Wer APRS dagegen **als Kommunikations- und Informationssystem nutzen möchte**, sollte ein Gerät wählen, das nicht nur die eigene Position sendet, sondern auch Daten anderer Stationen empfängt und darstellt sowie APRS-Nachrichten unterstützt.
