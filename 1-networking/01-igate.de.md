---
title: "IGate und Datenaustausch mit APRS-IS"
description: Rolle des IGate, Weiterleitungsrichtungen, q constructs und Mechanismen der APRS-IS-Datenzustellung unabhängig von Filtern.
---

Ein IGate (Internet Gateway) verbindet das APRS-Funknetz mit APRS-IS. Es empfängt Pakete über Funk und leitet sie an das Internetnetz weiter. Ein bidirektionales IGate kann außerdem ausgewählte Pakete aus APRS-IS über RF aussenden. Es ist jedoch keine transparente Brücke: Für beide Richtungen gelten unterschiedliche Weiterleitungsregeln, und auf der Internetseite spielen die APRS-IS-Server eine wichtige Rolle.

## Von RF zu APRS-IS

Die Hauptaufgabe eines IGate besteht darin, über Funk empfangene Pakete in APRS-IS verfügbar zu machen. Dazu gehören unter anderem Positionen, Nachrichten, Objekte, Telemetrie und Wetterdaten. Das IGate leitet gültige Pakete unter Beibehaltung ihres Inhalts und Funkpfads weiter. Dabei gelten Regeln, die verhindern, dass dieselben Daten erneut ins Netz eingespeist werden.

Gemäß den IGate-Regeln dürfen unter anderem folgende Pakete nicht an APRS-IS weitergeleitet werden:

- AX.25-Frames ohne die korrekten UI-Steuerungs- (`0x03`) und PID-Felder (`0xF0`);
- im Modus `PASSALL` empfangene Frames, bei denen die Datenintegrität nicht gewährleistet ist;
- allgemeine APRS-Abfragen, die mit `?` beginnen;
- Pakete mit `TCPIP` oder `TCPXX` im Pfad sowie gemäß den geltenden Regeln `NOGATE` oder `RFONLY`;
- Third-Party-Pakete mit `TCPIP` oder `TCPXX` im inneren Header.

Bei einem Third-Party-Paket ohne diese Kennzeichnungen müssen der äußere Funk-Header und die Third-Party-Markierung vor der Weiterleitung an APRS-IS entsprechend entfernt werden.

Ein IGate darf den Paketpfad nicht beliebig umschreiben. Den Übergang des Pakets von RF zu APRS-IS kennzeichnet es an der dafür vorgesehenen Stelle.

## q construct: Herkunft eines Pakets erkennen

Ein `q construct` ist ein Header-Mechanismus, der **ausschließlich in APRS-IS** verwendet wird. Er zeigt, wie ein Paket ins Netz gelangt ist, kennzeichnet den Einspeisepunkt und unterstützt die Erkennung von Schleifen. Er gehört nicht zum AX.25-Funkpfad und darf niemals über RF ausgesendet werden.

Beispiel eines über RF empfangenen Pakets:

```text
SQ9ABC>APRS,WIDE1-1:!5000.00N/01900.00E-
```

Nach der Weiterleitung durch das bidirektionale IGate `SQ9MDD-4` kann sein APRS-IS-Header so aussehen:

```text
SQ9ABC>APRS,WIDE1-1,qAR,SQ9MDD-4:!5000.00N/01900.00E-
```

`qAR` kennzeichnet ein von RF weitergeleitetes Paket eines IGate, das Nachrichten an diese Station weiterleiten kann. `SQ9MDD-4` identifiziert den Einspeisepunkt. Diese Kennzeichnung allein beweist jedoch nicht, dass eine Nachricht tatsächlich über RF ausgesendet wird: Das hängt auch von den Weiterleitungsregeln des IGate und der Netzsituation ab.

Die wichtigsten in APRS-IS vorkommenden Konstruktionen sind:

| Konstruktion | Bedeutung |
| --- | --- |
| `qAR` | Von RF weitergeleitetes Paket eines IGate, das die Möglichkeit zur Nachrichtenweiterleitung an diese Station angibt. |
| `qAO` | Von RF weitergeleitetes Paket ohne Möglichkeit zur Nachrichtenweiterleitung an diese Station, insbesondere durch ein reines Empfangs-IGate. |
| `qAC` | Direkt von einem Client mit erfolgreich verifizierter Anmeldung stammendes, vom Server gekennzeichnetes Paket. |
| `qAS` | Vom Server ohne vorhandenen q construct empfangenes oder vom Server erzeugtes Paket. |
| `qAU` | Direkt über UDP empfangenes Paket. |
| `qAI` | Konstruktion zur Verfolgung des Paketwegs durch APRS-IS-Server. |

