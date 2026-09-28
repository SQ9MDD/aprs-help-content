---
title: APRS-Pfade in der Praxis
---

Ein APRS-Pfad legt fest, welche Digipeater einen Funkrahmen in welcher Reihenfolge wiederholen dürfen. Er kann bestimmte Stationen angeben oder Aliase verwenden, die von mehreren Digipeatern unterstützt werden. Der tatsächliche Übertragungsweg hängt sowohl vom eingetragenen Pfad als auch von der Konfiguration der Stationen und ihren jeweiligen Versorgungsgebieten ab.

Dieser Artikel erläutert die klassische AX.25-Adressierung, das Verfahren `WIDEn-N`, Pfade mit und ohne Wegverfolgung, regionale und veranstaltungsbezogene Aliase, moderne Ansätze für die Weiterleitung durch Fill-in-Digipeater sowie den Sonderfall satellitengestützter Digipeater. Die Beispiele zeigen mögliche Veränderungen eines Rahmens unter angenommenen Digipeater-Konfigurationen. Sie bedeuten nicht, dass die genannten Aliase im gesamten APRS-Netz verfügbar sind.

## 1. Aufbau und Schreibweise eines Pfades

Der Pfad steht im Adressfeld eines AX.25-Rahmens nach der Ziel- und Quelladresse. In der lesbaren TNC2-Darstellung werden seine Elemente durch Kommas getrennt:

```text
SQ9MDD-9>APRS,WIDE2-1:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SP2-2:...
```

`SQ9MDD-9` ist die Quelle des Rahmens, während `APRS` eine beispielhafte Zieladresse (TOCALL) und kein Digipeater-Name ist. Erst die Adressen nach dem ersten Komma bilden den Pfad. Sind keine vorhanden, wird der Rahmen ohne Anforderung einer Weiterleitung ausgesendet:

```text
SQ9MDD-9>APRS:...
```

Geräte können diese Einstellung als `DIRECT` bezeichnen. Dabei handelt es sich jedoch weder um einen zusätzlichen Hop noch um einen Alias, der in den Rahmen eingetragen werden müsste. Auch ein Rahmen ohne Pfad kann von entfernten Stationen empfangen und von IGates, die ihn direkt hören, an APRS-IS weitergeleitet werden.

Bei der regulären Verarbeitung prüft ein Digipeater das **erste noch nicht verwendete Element** des Pfades. Spätere Elemente werden erst aktiv, nachdem die vorhergehenden verarbeitet wurden. Ausnahmen sind speziell konfigurierte Verfahren wie das weiter unten erläuterte Preemptive Digipeating.

### AX.25-Grenzen

Jede Adresse in einem AX.25-Funkrahmen belegt sieben Byte: sechs für das Rufzeichen oder den Alias (bei kürzeren Namen mit Leerzeichen aufgefüllt) und ein weiteres Byte, das unter anderem die vier Bit lange SSID sowie Adresskennzeichen enthält. Eine AX.25-SSID liegt im Bereich `0..15`. Das traditionelle, bei APRS verwendete Format sieht höchstens acht Digipeater-Adressen vor. Das bedeutet nicht, dass alle acht verwendet werden sollten: Jeder zusätzliche Eintrag verlängert den Rahmen, und bei der Wegverfolgung können weitere freie Adresspositionen benötigt werden.

Beim Entwurf von Aliasen sind zwei Fälle zu unterscheiden:

- Ein einfacher Alias wie `ARISS` oder `RAJD` passt in die sechs Zeichen des Adressfeldes.
- Bei einem Alias des Typs `n-N` wie `RAJD2-2` belegt `RAJD2` das sechs Byte lange Namensfeld (`RAJD` plus die Ziffer `n`), während `-2` die als Zähler `N` verwendete SSID ist.

Bei einem einstelligen `n` darf der Basisname eines solchen Alias deshalb höchstens fünf Zeichen lang sein. Die Textdarstellung hebt die Beschränkungen des binären AX.25-Adressfeldes nicht auf.

### Sternchen und H-Bit

