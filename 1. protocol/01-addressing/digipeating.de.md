---
title: Digipeating in APRS
description: Wie die Weiterleitung von APRS-Paketen durch Digipeater funktioniert, was H-Bit und *-Markierung bedeuten, wie WIDEn-N-Pfade verarbeitet werden und warum Duplikatunterdrückung für einen korrekt arbeitenden Netzbetrieb entscheidend ist.
template: doc
tableOfContents: true
---

Ein **Digipeater** ist eine digitale Relaisstation. Er empfängt einen vollständigen AX.25-Frame, analysiert dessen Pfad und sendet ihn erneut aus, wenn die erforderlichen Bedingungen erfüllt sind.

Er arbeitet daher nicht wie ein analoger Repeater, der ein Signal laufend weiterleitet. Ein Digipeater folgt dem Prinzip **store and forward**:

```text
Frame empfangen
    ↓
Pfad und Regeln prüfen
    ↓
auf Duplikat prüfen
    ↓
Pfadfeld ändern
    ↓
erneut senden
```

Meist wird das Paket auf demselben Funkkanal erneut ausgesendet, ein Multiport-Digipeater kann Verkehr jedoch auch zwischen unterschiedlichen Kanälen weiterleiten.

Die grundlegende Entscheidung über eine Weiterleitung betrifft das AX.25-Adressfeld und nicht den APRS-Inhalt im Information-Feld. Ein Digipeater muss nicht verstehen, ob ein Paket eine Position, Nachricht, Wetterdaten oder Telemetrie enthält, um dessen Pfad korrekt zu verarbeiten.

## Wichtiger Hinweis zur Spezifikation

Die ursprüngliche APRS Protocol Reference beschreibt Pfadformate und APRS-Mechanismen, enthält jedoch keinen vollständigen und eindeutigen Algorithmus für einen modernen Digipeater.

Daher entstanden im Laufe der Jahre Implementierungen, die sich in Einzelheiten unterscheiden.

Dieser Artikel verwendet als Bezugspunkte:

- das moderne, nachvollziehbare **New-N**-Modell,
- die Empfehlungen von APRS 1.1,
- den Referenzalgorithmus von John Langner WB2OSZ aus dem Dokument **APRS Digipeater Algorithm**.

Wo sich ältere Geräte oder andere Implementierungen abweichend verhalten können, wird dies ausdrücklich erwähnt.

## AX.25-Pfadfeld

Ein beispielhaftes APRS-Paket kann so aussehen:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

Nach Source Address und Destination Address folgen die Digipeater-Adressen:

```text
WIDE1-1,WIDE2-1
```

AX.25 erlaubt maximal acht Digipeater-Adressen in einem Frame.

Jede dieser Adressen enthält unter anderem ein **H-Bit**, also **Has Been Repeated**. Dieses Bit zeigt an, ob das betreffende Pfadelement bereits verwendet wurde.

Der Digipeater trifft seine Entscheidung anhand des **ersten noch nicht verwendeten Pfadelements**.

Bereits verwendete Elemente werden nicht erneut verarbeitet.

## H-Bit und `*`-Markierung

In der textuellen Monitordarstellung wird der Zustand des H-Bits mit:

```text
*
```

angezeigt.

Beispiel:

```text
SQ9MDD-7>APBOX0,SR5ABC*:...
```

bedeutet, dass die Adresse `SR5ABC` bereits für eine Weiterleitung verwendet wurde.

`*` ist weder Teil des Rufzeichens noch des Alias. Es ist eine textuelle Darstellung des Zustands im AX.25-Adressfeld.

### Nur die zuletzt verwendete Adresse trägt `*`

Im klassischen Monitorformat wird das Zeichen `*` an die **zuletzt verwendete Digipeater-Adresse** gesetzt.

Wenn der Pfad so aussieht:

```text
SR5AAA,SR5BBB*
```

bedeutet das, dass:

```text
SR5AAA
SR5BBB
```

verwendet wurden und `SR5BBB` das zuletzt verwendete Element ist.

Eine Darstellung wie:

```text
SR5AAA*,SR5BBB*
```