Die Dokumentation beschreibt außerdem Konstruktionen älterer Weiterleitungsverfahren, darunter `qAr` und `qAo`, sowie veraltete Mechanismen für nicht verifizierte Anmeldungen. Bei q constructs ist die Groß- und Kleinschreibung relevant.

`qAR` ist kein Beweis dafür, dass ein IGate über einen tatsächlich funktionsfähigen Sender verfügt. Ebenso bedeutet `qAO` nicht ausschließlich, dass ein Sender fehlt. Auch ein bidirektionales IGate kann `qAO` für eine Station verwenden, an die es keine Nachrichten weiterleitet.

## Von APRS-IS zu RF

Die Weiterleitung aus dem Internet auf den Funkkanal erfolgt wesentlich selektiver. Der gesamte APRS-IS-Datenstrom darf nicht erneut ausgesendet werden, da dies den lokalen Funkkanal schnell überlasten würde.

Die Hauptanwendung eines bidirektionalen IGate ist die Zustellung von Nachrichten an Stationen innerhalb seines Funkversorgungsbereichs. Nach den grundlegenden IGate-Kriterien werden eine Nachricht und die zugehörigen Positionspakete weitergeleitet, wenn die entsprechenden Bedingungen erfüllt sind:

- der Empfänger wurde innerhalb eines konfigurierten Zeitfensters über RF gehört und liegt im Versorgungsbereich des IGate, der beispielsweise anhand der DIGI-Hopzahl oder der Entfernung festgelegt wird;
- der Absender der Nachricht wurde in letzter Zeit nicht lokal über RF gehört;
- das Paket des Absenders enthält keine Kennzeichnungen, die diese Weiterleitung verhindern, insbesondere `TCPXX`, `NOGATE` oder `RFONLY`;
- der Empfänger wurde in letzter Zeit nicht als direkt über das Internet erreichbare Station gesehen.

Die genauen Zeitfenster, der Funkversorgungsbereich und weitere Einschränkungen hängen von der IGate-Konfiguration ab. Der Betreiber kann außerdem eigene Kriterien für die Weiterleitung bestimmter anderer Pakete festlegen, beispielsweise ausgewählter Objekte. **Der Empfang eines Pakets aus APRS-IS bedeutet nicht automatisch, dass das IGate es über RF aussendet.**

## APRS-IS-Filter und automatisch zugestellter Datenverkehr

Ein IGate verbindet sich häufig mit dem gefilterten APRS-IS-Port `14580`. Ein serverseitiger Filter definiert einen **zusätzlichen Datenstrom**, den der Client empfangen möchte. Er ersetzt nicht die grundlegenden Servermechanismen zur Kommunikation mit den vom IGate versorgten Stationen.

Beispielfilter:

```text
filter m/10
```

`m/10` legt einen Radius von 10 km um die letzte bekannte Position des Rufzeichens fest, mit dem sich der Client bei APRS-IS angemeldet hat. Dies ist weder der Funkempfangsradius des IGate noch ein Bereich um alle gehörten Stationen und auch keine absolute Begrenzung der vom Server zugestellten Daten. Ist dem Server die Position des Anmelderufzeichens nicht bekannt, hat der Filter keinen festgelegten Bezugspunkt.

Ein Filter mit festem Mittelpunkt ist beispielsweise:

```text
filter r/50/19/50
```

Er umfasst Positionen und Objekte innerhalb von 50 km um 50°N, 19°E sowie Nachrichten an Stationen in diesem Gebiet. In beiden Fällen wirkt das zusätzliche Abonnement **neben** dem grundlegenden Serverdatenstrom, nicht an dessen Stelle.

### Was stellt der Server unabhängig vom zusätzlichen Filter zu?

Die APRS-IS-Filterdokumentation nennt drei wichtige Kategorien von Datenverkehr, die am gefilterten Port standardmäßig zugestellt werden:

- **APRS-Nachrichten** an den angemeldeten Client und an Stationen, deren Pakete dieser Client von RF zu APRS-IS weitergeleitet hat.
- **Zugehörige Positionen der Nachrichtenabsender**: der nächste verfügbare Positionsbericht der Station, die eine solche Nachricht gesendet hat. Das bedeutet nicht, dass automatisch ihr gesamter Positionsverlauf zugestellt wird.
- **`TCPIP`-Pakete von durch den Client weitergeleiteten Stationen**: Dieser Mechanismus erfordert keine vorherige Nachricht. Er kann dazu führen, dass weitere Pakete einer Station, deren Datenverkehr das IGate zuvor in APRS-IS eingespeist hat, auch außerhalb des Filterradius eintreffen.

Der letzte Punkt ist für die Auswertung realer Protokolle besonders wichtig. Nicht jedes außerhalb von `m/10` empfangene Paket ist eine Nachricht oder die Position ihres Absenders. Der Server kann auch Internetdatenverkehr von Stationen zustellen, die durch frühere RF-Weiterleitungen mit dem IGate verknüpft sind.

Einschließende Filter werden addiert: Ein Paket, das einer beliebigen aktiven Regel entspricht, kann zugestellt werden. Ausschließende Filter begrenzen zusätzliche Abonnements, deaktivieren aber nicht die standardmäßige Nachrichtenverarbeitung. Die Filterung betrifft den Datenstrom **vom Server zum Client**. Sie beschränkt nicht die Pakete, die das IGate an APRS-IS sendet.

## Wie beeinflusst Funkempfang über große Entfernungen den APRS-IS-Datenstrom?

Die Funkreichweite eines IGate ist veränderlich. Bei verbesserten Ausbreitungsbedingungen kann es eine Hunderte Kilometer entfernte Station direkt oder über Digipeater empfangen. In beiden Fällen kann das Paket ordnungsgemäß an APRS-IS weitergeleitet werden. Der Radius des Filters für die Internetverbindung begrenzt diesen Vorgang nicht.

Betrachten wir ein IGate mit dem Filter `m/10`:

1. Das IGate empfängt über RF ein Paket einer entfernten Station, beispielsweise durch troposphärische Ausbreitung oder über einen DIGI-Pfad, und leitet es an APRS-IS weiter.
2. Der Server berücksichtigt diese Station in seinen Mechanismen zur Verarbeitung des vom Client weitergeleiteten Datenverkehrs.
3. Sendet die Station anschließend weitere Pakete mit einem Internetpfad `TCPIP` direkt an APRS-IS, kann der Server sie diesem IGate unabhängig von `m/10` zustellen. Dafür ist keine APRS-Nachricht erforderlich.
4. Sendet eine andere Station eine Nachricht an die zuvor durch das IGate weitergeleitete Station, stellt der Server auch diese Nachricht und den nächsten verfügbaren Positionsbericht ihres Absenders zu. Auch dieser Absender kann weit außerhalb des Filterbereichs liegen.
5. Der Empfang dieser Daten aus APRS-IS bedeutet nicht, dass das IGate sie automatisch über RF erneut aussendet. Für IS-zu-RF gelten gesonderte Regeln.

Somit können ein kleiner geografischer Filter und zeitweise eintreffende Pakete wesentlich weiter entfernter Stationen gleichzeitig auftreten. Dabei wird der Radius von `m/10` nicht erweitert. Vielmehr arbeiten die grundlegenden APRS-IS-Serverfunktionen parallel. Besonders auffällig kann dies nach Phasen mit Funkempfang über große Entfernungen sein, wenn das IGate Pakete von Stationen weitergeleitet hat, die es normalerweise nicht hört.

### Zwei unterschiedliche Fälle, die leicht verwechselt werden

**Pakete einer zuvor durch das IGate weitergeleiteten Station.** Das IGate empfängt `SQ9ABC` über RF und leitet ihr Paket an APRS-IS weiter. Sendet `SQ9ABC` anschließend selbst ein Paket direkt als `TCPIP` an APRS-IS, kann dieses außerhalb des geografischen Filters im Datenstrom des IGate erscheinen. Ein Nachrichtenaustausch mit einer anderen Station ist dafür nicht erforderlich.

**Pakete im Zusammenhang mit einer Nachricht an eine zuvor weitergeleitete Station.** Das IGate empfängt `SQ9ABC` über RF. Die Station `EA1XYZ` sendet über APRS-IS eine an `SQ9ABC` adressierte Nachricht. Der Server stellt dem IGate die Nachricht und das nächste verfügbare Positionspaket von `EA1XYZ` zu, auch wenn der Nachrichtenabsender weit außerhalb des Filtergebiets liegt.