Jede Digipeater-Adresse besitzt ein eigenes **H-Bit** (*has been repeated*), das kennzeichnet, dass das jeweilige Pfadelement bereits verwendet wurde. In der üblichen TNC2-Monitordarstellung erscheint ein Sternchen **nur hinter dem zuletzt verwendeten Element**; alle vorhergehenden Elemente gelten stillschweigend ebenfalls als verwendet:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...   # vor der Weiterleitung
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...  # nach dem ersten Digi
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...  # nach dem zweiten Digi
```

Die letzte Zeile bedeutet nicht, dass `SR5AAA` den Rahmen nicht wiederholt hat. Im binären AX.25 sind die H-Bits beider Adressen gesetzt. Manche Diagnoseprogramme zeigen hinter jeder bereits verwendeten Adresse ein Sternchen, doch das ist nicht die übliche verkürzte TNC2-Darstellung. Das Textzeichen `*` repräsentiert das H-Bit und wird nicht als Bestandteil der AX.25-Adresse übertragen.

## 2. Entstehung des New-N Paradigm

Ältere APRS-Netze verwendeten unter anderem die Aliase `RELAY`, `WIDE`, `TRACE` und `TRACEn-N`. Damit ließ sich die Reichweite erweitern, doch einige damalige Implementierungen erzeugten übermäßig viele Duplikate derselben Rahmen. Außerdem wurden beim ursprünglichen `WIDEn-N` die Rufzeichen der Digipeater häufig nicht aufgezeichnet, was die Verkehrsanalyse und Netzplanung erschwerte.

Die Ende 2004 begonnene Initiative **New-N Paradigm** ordnete diese Verfahren neu:

- Der historische Alias `RELAY` wurde durch `WIDE1-1` ersetzt, damit Mobilstationen weiterhin einfache häusliche Fill-in-Digipeater nutzen konnten.
- Anstelle des alten einzelnen Alias `WIDE` verbreitete sich `WIDEn-N` mit einem Zähler für die verbleibenden Weiterleitungen.
- `WIDEn-N` wurde zunehmend im Trace-Modus verarbeitet, der historisch unter anderem durch `UITRACE` realisiert wurde.
- Das Verfahren `UIFLOOD` ohne vollständige Wegverfolgung blieb unter anderem für regionale `SSn-N`-Netze erhalten.
- Das Begrenzen überhöhter Zählerwerte und die Unterdrückung von Duplikaten wurden zu grundlegenden Bestandteilen der Digipeater-Konfiguration.

Die Bezeichnungen `UITRACE` und `UIFLOOD` stammen aus bestimmten TNC-Implementierungen. Andere Programme können gleichartige Funktionen unter anderen Namen anbieten. `RELAY`, das alte `WIDE` und `TRACE` sind heute vor allem als historische Verfahren oder Bestandteile älterer Konfigurationen zu betrachten, nicht als austauschbare Entsprechungen des modernen `WIDEn-N`.

## 3. Weiterleitung über bestimmte Rufzeichen und einfache Aliase

Der einfachste Pfad gibt einen Digipeater direkt an. Die aufeinanderfolgenden Rufzeichen legen die Reihenfolge der Weiterleitung fest:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...
```

`SR5BBB` wird den Rahmen nicht bereits in der ersten Stufe wiederholen, nur weil diese Station die Aussendung empfangen hat. Zuerst muss die Adresse `SR5AAA` abgearbeitet sein. Explizite Pfade eignen sich, wenn die Route bewusst über bestimmte Stationen festgelegt werden soll, beispielsweise für Verbindungen zwischen zwei Punkten.

Ein Digipeater kann auch einen **einfachen Alias** wie `RAJD` verarbeiten. Beim Empfang eines über `RAJD` adressierten Rahmens kann er den Alias durch sein eigenes Rufzeichen ersetzen oder den Alias beibehalten und sein Rufzeichen gesondert einfügen. Die Veränderung hängt von Implementierung und Konfiguration ab:

```text
SQ9MDD-9>APRS,RAJD:...
SQ9MDD-9>APRS,SR5AAA*:...       # Alias durch Rufzeichen ersetzt
```

Bei der zweiten Variante kann die Stationskennung gesondert eingefügt werden, wodurch ein weiteres Adressfeld benötigt wird. Ein einfacher Alias enthält keinen Zähler für verbleibende Weiterleitungen. Deshalb darf weder ein Verhalten wie bei `WIDE2-2` noch eine identische Weiterleitungsregel bei allen Stationen angenommen werden, die denselben Alias erkennen.

## 4. Die Schreibweise `n-N` verstehen

In der Aliasfamilie `WIDEn-N` bezeichnet `n` die Ziffer vor dem Bindestrich und `N` den SSID-Wert dahinter:

```text
WIDE2-2
    ^ ^
    n N
```

`n` kennzeichnet die Aliasklasse und die angegebene ursprüngliche Hop-Anzahl; **`N` zählt die noch verbleibenden Weiterleitungen**. Gewöhnlich beginnt ein Rahmen seine Route mit `n = N`. Aber auch `WIDE2-1` ist gültig: Es gehört zur Familie `WIDE2`, fordert jedoch nur noch einen Hop an.

Bei jeder regelkonformen Weiterleitung wird `N` um eins verringert. Ein vereinfachter Ablauf ohne Kennzeichnung der einzelnen Digipeater sieht so aus:

```text
WIDE2-2 -> WIDE2-1 -> WIDE2*
SP2-2   -> SP2-1   -> SP2*
```

Ist der Zähler aufgebraucht, kann das Element ohne `-0` erscheinen, da eine SSID mit dem Wert null in der Textdarstellung weggelassen wird. Das H-Bit kennzeichnet es dann als verwendet. Die Implementierungen unterscheiden sich darin, wie sie einen aufgebrauchten Alias beibehalten oder ersetzen. Deshalb zeigen reale Protokolle nicht immer genau dieselbe Zusammenstellung der Felder.

`WIDE2-2` garantiert **nicht nur zwei Aussendungen im gesamten Netz**. Es erlaubt höchstens zwei aufeinanderfolgende Weiterleitungen *innerhalb eines bestimmten Zweigs des Paketweges*. Hören mehrere Digipeater den ursprünglichen Rahmen, kann jeder einen eigenen Zweig erzeugen, wodurch die Gesamtzahl der Aussendungen steigt.