kann von manchen Programmen als direkte Visualisierung einzelner H-Bits angezeigt werden, entspricht aber nicht der Standardkonvention des klassischen Monitorformats. In der Standarddarstellung werden früher verwendete Adressen durch die Position des letzten Sternchens impliziert.

## Weiterleitung über konkrete Rufzeichen

Der einfachste Fall liegt vor, wenn die Quellstation konkrete Digipeater-Rufzeichen in den Pfad einträgt:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB:...
```

`SR5AAA` erkennt sein eigenes Rufzeichen als erstes noch nicht verwendetes Pfadelement.

Nach der Weiterleitung erhalten wir:

```text
SQ9MDD-7>APBOX0,SR5AAA*,SR5BBB:...
```

Danach kann `SR5BBB` das nächste Element verwenden.

Nach dessen Weiterleitung sieht die Standard-Monitordarstellung so aus:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

Im eigentlichen AX.25-Feld ist das H-Bit für beide verwendeten Adressen gesetzt.

Diese Arbeitsweise entspricht Routing über ausdrücklich angegebene Digipeater.

## Aliasse

APRS verlangt nicht, dass die Rufzeichen aller Digipeater entlang des Pfades bekannt sind. Stattdessen werden häufig Aliasse verwendet.

Ein Digipeater kann reagieren auf:

- sein eigenes Rufzeichen,
- einen konfigurierten einfachen Alias,
- einen Alias vom Typ **WIDEn-N**,
- lokale oder regionale Aliasse nach ähnlichen Regeln.

Wenn ein Alias nachvollziehbar sein soll, sollte ein moderner Digipeater sein eigenes Rufzeichen im Pfad hinterlassen, damit später erkennbar ist, welchen tatsächlichen Weg das Paket genommen hat.

Ein nachvollziehbarer Alias wie:

```text
TEMP
```

kann nach der Weiterleitung ersetzt werden durch:

```text
SR5ABC*
```

Es gibt auch nicht nachvollziehbare Alias-Implementierungen, die den Alias lediglich als verwendet markieren. Moderne APRS-Praxis bevorzugt jedoch Pfade, aus denen sich der tatsächliche Weg eines Pakets rekonstruieren lässt.

## WIDEn-N

Der wichtigste moderne Mechanismus für allgemeines APRS-Digipeating ist **WIDEn-N**.

Beispiel:

```text
WIDE2-2
```

Die erste Zahl:

```text
2
```

definiert die Alias-Klasse `WIDE2`.

Die zweite Zahl:

```text
2
```

ist der Zähler der noch verbleibenden Nutzungen dieses Pfadelements.

Vereinfacht:

```text
WIDE2-2
      ^
      2 Nutzungen verbleiben