Der erste Mechanismus betrifft `TCPIP`-Pakete einer zuvor durch den Client weitergeleiteten Station. Der zweite betrifft Nachrichten an diese Station und die Positionen des **Nachrichtenabsenders**. Diese Unterscheidung erklärt, weshalb auch ohne vorherigen sichtbaren APRS-Nachrichtenaustausch Pakete außerhalb des Filters erscheinen können.

### Wie lassen sich solche Pakete in der Praxis einordnen?

Im APRS-IS-Datenstrom können gleichzeitig durch `m/10` ausgewählte Pakete, `TCPIP`-Pakete zuvor durch das IGate weitergeleiteter Stationen, Nachrichten mit zugehörigen Positionen und Daten aus anderen aktiven Filterregeln vorkommen. Diese Mechanismen arbeiten parallel.

Ein Header wie:

```text
SQ9ABC>APRS,TCPIP*,qAC,T2SERVER:!5000.00N/01900.00E-
```

zeigt, wie das Paket in APRS-IS gelangt ist. `qAC` allein **zeigt nicht an**, warum ein bestimmter Server es einem bestimmten Client zugestellt hat. Anhand eines einzelnen Protokolleintrags lässt sich auch nicht entscheiden, ob das Paket wegen des geografischen Filters, einer früheren Weiterleitung der Station durch das IGate oder einer anderen Regel eingetroffen ist.

Enthält das Protokoll neben entfernten Positionen auch Statusmeldungen, Telemetrie und Objekte, sollten diese nicht pauschal der Nachrichtenverarbeitung zugeschrieben werden. Darunter können sich `TCPIP`-Pakete zuvor durch das IGate weitergeleiteter Stationen oder Daten aus anderen aktiven Filtern befinden. Die Dokumentation bietet keine Grundlage für die Behauptung, dass das einmalige Hören einer Station bedingungslos dazu führt, dass für eine festgelegte Zeit der gesamte Datenverkehr aus ihrer Umgebung zugestellt wird.

Für Betreiber gilt der Grundsatz: **Der geografische Filter definiert zusätzlich angeforderten Datenverkehr, während der Server weiterhin die Stationen versorgt, deren Pakete das IGate in APRS-IS eingespeist hat**. Deshalb kann der Empfang von Paketen außerhalb von `m/10` völlig normales Netzverhalten sein.

## Third-Party-Format bei der Weiterleitung auf RF

Ein aus APRS-IS empfangenes Paket darf nicht mitsamt seinem Internetpfad und q construct über RF ausgesendet werden. Das IGate verwendet das Third-Party-Format: einen äußeren Funk-Frame, der das ursprüngliche Paket als Nutzdaten enthält.

Schema:

```text
IGATECALL>APRS,GATEPATH:}FROMCALL>TOCALL,TCPIP,IGATECALL*:originaldaten
```

Der innere Header `TCPIP,IGATECALL*` kennzeichnet die Herkunft des Pakets. Vor der Aussendung muss dessen APRS-IS-Pfad entfernt werden. Dadurch erkennt ein anderes IGate, das diese Funkaussendung empfängt, das aus dem Internet stammende Paket und speist es nicht erneut in APRS-IS ein.

Die Kennzeichnung `}` leitet das innere Third-Party-Paket ein. Sie darf nicht mit dem q construct verwechselt werden, das ausschließlich auf der Internetseite existiert.

## Dokumentation

- [APRS-IS: IGate Details](https://www.aprs-is.net/IGateDetails.aspx) - Kriterien der Paketweiterleitung und Third-Party-Format.
- [APRS-IS: q Construct](https://www.aprs-is.net/q.aspx) - Bedeutung und Verwendung der q constructs.
- [APRS-IS: Server Design](https://www.aprs-is.net/ServerDesign.aspx) - grundlegende Serverregeln einschließlich der verpflichtenden Nachrichtenverarbeitung.
- [APRS-IS: Server-side Filter Commands](https://www.aprs-is.net/javAPRSFilter.aspx) - Filterverhalten und unabhängig davon zugestellte Pakete.