## 5. Pfade mit Wegverfolgung (trace)

Bei einem Pfad mit Wegverfolgung hinterlässt jeder Digipeater eine Information über seine Identität. Das ist ein wesentliches Merkmal des modernen `WIDEn-N` gemäß New-N Paradigm: Der Weg, den eine empfangene Kopie des Rahmens genommen hat, lässt sich nachvollziehen.

Beispiel für einen Zweig der `WIDE2-2`-Wegverfolgung:

```text
SQ9MDD-9>APRS,WIDE2-2:...
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...
```

Im Beispiel fügen die Digipeater ihre Rufzeichen ein, und der aufgebrauchte Alias bleibt im Pfad stehen. Eine andere korrekt konfigurierte Implementierung kann den Alias durch das letzte Rufzeichen ersetzen und so eine kürzere Enddarstellung wie `SR5AAA,SR5BBB*` erzeugen. Bei der Auswertung von Protokollen muss daher das Verhalten der jeweiligen Software berücksichtigt werden. Eine identische Form aller Header darf nicht vorausgesetzt werden.

Die Wegverfolgung erleichtert die Diagnose, aber jedes eingefügte Rufzeichen beansprucht weitere sieben Byte im Adressfeld. Bei langen Pfaden kann der Platz für zusätzliche Adressen ausgehen.

## 6. Pfade ohne Wegverfolgung (flood)

Bei der Variante ohne Wegverfolgung verringert der Digipeater den Aliaszähler, **fügt aber sein eigenes Rufzeichen nicht in den Pfad ein**. Es handelt sich weder um ein anderes Protokoll noch um ein besonderes Format des APRS-Informationsfeldes, sondern um eine Art der Adressverarbeitung durch Digipeater, die historisch mit `UIFLOOD` verbunden ist.

Beispiel für ein Netz, in dem der regionale Alias `SP` ohne Wegverfolgung eingerichtet ist:

```text
SQ9MDD-9>APRS,SP2-2:...
SQ9MDD-9>APRS,SP2-1:...
SQ9MDD-9>APRS,SP2*:...
```

Alle drei Zeilen können denselben Rahmen darstellen, der von verschiedenen Stationen weitergeleitet wurde. Aus dem endgültigen Header geht nicht hervor, welche Digipeater beteiligt waren. Ein Empfänger, der `SP2-1` hört, kann allein anhand des Pfades nicht feststellen, welche Station den vorherigen Hop ausgeführt hat.

Wesentliche Eigenschaften des Flood-Verfahrens:

- Der Pfad wird nicht bei jeder Weiterleitung um ein weiteres Digi-Rufzeichen verlängert.
- Ohne vollständige Wegverfolgung ist der Übertragungsweg schwieriger nachzuvollziehen.
- Zählerbegrenzung und Duplikatunterdrückung bleiben notwendig.
- Flood bedeutet keine unbegrenzte Verbreitung: Die beteiligten Stationen und ihre Konfiguration bestimmen den Versorgungsbereich.

### Flood mit teilweiser Identifizierung

Ein Betrieb ohne vollständige Wegverfolgung bedeutet nicht zwangsläufig, dass jede Information über Zwischenstationen verloren geht. Historische `UIFLOOD`-Konfigurationen mit der Option `ID` erlaubten unter anderem, Angaben über den ersten und den letzten Digipeater eines regionalen Pfades zu behalten. Bei einem gemischten Pfad `WIDE1-1,SSn-N` kann das erste Rufzeichen außerdem aus dem gesondert abgearbeiteten Element `WIDE1-1` stammen.

Eine solche Lösung **liefert keinen vollständigen Trace**. Die Art der Identifizierung und die Position der beibehaltenen Aliase hängen vom jeweiligen TNC ab. Nicht jede Verwendung eines regionalen Alias ist automatisch Flood: Genau derselbe Alias kann auch für Trace konfiguriert werden.

## 7. Ein- und mehrteilige Pfade sowie Fill-in-Digipeater

Die Anzahl der Pfadelemente entspricht der Zahl der durch Kommas getrennten Einträge. Die Hop-Anzahl ergibt sich aus deren Zählern und den Verarbeitungsregeln. Ein einzelnes Element kann mehr als eine Weiterleitung anfordern:

| Pfad | Anzahl der Elemente | Angeforderte Hops in einem Zweig |
| --- | ---: | ---: |
| `WIDE2-1` | 1 | 1 |
| `WIDE2-2` | 1 | 2 |
| `SP2-2` | 1 | 2 |
| `WIDE1-1,WIDE2-1` | 2 | 2 |
| `WIDE1-1,WIDE2-2` | 2 | 3 |
| `SP1-1,SP2-2` | 2 | 3, sofern beide Elemente unterstützt werden |

Bei normaler Verarbeitung wird das zweite Element erst aktiv, wenn das erste aufgebraucht ist. Das bedeutet aber nicht, dass jedes Element von einer anderen *Gerätekategorie* verarbeitet werden muss: Entscheidend sind die konfigurierten Aliase.