```

Jeder korrekt arbeitende WIDEn-N-Digipeater verringert die zweite Zahl bei der Weiterleitung.

### Wenn N größer als 1 ist

Das Paket:

```text
SQ9MDD-7>APBOX0,WIDE2-2:...
```

wird vom Digipeater `SR5AAA` empfangen.

Nach der Weiterleitung:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

Der Digipeater:

1. hat sein Rufzeichen in den Pfad eingefügt,
2. es als verwendet markiert,
3. den Zähler von `2` auf `1` reduziert.

Das verbleibende:

```text
WIDE2-1
```

ist nun das erste noch nicht verwendete Pfadelement und kann von einem weiteren Digipeater verarbeitet werden.

### Wenn N gleich 1 ist

Empfängt ein weiterer Digipeater `SR5BBB`:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

ist der Zähler danach aufgebraucht.

Im Referenzalgorithmus bleibt kein sinnloses:

```text
WIDE2-0
```

im Pfad stehen.

Der Alias wird durch das Rufzeichen des Digipeaters ersetzt.

In der Standard-Monitordarstellung ergibt sich:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

Das Paket hat den gesamten vorgesehenen Abschnitt `WIDE2-2` verbraucht.

Einige ältere Implementierungen können einen aufgebrauchten Alias mit gesetztem H-Bit im Pfad belassen, weshalb in realen Logs auch andere Darstellungen vorkommen können.

### Wenn N gleich 0 ist

Ein Element wie:

```text
WIDE2-0
```

ist aufgebraucht und sollte keine weitere Weiterleitung auslösen.

In einem korrekt erzeugten Pfad sollte dieser Zustand nicht als erste noch nicht verwendete Adresse auftreten.

## WIDE1-1 und Fill-in-Digipeater

Der Alias:

```text
WIDE1-1
```

hat in einer typischen APRS-Netzarchitektur eine besondere Bedeutung.

Er wird vor allem für den ersten lokalen Hop über einen **Fill-in-Digipeater** verwendet, also eine Station mit kleinerer Reichweite an einem Ort, an dem Benutzer keinen guten direkten Zugang zu den großen Digipeatern haben.

Ein Beispielpfad:

```text
WIDE1-1,WIDE2-1
```

kann folgendermaßen verarbeitet werden.

Ausgangspaket:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

nach dem lokalen Fill-in `SR5FILL`:

```text
SQ9MDD-7>APBOX0,SR5FILL*,WIDE2-1:...
```

danach nach dem Haupt-Digipeater `SR5WIDE`:

```text
SQ9MDD-7>APBOX0,SR5FILL,SR5WIDE*:...
```

Ein Fill-in sollte entsprechend seiner lokalen Aufgabe konfiguriert sein. Ein typischer Fill-in reagiert auf `WIDE1-1`, sollte sich jedoch nicht wie ein vollwertiger Wide-Area-Digipeater verhalten, der beliebige `WIDEn-N` verarbeitet.

Die Wahl eines Pfades aus Sicht des Benutzers ist ein eigenes Thema. Hier ist nur wichtig, wie die einzelnen Pfadelemente verarbeitet werden.

## Das erste noch nicht verwendete Element ist entscheidend

Der grundlegende Digipeater-Algorithmus durchsucht den Pfad nicht beliebig nach einem Element, das er verarbeiten kann.

Vorrangig betrachtet wird:

**die erste noch nicht verwendete Digipeater-Adresse.**

Bei:

```text
SR5AAA*,WIDE2-1,SR5XYZ
```

ist das erste noch nicht verwendete Element:

```text
WIDE2-1
```

`SR5XYZ` steht später im Pfad und sollte im Normalbetrieb nicht über `WIDE2-1` hinweg "springen".

Diese Regel erhält die vom Sender vorgegebene Pfadreihenfolge und verhindert ein willkürliches Überspringen einzelner Elemente.

## Preemptive Digipeating

Einige moderne Implementierungen bieten optionales **preemptive digipeating**.

Dieser Mechanismus erlaubt einem Digipeater, sein eigenes Rufzeichen oder einen bestimmten Alias weiter hinten im noch nicht verwendeten Teil des Pfades zu finden und trotz vorheriger Elemente zu reagieren.

Zum Beispiel:

```text
WIDE1-1,SR5ABC
```

würde normalerweise erfordern, dass zuerst `WIDE1-1` verwendet wird.

Preemptive Digipeating kann `SR5ABC` erlauben, das Paket früher zu übernehmen.

Dies ist jedoch keine grundlegende Regel des klassischen APRS-Digipeatings. Es verändert die Semantik der Pfadreihenfolge und sollte bewusst eingesetzt werden. Das genaue Verhalten hängt von der jeweiligen Implementierung ab.

## Duplikatunterdrückung

Der WIDEn-N-Zähler und die H-Bits allein reichen nicht aus, um das Netz zu schützen.

Ein Digipeater muss auch **Duplikate** erkennen.

Dieselbe ursprüngliche Aussendung kann von mehreren benachbarten Digipeatern empfangen werden und anschließend über unterschiedliche Wege erneut bei einem davon eintreffen.

Ohne einen Speicher kürzlich weitergeleiteter Pakete würde das Netz sehr schnell immer neue Kopien derselben Information erzeugen.

Im WB2OSZ-Referenzalgorithmus merkt sich der Digipeater kürzlich weitergeleitete Pakete typischerweise für ungefähr:

```text
30 s
```

Taucht dasselbe Paket innerhalb dieses Zeitfensters erneut auf, wird es nicht noch einmal weitergeleitet.

### Der Pfad darf nicht Teil des Duplikatvergleichs sein

Das ist sehr wichtig.

Derselbe Frame kann nach dem Durchlaufen unterschiedlicher Digipeater verschiedene Pfade besitzen:

```text
SQ9MDD-7>APBOX0,SR5AAA*:...
```

und:

```text
SQ9MDD-7>APBOX0,SR5BBB*:...
```

und dennoch können beide Kopien derselben ursprünglichen Aussendung sein.

Das Pfadfeld darf daher nicht darüber entscheiden, ob ein Paket ein Duplikat ist.

Der WB2OSZ-Referenzalgorithmus vergleicht:

- Source Address,
- Destination Address, wobei dessen SSID ignoriert wird,
- das Information-Feld,

und ignoriert die Digipeater-Adressen.

Implementierungen speichern häufig einen Hash dieser Daten anstelle des gesamten Frames.

## Warum ein Digipeater die Paketdaten nicht verändern sollte

Beim normalen modernen Digipeating ändert sich der **Digipeater-Pfad**.

Ein Digipeater sollte nicht verändern:

```text
Source Address
Destination Address
Information
```

Würde eines dieser Felder geändert, wäre die weitergeleitete Kopie aus Sicht der Duplikaterkennung nicht mehr derselbe Frame.

Das ist besonders wichtig, weil unterschiedliche Kopien derselben Aussendung über verschiedene Pfade laufen können, aber trotzdem als dasselbe Paket erkannt werden müssen.

Der historische Routing-Mechanismus über Destination SSID war eine Ausnahme von dieser modernen Regel, ist heute jedoch veraltet und sollte nicht als Grundlage neuer Implementierungen dienen.

## Schutz vor Schleifen

Ein korrekt arbeitendes APRS-Netz verwendet mehrere unabhängige Schutzmechanismen:

1. Das **H-Bit** markiert bereits verwendete Pfadelemente.
2. Die **erste noch nicht verwendete Adresse** erzwingt die Verarbeitungsreihenfolge.
3. Der **WIDEn-N-Zähler** begrenzt die Zahl weiterer Weiterleitungen.
4. **Duplikatunterdrückung** verhindert, dass dieselbe Aussendung, die über einen anderen Weg empfangen wurde, erneut gesendet wird.
5. **Nachvollziehbarkeit durch das eigene Rufzeichen des Digipeaters** macht den tatsächlichen Weg sichtbar und hilft, fehlerhaftes Netzverhalten zu erkennen.

Keiner dieser Mechanismen ersetzt die anderen.

## Traced und non-traced Digipeating

Historisch gab es zwei wichtige Arten, Aliasse zu behandeln.

### Non-traced

Der Alias blieb im Pfad und nur sein Zustand oder Zähler wurde verändert.

Aus einer solchen Darstellung ließ sich nicht eindeutig erkennen, welcher physische Digipeater die Weiterleitung durchgeführt hatte.

### Traced

Der Digipeater fügt sein eigenes Rufzeichen in den Pfad ein.

Beispiel:

```text
WIDE2-2
```

nach dem ersten Hop:

```text
SR5AAA*,WIDE2-1
```

Dadurch kann der Empfänger den tatsächlichen Weg des Pakets rekonstruieren.

Das moderne **New-N Paradigm** bevorzugt nachvollziehbares WIDEn-N gerade deshalb, weil es Analyse, Diagnose und Verwaltung des Netzes ermöglicht.

## RELAY, WIDE und TRACE - historische Mechanismen

In älterer APRS-Dokumentation finden sich Pfade wie:

```text
RELAY
WIDE
TRACE
TRACEn-N
```

Sie dürfen nicht mit dem heutigen:

```text
WIDE1-1
WIDEn-N
```

verwechselt werden.

Die alten Aliasse `RELAY`, einfaches `WIDE` und `TRACE` wurden im Rahmen des **New-N Paradigm** außer Betrieb genommen, weil ältere Implementierungen damit große Mengen an Duplikaten erzeugten und keine ausreichende Kontrolle über den Netzverkehr boten.

APRS 1.1 und spätere Empfehlungen führen neue Implementierungen zu nachvollziehbarem WIDEn-N und kleinen, durch die lokalen Netzbedingungen begründeten N-Werten.

Artikel und Konfigurationen mit `RELAY,WIDE` sollten als historisches Material betrachtet werden.

## Begrenzung zu großer N-Werte

Ein moderner Digipeater muss nicht jeden vom Benutzer gesendeten Wert:

```text
WIDEn-N
```

uneingeschränkt akzeptieren.

Implementierungen können lokale Grenzwerte und Schutzmechanismen für übermäßig große Werte verwenden, damit eine einzelne falsch konfigurierte Station nicht unnötig einen großen Teil des gemeinsamen Kanals belegt.

Ein Paket mit einem sehr aggressiven Pfad kann zum Beispiel:

- verworfen,
- auf einen Hop begrenzt,
- entsprechend lokaler Regeln umgeschrieben werden.

Dies schützt die gemeinsam genutzte Kanalressource und ändert nicht die grundsätzliche Bedeutung von WIDEn-N.

## Maximale Pfadlänge

AX.25 erlaubt maximal acht Digipeater-Adressen.

Das ist für nachvollziehbares WIDEn-N wichtig, weil aufeinanderfolgende Digipeater ihre Rufzeichen in den Pfad einfügen können.

Ist die Adressliste bereits voll, kann eine Implementierung nicht unbegrenzt weitere Elemente hinzufügen.

Der Referenzalgorithmus berücksichtigt diese Grenze beim Umschreiben des Pfades.

Neue Systeme sollten keine künstlich langen Routen erzeugen. Im praktischen APRS geht es um einen wirksamen Zugang zum lokalen Netz, nicht um die Maximierung der Hop-Anzahl.

## Verzögertes und "viscous" Digipeating

Einige Implementierungen verwenden zusätzlich einen Mechanismus zur verzögerten Weiterleitung.

Der Digipeater:

1. stellt fest, dass das Paket zur Weiterleitung geeignet ist,
2. sendet es nicht sofort,
3. hört für kurze Zeit, ob ein anderer Digipeater denselben Frame weiterleitet,
4. verwirft seine eigene Aussendung, wenn er eine passende Kopie hört.

Dadurch kann die Zahl gleichzeitiger Weiterleitungen im Netz reduziert werden.

Dies ist kein grundlegender Bestandteil des APRS- oder AX.25-Formats. Es handelt sich um eine Implementierungsstrategie, die von manchen Programmen und Geräten verwendet wird.

## Was verändert ein Digipeater tatsächlich?

Für modernes standardmäßiges Digipeating lässt sich die wichtigste Regel in einem Satz zusammenfassen:

> **Ein Digipeater leitet dasselbe Paket weiter und ändert nur die Informationen, die zur Verarbeitung des AX.25-Pfades erforderlich sind.**

Beispiel:

```text
vorher:
SQ9MDD-7>APBOX0,WIDE2-2:!5213.45N/02100.12E>Test

nach dem ersten Digi:
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:!5213.45N/02100.12E>Test
```

Unverändert blieben:

```text
SQ9MDD-7                    Source Address
APBOX0                      Destination Address
!5213.45N/02100.12E>Test    Information
```

Geändert wurde nur der Pfad:

```text
WIDE2-2
```

zu:

```text
SR5AAA*,WIDE2-1
```

Diese Trennung ist entscheidend für korrektes Routing, Pfadverfolgung und Duplikatunterdrückung.

## Quellen

Dieser Artikel basiert auf:

- [APRS Digipeater Algorithm - WB2OSZ](https://github.com/wb2osz/aprsspec/blob/main/APRS-Digipeater-Algorithm.pdf)
- [APRS Digipeaters - WB2OSZ](https://raw.githubusercontent.com/wb2osz/direwolf-doc/main/APRS-Digipeaters.pdf)
- [Understanding APRS Packets](https://github.com/wb2osz/aprsspec/blob/main/Understanding-APRS-Packets.pdf)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [New-N Paradigm / Fixing Digipeaters](https://www.aprs.org/fix14439.html)
- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