### Ursprung von `WIDE1-1`: lokale Fill-ins für Mobilstationen

In älteren APRS-Konfigurationen verwendeten Mobilstationen unter anderem Pfade, die mit `RELAY` begannen. Den ersten Hop konnte dann eine nahe gelegene Heimstation mit einem einfachen TNC übernehmen, der als **Fill-in-Digi** arbeitete, selbst wenn er nicht über die Funktionen eines vollständigen regionalen Digipeaters verfügte. Das war besonders für mobile und tragbare Stationen mit geringerer Leistung, weniger günstigen Antennen oder Fahrwegen durch örtliche Versorgungslücken wichtig. Historische Pfade mit `RELAY` und `WIDE` begünstigten jedoch eine übermäßige Zahl von Duplikaten.

Mit der Einführung des New-N Paradigm wurde `RELAY` durch **`WIDE1-1`** ersetzt. Diese Lösung berücksichtigte die Fähigkeiten bereits vorhandener, einfacher Heimgeräte, darunter Mini-Digi-Konstruktionen. Solch ein TNC musste weder den `WIDEn-N`-Algorithmus verstehen noch dessen Zähler verringern: Es genügte, den exakten Alias `WIDE1-1` zu erkennen, den Rahmen einmal zu wiederholen und dieses Pfadelement als verwendet zu markieren. Die weitere Wegverfolgung übernahm anschließend ein vollständiger Digipeater mit `WIDEn-N`-Unterstützung.

Daraus ergibt sich der Pfad für Mobilstationen, die örtliche Fill-ins nutzen:

```text
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...          # Mobilstation sendet
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...           # häuslicher Fill-in, erster Hop
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...      # regionaler Digi, zweiter Hop
```

`SR5AAA` steht hier für einen einfachen Fill-in, der `WIDE1-1` durch sein Rufzeichen ersetzt, während `SR5BBB` das verbleibende `WIDE2-1` verarbeitet. Ein leistungsfähigeres Gerät kann auch den bereits verwendeten Alias `WIDE1` beibehalten; der endgültige Header wird dadurch länger. Ein einfacher Digipeater, der `WIDE1-1` wie einen gewöhnlichen Alias behandelt, kann ihn hingegen ohne Änderung seiner SSID als verwendet markieren. In diesem Fall schließt das H-Bit, nicht zwingend die sichtbare Darstellung `WIDE1-0`, das erste Element ab.

**Ein klassischer einfacher Fill-in, der ausschließlich `WIDE1-1` unterstützt, sollte weder `WIDE2-1` noch andere Elemente des weiterführenden Pfades bearbeiten.** Seine Aufgabe besteht darin, ein Paket einmal aus einer örtlichen Versorgungslücke in Richtung eines regionalen Digipeaters weiterzuleiten. Werden solche Relais dort eingesetzt, wo Mobilstationen das regionale Netz bereits gut erreichen, entstehen unnötige zusätzliche Rahmenkopien auf dem gemeinsamen Kanal. Für moderne Fill-ins, die ihre Aussendung auch von der Empfangsart und dem beobachteten Verkehr abhängig machen, können andere Regeln gelten; sie werden im Folgenden erläutert.

`WIDE1-1` ist allerdings nicht ausschließlich einfachen Fill-ins vorbehalten. Auch ein vollständiger regionaler Digipeater kann diesen Alias verarbeiten, wenn er die Mobilstation direkt hört. Dann wird der erste Hop von `WIDE1-1,WIDE2-1` ohne Beteiligung einer Heimstation ausgeführt; **dadurch entsteht kein zusätzlicher Hop über die zwei in diesem Pfad angeforderten Hops hinaus**.

### Warum `WIDE1-1` für Heimstationen nicht empfohlen wurde

Zu unterscheiden sind **die Verarbeitung des Alias `WIDE1-1` durch einen häuslichen Fill-in** und **das Senden eigener Baken einer Heimstation mit `WIDE1-1` im Pfad**. Nach den ursprünglichen Empfehlungen des New-N Paradigm war der erste Fall vor allem für Mobilstationen gedacht. Gewöhnliche Feststationen sollten hingegen `WIDEn-N`-Pfade mit regional angepasster Hop-Anzahl verwenden, unter den von den Initiatoren beschriebenen Bedingungen historisch häufig `WIDE2-2`. `WIDE1-1` war nicht als standardmäßiges erstes Pfadelement für Heimstationen vorgesehen.

Der Grund liegt in der Netztopologie. Eine Feststation besitzt normalerweise einen gleichbleibenden Standort und eine günstigere Antennenanlage, sodass sie häufig direkt einen regionalen Digipeater erreicht. Wird `WIDE1-1` hinzugefügt, aktiviert sie auch nahe gelegene Fill-ins, die sie gar nicht benötigt. Deren Weiterleitungen können sich mit der Aussendung des regionalen Digipeaters überschneiden. Der Austausch von `WIDE2-2` gegen `WIDE1-1,WIDE2-1` erhöht die zulässige Hop-Anzahl nicht, sondern eröffnet den ersten Hop einer zusätzlichen Gruppe von Relaisstationen.

Dies ist kein Verbot durch AX.25. Eine außergewöhnlich gelegene Feststation in einer tatsächlichen Versorgungslücke kann technisch einen Fill-in verwenden, sofern dies durch die örtliche Topologie und Absprachen zwischen den Betreibern gerechtfertigt ist. Eine solche Ausnahme muss jedoch vom **ursprünglichen Zweck und den ursprünglichen Empfehlungen** unterschieden werden: `WIDE1-1` wurde eingeführt, um Mobilstationen mit einfachen örtlichen Digipeatern zu unterstützen, nicht als universeller Pfad für sämtliche APRS-Geräte.

Der Pfad `SP1-1,SP2-2` funktioniert hinsichtlich Reihenfolge und Zählern entsprechend, **sofern** das örtliche Netz passende Regeln für beide Elemente besitzt. Die Schreibweise `SP1-1` allein macht den ersten Digipeater nicht zu einem Fill-in. Bei regionalen Aliasen ist für diese Rolle eine gesonderte Abstimmung der Betreiber erforderlich.

### Moderne Fill-ins: `direct-only` und `viscous delay`

Die historische Konstruktion `WIDE1-1,WIDE2-1` löste ein konkretes Problem: Ein einfacher Mini-Digi zu Hause erkannte einen Alias und wiederholte den Rahmen, ohne beurteilen zu können, ob ein größerer regionaler Digipeater dies bereits zuvor getan hatte. Moderne Software kann bei der Entscheidung über eine Weiterleitung auch die Herkunft des Rahmens und den auf dem Kanal beobachteten Verkehr berücksichtigen. Das verändert nicht die AX.25-Adressierungsregeln, ermöglicht jedoch einen gezielteren Einsatz der vorhandenen Verfahren.

Zwei sich ergänzende Techniken sind:

- **`direct-only`**: Der Digipeater berücksichtigt nur Rahmen, die er direkt vom Absender empfangen hat, nicht aber bereits von einem anderen Digi weitergeleitete Kopien. Dadurch wird das lokale Relais nicht automatisch zur nächsten Station jedes angetroffenen Pfades.
- **`viscous delay`**: Der Digipeater hält einen geeigneten Rahmen für eine kurze, konfigurierte Zeit zurück. Hört er währenddessen eine entsprechende Weiterleitung eines anderen Digipeaters, kann er seine eigene Aussendung abbrechen. Beobachtet er keine solche Weiterleitung, sendet er den wartenden Rahmen gemäß seinen Regeln.

Diese Techniken sind kein neues Pfadformat. APRX dokumentierte den *viscous digipeater* bereits 2009, und sein Modus `directonly` lässt sich mit `viscous-delay` kombinieren. Entscheidend ist also nicht das Entstehungsdatum der Algorithmen, sondern die Möglichkeit, sie statt einer bedingungslosen Wiederholung durch einfache Mini-Digis einzusetzen.

Beispielsweise lässt sich ein intelligenter lokaler Fill-in **gezielt so konfigurieren**, dass er direkt empfangene `WIDE2-2`-Rahmen verzögert und unter Berücksichtigung von Duplikaten verarbeitet. Leitet der regionale Digi den Rahmen zuerst weiter und hört die lokale Station diese Aussendung innerhalb ihrer Wartezeit, verzichtet der Fill-in auf seinen eigenen TX. Leitet der regionale Digi den Rahmen nicht weiter, kann der örtliche Fill-in den ersten Hop übernehmen und `WIDE2-1` für die weitere Verarbeitung belassen. Dies ist ein Beispiel für eine mögliche Netzrichtlinie, **nicht das Standardverhalten aller Digipeater**:

```text
SQ9MDD-9>APRS,WIDE2-2:...  # Aussendung der Mobilstation

# Fall A: Der regionale Digi empfängt die Station direkt
# Der regionale Digi wiederholt; der Fill-in hört die Kopie und bricht seinen TX ab.

# Fall B: Der regionale Digi empfängt die Station nicht direkt
# Der Fill-in hört keine andere Kopie und sendet nach der Verzögerung:
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
```

In einem so konfigurierten Netz ist nicht nur entscheidend, **welcher Alias eingetragen wurde**, sondern auch, **ob ein bestimmter Digipeater tatsächlich senden muss**. Ein gesondertes erstes Element `WIDE1-1` kann dann entbehrlich werden. Wo örtliche Geräte ausschließlich diesen Alias unterstützen, bleibt es allerdings notwendig. Ebenso verleihen `direct-only` und `viscous delay` einem Rahmen ohne Pfad nicht automatisch die Erlaubnis zur Weiterleitung: Der Digi benötigt eine entsprechende Adressierungsregel oder ein bewusst eingerichtetes Sonderverhalten.

Eine Verzögerung garantiert nicht die Unterdrückung sämtlicher Duplikate. Wenn der Fill-in die Aussendung des regionalen Digipeaters nicht hört, kann er daraus nicht schließen, dass keine Weiterleitung stattgefunden hat. Zudem erhöht verzögerter TX die Zustellzeit, und zu viele vergleichbare Relais können den gemeinsamen Kanal weiterhin überlasten. Parameter und unterstützte Aliase sind an die tatsächliche Netztopologie anzupassen.

Daraus ergibt sich ein wesentlicher Wandel der Herangehensweise: **Die historische Pfadempfehlung für Mobilstationen diente der Zusammenarbeit mit den Einschränkungen der damaligen Infrastruktur und war keine zeitlose Protokollvorgabe**. In Netzen mit intelligenten Digipeatern kann die Weiterleitungsstrategie wichtiger sein als die traditionelle Unterscheidung zwischen einem speziellen Pfad für Mobilstationen mit Fill-in und einem Pfad für Stationen mit direktem Zugang zum regionalen Digi. Das Pfadfeld bestimmt allerdings weiterhin, welche Weiterleitungen zulässig sind.

## 8. Regionale und veranstaltungsbezogene Aliase

Mit einem regionalen Alias lässt sich eine *logische Gruppe von Digipeatern* bestimmen, die bestimmten Verkehr bearbeiten soll. Das klassische Konzept `SSn-N` entstand, damit Rahmen entfernte Teile einer Region erreichen konnten, ohne das gesamte benachbarte `WIDEn-N`-Netz einzubeziehen. Je nach Land bestehen unterschiedliche Namenskonventionen und Konfigurationen.

Beispiele möglicher Schreibweisen:

```text
SP2-2
WM2-2
```

`SP` und `WM` sind hierbei Basisnamen von Aliasen und keine Verwaltungsgrenzen, die das Protokoll automatisch erkennen könnte. Sie funktionieren nur dort, wo Betreiber ihre Verarbeitung eingerichtet haben. Darüber hinaus kann derselbe Alias je nach Konfiguration im Trace- oder Flood-Modus verarbeitet werden.

### Aliase für Veranstaltungen, Übungen und Aktivitäten

Dasselbe Verfahren lässt sich für Rallyes, Kommunikationsübungen, Amateurfunkveranstaltungen oder zeitweilige Feldnetze nutzen. Nehmen wir an, mehrere abgestimmte Digipeater bearbeiten den Basisalias `RAJD` ohne Wegverfolgung:

```text
SQ9MDD-9>APRS,RAJD2-2:...
SQ9MDD-9>APRS,RAJD2-1:...
SQ9MDD-9>APRS,RAJD2*:...
```

Dies ist **ein Entwurfsbeispiel**, kein tatsächlich vorhandener und allgemein unterstützter APRS-Alias. Nach der Veranstaltung können die Betreiber `RAJD` abschalten, ohne die reguläre Verarbeitung von `WIDEn-N` zu beeinflussen. Muss der Weg einer Nachricht nachvollziehbar sein, kann derselbe abgestimmte Alias stattdessen im Trace-Modus betrieben werden.

Ein historisches Beispiel für eine ähnliche Anwendung ist `TEMPn-N`, das im New-N Paradigm für zeitweilige Digipeater beschrieben wird, die unter anderem beim Field Day und in Notfällen eingesetzt werden. Das bedeutet jedoch nicht, dass der Alias `TEMP` bei jedem APRS-Gerät ab Werk aktiviert ist.

Bei der Einrichtung eines Alias müssen mindestens sein Name, die beteiligten Stationen, die Art der Wegverfolgung, die zugelassenen Zählerwerte, die Duplikatfilterung und die Betriebsdauer abgestimmt werden. Namenskonflikte mit dem örtlichen Netz und den Standardregeln der verwendeten Software sind ebenfalls zu vermeiden.

**Die Trennung durch einen Alias ist logisch, nicht funktechnisch.** Auf derselben Frequenz belegt jede zusätzliche Weiterleitung weiterhin den gemeinsamen Kanal. Ein Digipeater mit mehreren eingerichteten Aliasen kann gemäß seinen übrigen Regeln auch andere Rahmen weiterleiten. Ein örtlicher Alias gewährleistet für sich genommen weder eine Trennung des Verkehrs noch Vertraulichkeit.

## 9. Satelliten-Aliase

Ein Digipeater auf einem Satelliten oder an Bord der Internationalen Raumstation kann ebenfalls über das AX.25-Pfadfeld adressiert werden. Dabei sind **einfache Aliase und konkrete Stationsrufzeichen** besonders wichtig, nicht komplexe terrestrische `WIDEn-N`-Pfade.

| Adresse im Pfad | Bedeutung |
| --- | --- |
| `ARISS` | Gemeinsamer Alias, der von der ISS und einigen weiteren Satelliten abhängig von der aktuellen Konfiguration unterstützt wird. |
| `APRSAT` | Historischer gemeinsamer Alias aus der APRS-Dokumentation; eine heutige Unterstützung auf jedem Satelliten darf nicht vorausgesetzt werden. |
| `RS0ISS`, `NA1SS` | Rufzeichen der Station auf der ISS; ob sie als Digi-Adressen verwendet werden können, hängt von der aktiven Ausrüstung und Konfiguration ab. |
| Rufzeichen eines bestimmten Satelliten | In der Dokumentation des jeweiligen Relais angegebene Adresse, etwa `W3ADO-1` oder `PCSAT-1` für NO-44. |

Beispiel für die Nutzung eines gemeinsamen Alias auf einem Satelliten, der ihn unterstützt:

```text
SQ9MDD-7>APRS,ARISS:...
```

`ARISS` ist hier eine einzelne einfache Adresse. Ohne konkrete Begründung sollte kein terrestrischer Pfad `WIDE1-1,WIDE2-1` angehängt werden. Nach der satellitengestützten Weiterleitung können viele Bodenstationen und Satelliten-Gateways den Rahmen empfangen. Dies ändert jedoch nicht die Bedeutung des Alias selbst.

Historische APRS-Unterlagen beschrieben die gemeinsamen Aliase `ARISS`, `APRSAT` und `WIDE` sowie Versuche mit komplexeren satellitengestützten Weiterleitungen. Das sind keine allgemeingültigen heutigen Einstellungen. Die AMSAT-Übersicht vom **7. September 2026** nannte für die ISS unter anderem `RS0ISS`, `NA1SS` und `ARISS` sowie abweichende Adressen für weitere Satelliten. Vor einer Aussendung sind der aktuelle Betriebszustand des jeweiligen Satelliten, seine unterstützte Adresse, die Frequenz und die Modulationsart zu prüfen. Allein ein Eintrag in der Übersicht garantiert nicht, dass der Dienst während eines bestimmten Überflugs verfügbar ist.

## 10. Begrenzung von Duplikaten und übermäßigen Weiterleitungen

Der Zähler `N` begrenzt die Länge eines einzelnen Pfadzweigs, nicht die Gesamtzahl der Kopien im Netz. Wenn `SR5AAA` und `SR5BBB` einen `WIDE2-2`-Rahmen beide direkt hören, können beide den ersten Hop ausführen. Anschließend können verschiedene benachbarte Digipeater die erhaltenen Kopien ein zweites Mal weiterleiten. Zwei angeforderte Hops bedeuten deshalb nicht lediglich zwei HF-Aussendungen.

Korrekt konfigurierte Digipeater sollten kürzlich weitergeleitete Duplikate erkennen, üblicherweise anhand von Quelle, Zieladresse und Informationsfeld, unabhängig von Änderungen des Pfades. Der genaue Algorithmus, die Aufbewahrungszeit und Ausnahmeregeln hängen von der Implementierung ab. Die Duplikatunterdrückung verhindert jedoch nicht jede Kollision: Zwei Stationen, die gleichzeitig die erste Kopie empfangen, können unabhängig voneinander beschließen zu senden.

Wichtig sind außerdem:

- Die Begrenzung unterstützter Werte von `n` und `N`, einschließlich Zurückweisen oder Kürzen übermäßig langer Pfade (*trapping*).
- Die Prüfung, ob ein Zähler sinnvoll ist, beispielsweise das Unterbinden von `WIDE1-7` innerhalb der `WIDEn-N`-Regeln.
- Das Vermeiden einer erneuten Weiterleitung durch einen Digipeater, dessen Rufzeichen bereits im verwendeten Teil des Pfades steht.
- Die Kontrolle der Aussendehäufigkeit eigener Baken und unnötiger Weiterleitungen auf dem gemeinsamen Kanal.

Ein zu hoher Zähler bei einem regionalen Alias kann das Netz auch innerhalb dieser Region überlasten. Bei der Wahl eines kleineren oder größeren Werts sind die tatsächliche Topologie und örtliche Absprachen maßgeblich, nicht nur die angegebene Reichweite des Senders.

## 11. Preemptive Digipeating

Normalerweise verarbeitet ein Digipeater ausschließlich das erste noch nicht verwendete Pfadelement. Manche Implementierungen bieten **Preemptive Digipeating**. Damit können sie ihr eigenes Rufzeichen oder einen besonderen Alias an einer späteren Stelle des Pfades erkennen und die davorliegenden Elemente entsprechend verändern.

Dieses Verfahren kann in gezielt geplanten Spezialnetzen nützlich sein, wenn ein Rahmen eine Station weiter hinten auf der vorgesehenen Route direkt erreicht. Ein solches Verhalten darf nicht bei jedem APRS-Digipeater vorausgesetzt werden. Das Ergebnis hängt von der jeweiligen Implementierung und dem gewählten Modus zum Überspringen früherer Positionen ab. Alle normalen Beispiele dieses Artikels gehen von der Verarbeitung ohne Preemption aus.

## 12. `RFONLY`, `NOGATE` und der Übergang zu APRS-IS

Am Ende eines Pfades können folgende Kennzeichen stehen:

```text
SQ9MDD-9>APRS,WIDE2-1,RFONLY:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1,NOGATE:...
```

`RFONLY` und `NOGATE` sind **für Gateways vorgesehene Kennzeichen**, keine zusätzlichen Anforderungen für Weiterleitungen. Sie erhöhen die Hop-Anzahl nicht. Ihre Anwesenheit im Pfadfeld hebt die AX.25-Beschränkungen für Anzahl und Länge der Adressen nicht auf.

Die APRS-IS-Spezifikation nennt sie als Gründe, einen Rahmen nicht von HF ins Internet weiterzuleiten. **Die Auswertung beider Kennzeichen durch ein IGate ist jedoch optional**. Daher kann nicht garantiert werden, dass jedes Gateway ein solches Paket blockiert. Die Kennzeichen gewährleisten auch keine Vertraulichkeit der Funkübertragung.

Nach einer ordnungsgemäßen Übergabe an APRS-IS ergänzt das Gateway einen passenden *q-construct*, beispielsweise `qAR` mit seinem eigenen Rufzeichen oder `qAO` bei einem reinen Empfangsgateway. Dies sind Bestandteile des Internet-Headers und **nicht des AX.25-Funkpfades**. Sie sollten nicht in einem unmittelbar von einem APRS-Sender ausgestrahlten Rahmen auftauchen. Einzelheiten gehören zum gesonderten Thema IGate-Betrieb.

## 13. Interpretation beispielhafter Rahmen

| Schreibweise | Was sich daraus ableiten lässt |
| --- | --- |
| `SQ9MDD-9>APRS:...` | Der Absender hat keine Weiterleitung durch Digipeater angefordert. Ein direkter Empfang durch ein IGate bleibt möglich. |
| `SQ9MDD-9>APRS,WIDE2-1:...` | Ein weiterer Hop über eine Station, die die Familie `WIDE2` unterstützt, wird angefordert. |
| `SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...` | `SR5AAA` ist die zuletzt verwendete Adresse, und das Element `WIDE2-1` bleibt aktiv. |
| `SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...` | Beide genannten Adressen sind verwendet; das Sternchen steht nur hinter der letzten. |
| `SQ9MDD-9>APRS,SP2-1:...` | Für den Alias `SP` bleibt ein Hop übrig; bei Flood lässt sich das Rufzeichen des vorherigen Digis jedoch nicht ablesen. |
| `SQ9MDD-9>APRS,SP2*:...` | Das Element `SP2` ist aufgebraucht; daraus folgt nicht, wie viele Kopien andere Stationen empfangen haben. |
| `SQ9MDD-9>APRS,ARISS:...` | Der Absender hat den einfachen Alias `ARISS` angegeben; seine Unterstützung hängt von der aktuellen Konfiguration des empfangenden Satelliten ab. |
| `SQ9MDD-9>APRS,WIDE2-1,NOGATE:...` | Ein Hop wird angefordert, verbunden mit der Kennzeichnung, den Rahmen möglichst nicht an APRS-IS weiterzugeben. |

Bei der Paketanalyse sollten drei Fragen getrennt betrachtet werden: **Was hat der Absender in den Pfad eingetragen?**, **wie hat der empfangende Digipeater ihn tatsächlich verändert?** und **was wurde später durch die APRS-IS-Infrastruktur ergänzt?** Andernfalls wird ein nicht nachverfolgbarer Flood-Pfad leicht mit direktem Empfang verwechselt oder werden mehrere Kopien einer Nachricht fälschlich als aufeinanderfolgende Hops desselben Zweigs interpretiert.

## Dokumentation und Quellen

- [Bob Bruninga, *Fixing the APRS Network: The New n-N Paradigm*](https://www.aprs.org/fix14439.html) - Geschichte und Regeln für `WIDEn-N`, `SSn-N`, `UITRACE`, `UIFLOOD` und `TEMPn-N` sowie die ursprünglichen Empfehlungen für Mobil-, Fest- und Fill-in-Stationen.
- [Bob Bruninga, *MD/VA Digipeater Plan*](https://www.aprs.org/digis/digis-md.html) - detaillierte historische Unterscheidung der empfohlenen Pfade für Mobilstationen mit Fill-in-Bedarf und für Feststationen.
- [aprs.fi, *How APRS paths work*](https://blog.aprs.fi/2020/02/how-aprs-paths-work.html) - Interpretation von AX.25-Pfaden und Praxisbeispiele zu `WIDEn-N` und Fill-in.
- [APRX, *Viscous Digipeater*](https://github.com/PhirePhly/aprx/blob/master/ViscousDigipeater.README) - Verzögerung von Rahmen, Beobachtung von Duplikaten und Abbruch von Fill-in-Aussendungen.
- [APRX, *aprx(8)*](https://manpages.debian.org/testing/aprx/aprx.8.en.html) - die Modi `directonly` und `viscous-delay` sowie deren Parameter.
- [Argent Data Systems, *Digipeater Setup*](https://argentdata.com/support/digipeater_setup/) - Aliasverarbeitung, Duplikatunterdrückung und Preemptive Digipeating am Beispiel einer konkreten Implementierung.
- [APRS-IS, *IGate Details*](https://www.aprs-is.net/IGateDetails.aspx) - Gateway-Regeln, `NOGATE`, `RFONLY` und q-constructs.
- [AMSAT, *Live Digipeater Satellites*](https://www.amsat.org/live-digipeater-satellites/) - Adressen und Parameter satellitengestützter Digipeater; Betriebsdaten sind vor der Verwendung erneut zu prüfen.
- [APRS-AX.25](https://wiki.sral.fi/wiki/APRS-AX.25.en) - Adressfelder und H-Bits in APRS-Funkrahmen.
