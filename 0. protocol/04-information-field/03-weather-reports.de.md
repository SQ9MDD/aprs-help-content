---
title: "APRS-Wetterberichte"
description: "WX-Berichte: Geschichte, Formate, Pflichtfelder, fehlende Messwerte, Messmethodik und Datenqualität bei CWOP/MADIS."
---

Mit APRS lassen sich aktuelle meteorologische Messwerte unmittelbar über das Funknetz sowie über APRS-IS übertragen. Ein WX-Bericht kann die Position der Station sowie Angaben zu Wind, Temperatur, Niederschlag, Luftfeuchtigkeit und Luftdruck enthalten. Das Protokoll sieht außerdem zusätzliche Parameter und Formate für ältere Geräte vor.

Wetterdaten besitzen keinen einzigen, ausschließlich ihnen zugeordneten Data Type Identifier (DTI). Sie können Teil eines gewöhnlichen Positionsberichts, eines APRS-Objekts oder eines separaten Berichts ohne Positionsangabe sein. Für ihre Interpretation sind die Kombination aus DTI, Berichtsstruktur und Stationssymbol maßgeblich.

## Entwicklung der WX-Berichte

APRS übertrug Wetterdaten lange vor dem Aufkommen moderner internetgestützter Wetterdienste für Funkamateure. Frühe Lösungen arbeiteten unter anderem mit Wetterstationen von Peet Bros ULTIMETER und Davis zusammen. Auch ein abgesetzter Betrieb war möglich: Eine Wetterstation, ein TNC und ein Funkgerät konnten Messwerte übertragen, ohne dass dauerhaft ein Computer laufen musste. Neben der lokalen Wetterbeobachtung diente dies auch dem Austausch von Meldungen in Beobachternetzen wie SKYWARN.

Die Entwicklung des Formats zeigt, wie APRS an die verfügbare Hardware und neue Anforderungen angepasst wurde:

- **1990er-Jahre** - APRS unterstützte Daten verschiedener Wetterstationsmodelle, darunter auch die Rohdatenformate der Hersteller. Das Dokument `WX.TXT` erwähnt eine Formatänderung in APRSdos 793 vom Juni 1997, die mit der älteren Art der Datendarstellung nicht rückwärtskompatibel war.
- **2000** - Die *APRS Protocol Reference 1.0.1* ordnete Wetterberichte in drei Formen ein: Rohberichte, Berichte ohne Position und vollständige Berichte mit Position und Messwerten.
- **2001 und spätere Klarstellungen zu APRS 1.1** - Präzisiert wurden unter anderem die Unterscheidung zwischen einem fehlenden Messwert und dem Wert null, die Bedeutung von Niederschlagszählern sowie die Mehrdeutigkeit des Feldes `s`, das je nach Berichtsvariante Windgeschwindigkeit oder Schneefall bedeuten kann.
- **Seit Juli 2001** - Daten aus dem CWOP-Netz, das aus dem Amateurfunkumfeld und APRSWXNET hervorging, fließen in das NOAA-System MADIS ein. Damit gelangten Meldungen privater Wetterstationen in einen breiteren meteorologischen Beobachtungsdatenbestand.
- **2006** - Die Nutzung von APRS zur Meldung von Wasserständen und Hochwassergefahren wurde beschrieben. Es erschienen Symbole für Pegelmessstellen sowie ein Vorschlag, zusätzliche Messungen innerhalb der Wetterdatenfelder zu übertragen.
- **März 2011** - Nach dem Reaktorunfall in Fukushima Daiichi schlug Bob Bruninga vor, das WX-Format um Strahlungsmesswerte zu erweitern. Ein Dokument vom 24. März 2011 beschreibt das Feld `Xxxx` sowie eine weitere Vereinheitlichung der Kennzeichnung von Sensoren und Gefahren.

Die Geschichte des Strahlungsfeldes verdeutlicht eine wichtige Eigenschaft von APRS: Das WX-Format wurde zunehmend auch als Träger für Umweltmessungen und nicht nur für klassische meteorologische Daten betrachtet. Dabei muss jedoch zwischen Feldern der ursprünglichen Spezifikation und späteren Vorschlägen unterschieden werden. Die Dokumentation einer Erweiterung bedeutet nicht, dass jede Anwendung sie implementiert.

## Arten von Wetterberichten

Die *APRS Protocol Reference 1.0.1* unterscheidet drei Formate:

| Format | Merkmale |
| --- | --- |
| **Complete Weather Report** | Wetterdaten und Position in einem Paket. Empfohlenes Format für neue Implementierungen. |
| **Positionless Weather Report** | Wetterdaten ohne Koordinaten. Der Empfänger muss die Stationsposition bereits aus einem separaten Paket kennen. |
| **Raw Weather Report** | Rohdaten im Format eines bestimmten Wettergeräts. Historische Lösung, für neue Sender nicht empfohlen. |

Wenn Position und aktuelle Messwerte in einem Paket zusammengefasst werden, sinkt die Abhängigkeit von vorherigen Übertragungen. Dies ist besonders auf einem Funkkanal wichtig, auf dem nicht der Empfang jedes einzelnen Frames garantiert ist.

## Vollständiger Wetterbericht

In seiner Grundform ist ein Complete Weather Report ein Positionsbericht mit einem Wettersymbol, auf das WX-Daten folgen. Dafür kann jeder der vier standardmäßigen Positions-DTI verwendet werden:

| DTI | Zeitstempel | Deklarierte Unterstützung von APRS-Nachrichten |
| --- | --- | --- |
| `!` | nein | nein |
| `=` | nein | ja |
| `/` | ja | nein |
| `@` | ja | ja |

### Welche Bestandteile sind im vollständigen Bericht vorgeschrieben?

Bei einem **unkomprimierten** Complete Weather Report mit Position sind die vom gewählten DTI vorgegebene Positionsstruktur, ein Wettersymbol, das sieben Zeichen lange Feld für Windrichtung und Windgeschwindigkeit `ddd/sss` sowie das Temperaturfeld `txxx` vorgeschrieben. Es geht also um die **Anwesenheit dieser Felder, nicht darum, dass die Station sämtliche Sensoren besitzen muss**. Liegt ein Messwert nicht vor, bleibt die Feldposition erhalten und wird mit Punkten oder Leerzeichen in der entsprechenden Anzahl aufgefüllt.

| Bestandteil | Im unkomprimierten vollständigen Bericht vorgeschrieben? | Wenn kein Messwert vorliegt |
| --- | --- | --- |
| DTI und gültige Position | Ja | Die Position kennzeichnet diese Berichtsvariante; sie darf nicht durch WX-Punkte ersetzt werden. |
| Wetterstationssymbol | Ja | Üblicherweise `/_` oder `\_`; beide Zeichen des Symbols sind relevant. |
| `ddd/sss` - Windrichtung und Windgeschwindigkeit | Ja | `.../...` oder jeweils drei Leerzeichen vor und nach dem Schrägstrich. |
| `txxx` - Temperatur | Ja | `t...` oder `t` gefolgt von drei Leerzeichen. |
| `gxxx` - Windböe | Nein, gemäß späteren Klarstellungen zum vollständigen Format | Kann entfallen oder als `g...` übertragen werden. |
| Niederschlag, Luftfeuchtigkeit, Luftdruck und weitere Felder | Nein | Das Feld weglassen oder seine Ziffern durch Punkte/Leerzeichen ersetzen. |

Die Unterscheidung bei Böen ist wichtig: In einem **Bericht ohne Positionsangabe** gehört `gxxx` zur vorgeschriebenen Anfangssequenz, während spätere Klarstellungen zum vollständigen Format vor allem `ddd/sss` und `txxx` voraussetzen. In den Tabellen und Beispielen von APRS101 erscheint `gxxx` ebenfalls häufig, sofern Böenmesswerte vorliegen.

Beispiel eines vollständigen Pakets im Monitorformat:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_220/004g005t068r000p015P012h72b10132
```

Der Teil nach dem Doppelpunkt ist das Information-Feld. Seine grundlegenden Bestandteile sind:

```text
! | 5003.50N | / | 01956.75E | _ | 220/004 | g005t068r000p015P012h72b10132
```

| Bestandteil | Bedeutung |
| --- | --- |
| `!` | DTI eines Positionsberichts ohne Zeitstempel. |
| `5003.50N` | Geografische Breite. |
| `/` | Kennung der primären Symboltabelle. |
| `01956.75E` | Geografische Länge. |
| `_` | Symbolcode der Wetterstation. |
| `220/004` | Windrichtung und Windgeschwindigkeit. |
| `g005...` | Übrige meteorologische Daten. |

Die Zeichen `/` und `_` innerhalb des Positionsteils ergeben gemeinsam das Symbol `/_`. Der Symbolcode `_` darf nicht mit dem DTI `_` verwechselt werden, der am Anfang eines eigenständigen Berichts ohne Positionsangabe steht.

### Messfelder und Einheiten

Das klassische WX-Format verwendet kurze Felder mit fester Länge. In einem vollständigen Bericht werden Windrichtung und Windgeschwindigkeit ohne Buchstabenpräfixe in der sieben Byte langen Erweiterung `ddd/sss` übertragen.

| Feld | Messgröße | Einheit und Kodierung |
| --- | --- | --- |
| `ddd/sss` | Windrichtung und mittlere Windgeschwindigkeit | Grad und mph; über 1 Minute gemittelte Geschwindigkeit. **Pflichtfeld.** |
| `gxxx` | Windböe | mph; höchste Geschwindigkeit innerhalb der letzten 5 Minuten. Im vollständigen Bericht optional. |
| `txxx` | Temperatur | °F; negative Werte sind möglich, z. B. `t-07`. **Pflichtfeld.** |
| `rxxx` | Niederschlag der letzten Stunde | Hundertstel Zoll. |
| `pxxx` | Niederschlag der letzten 24 Stunden | Hundertstel Zoll; gleitendes 24-Stunden-Zeitfenster. |
| `Pxxx` | Niederschlag seit Mitternacht | Hundertstel Zoll. |
| `hxx` | Relative Luftfeuchtigkeit | Prozent; `h00` bedeutet 100 %. |
| `bxxxxx` | Luftdruck | Zehntel hPa (mbar). |

Die Werte werden unabhängig von den Anzeigeeinheiten der jeweiligen Anwendung in den vom Protokoll vorgeschriebenen Einheiten übertragen. Der Empfänger kann Temperaturen in °C, Windgeschwindigkeiten in km/h und Niederschläge in Millimetern anzeigen, die Kodierung des Berichts ändert sich dadurch aber nicht.

Im gezeigten Beispiel:

| Messgröße | Aus dem Bericht dekodierter Wert |
| --- | --- |
| Wind | 220°, 4 mph |
| Böe | 5 mph |
| Temperatur | 68°F (20°C) |
| Niederschlag der letzten Stunde | 0 |
| Niederschlag der letzten 24 Stunden | 0,15 Zoll |
| Niederschlag seit Mitternacht | 0,12 Zoll |
| Luftfeuchtigkeit | 72 % |
| Luftdruck | 1013,2 hPa |

Die Felder `r`, `p` und `P` beschreiben unterschiedliche Zeiträume. Insbesondere bezeichnet `p` nicht den Niederschlag des vorherigen Kalendertages und `P` ist kein Ersatz für `p`. Die Ermittlung dieser Werte sowie die Unterschiede zwischen Windmessungen nach APRS und CWOP werden im Abschnitt über die Messmethodik erläutert.

### Fehlende Messwerte: Punkte, Leerzeichen und weggelassene Felder

APRS unterscheidet einen **tatsächlichen Nullwert** von einem **nicht verfügbaren Messwert**. Ist ein definiertes Feld im Bericht vorhanden, aber der entsprechende Sensor fehlt oder liefert vorübergehend keinen Wert, können die Ziffern durch Punkte (`.`) oder Leerzeichen ersetzt werden. Die Feldlänge bleibt dabei unverändert. Spätere Klarstellungen des Protokollautors bevorzugen Punkte, weil sie leichter zu lesen sind.

| Situation | Beispiel | Interpretation |
| --- | --- | --- |
| Kein Windmesser vorhanden | `.../...` | Windrichtung und Windgeschwindigkeit sind unbekannt. |
| Richtung unbekannt, Geschwindigkeit bekannt | `.../004` | Die Geschwindigkeit beträgt 4 mph, die Richtung ist unbekannt. |
| Kein Thermometer vorhanden | `t...` | Temperatur unbekannt. |
| Keine Böenmessung, Feld aber vorhanden | `g...` | Böen unbekannt; das bedeutet nicht 0 mph. |
| Kein Luftdruckwert, Feld aber vorhanden | `b.....` | Luftdruck unbekannt. |
| Kein Niederschlagsmesswert, Feld aber vorhanden | `r...` | Keine Daten zum Niederschlag der letzten Stunde. |
| Gemessen wurde kein Niederschlag | `r000` | Der Messwert beträgt genau 0,00 Zoll. |

**Leere optionale Felder müssen nicht gesendet werden.** Sowohl `r...` als auch das Weglassen von `r` zeigen an, dass kein Messwert verfügbar ist, während `r000` ein konkretes Messergebnis darstellt. Pflichtfelder dürfen dagegen nicht entfernt werden, nur weil der zugehörige Sensor fehlt.

Beispiel eines minimalen unkomprimierten Berichts einer Station mit ausschließlich einem Regenmesser:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_.../...t...r012
```

Nach dem Symbol `_` stehen die Pflichtfelder `.../...` und `t...`. Das optionale Feld `g` entfällt, da die Station keine Böen misst. Der einzige verfügbare Messwert ist `r012`: Innerhalb der letzten Stunde fielen 0,12 Zoll Regen. Das ist zulässig: **Das Format verlangt die Anwesenheit bestimmter Felder, aber keine Ausstattung mit jedem denkbaren Sensor**.

Dagegen ist die folgende Schreibweise:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_r012
```

nicht gleichwertig. Sie lässt bei dieser Art unkomprimiertem Bericht vorgeschriebene Felder weg und sollte nicht als gültiger Complete Weather Report erzeugt werden.

Nach den Pflichtelementen müssen nicht alle weiteren Parameter vorhanden sein oder immer in derselben Reihenfolge folgen. Ein Parser sollte sie anhand ihrer Kennungen und festen Feldlängen erkennen, anstatt eine vollständige Abfolge aller möglichen Messwerte vorauszusetzen.

## Position, Zeit und Objekte

Ein vollständiger Bericht kann eine unkomprimierte oder eine komprimierte Position enthalten. Bei der unkomprimierten Variante steht die Erweiterung `ddd/sss` unmittelbar hinter dem Wettersymbol. Bei der komprimierten Variante werden die Winddaten in den dafür vorgesehenen Feldern der komprimierten Position übertragen; deshalb darf die sieben Byte lange Erweiterung `ddd/sss` nicht nochmals angehängt werden. Die Beispiele dieses Artikels, in denen Windwerte durch Punkte ersetzt werden, beziehen sich auf das unkomprimierte Format und nicht auf die Komprimierungsbytes.

Ein Bericht mit Zeitstempel kann folgendermaßen aussehen:

```text
SQ9MDD>APRS:@282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

DTI `@` bezeichnet eine Position mit Zeitstempel und deklarierter Unterstützung von APRS-Nachrichten. `282000z` bedeutet den 28. Tag des Monats um 20:00 UTC.

Messwerte können auch einem APRS-Objekt zugeordnet werden, beispielsweise wenn eine Station Daten eines entfernten Sensors veröffentlicht:

```text
SQ9MDD>APRS:;WX-KRAKOW*282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

In diesem Fall beginnt der Bericht mit DTI `;`; der Name `WX-KRAKOW` kennzeichnet das Objekt. Die Koordinaten und Wetterdaten beziehen sich auf dieses Objekt und nicht zwangsläufig auf den Standort der sendenden Station.

## Wetterbericht ohne Positionsangabe

Ein Positionless Weather Report beginnt mit DTI `_`. Danach folgen ein achtstelliger Zeitstempel im Format `MMDDHHMM` und die Messfelder. Windrichtung und Windgeschwindigkeit werden hier durch die Buchstaben `c` und `s` gekennzeichnet und nicht als `ddd/sss` übertragen.

Beispiel aus der APRS Protocol Reference:

```text
_10090556c220s004g005t077r000p000P000h50b09900wRSW
```

| Abschnitt | Bedeutung |
| --- | --- |
| `_` | DTI des Wetterberichts ohne Position. |
| `10090556` | 9. Oktober, 05:56 Uhr. |
| `c220s004` | Wind aus 220°, 4 mph. |
| `g005t077` | Böe 5 mph, Temperatur 77°F. |
| `r000p000P000` | Drei voneinander unabhängige Niederschlagsmessungen. |
| `h50b09900` | Luftfeuchtigkeit 50 %, Luftdruck 990,0 hPa. |
| `wRSW` | Historische Kennung der Wettersoftware und des Wettergeräts. |

In dieser Variante ist die **folgende Anfangssequenz vorgeschrieben**: `_` + achtstelliger Zeitstempel `MMDDHHMM` + `cxxx` + `sxxx` + `gxxx` + `txxx`. Die Reihenfolge dieser Felder muss eingehalten werden. Weitere Parameter können danach in wechselnder Reihenfolge folgen oder vollständig entfallen. `s` steht hier für die Windgeschwindigkeit und nicht für Schneefall.

Beispiel einer Station mit ausschließlich einem Regenmesser, entsprechend der in APRS101 dokumentierten Struktur:

```text
_10090556c...s...g...t...P012
```

Die Felder `c`, `s`, `g` und `t` **müssen auch dann vorhanden sein**, wenn keiner dieser Messwerte vorliegt. `P012` bedeutet 0,12 Zoll Niederschlag seit Mitternacht. Würde die Windrichtung tatsächlich 0° betragen, müsste `c000` statt `c...` verwendet werden.

Das Paket enthält keine Koordinaten. Damit eine Wetterstation auf der Karte dargestellt werden kann, muss dem Empfänger ihr Standort aus einem zuvor empfangenen Positionsbericht bekannt sein. Aus diesem Grund bevorzugen spätere APRS-Empfehlungen vollständige Berichte, die Position und aktuelle Messwerte gemeinsam übertragen.

## Historische Rohwetterberichte

Ältere Wetterstationen konnten Messwerte ohne Umwandlung in das allgemeine WX-Format in ihrem eigenen Geräteformat senden. APRS101 führt folgende Kennungen auf:

| DTI | Historisches Geräteformat |
| --- | --- |
| `!` | Ultimeter 2000 |
| `#` | Peet Bros U-II |
| `$` | Ultimeter 2000 |
| `*` | Peet Bros U-II |

Beispiel eines rohen Peet-Bros-U-II-Berichts aus der Dokumentation:

```text
#50B7500820082
```

Einige dieser Kennungen überschneiden sich mit DTI anderer APRS-Datentypen. Für eine korrekte Erkennung muss deshalb auch die nachfolgende Syntax geprüft werden. Bei der Entwicklung eines neuen Senders sollten die Gerätedaten in das vollständige WX-Format umgewandelt werden, anstatt das Rohformat des Herstellers zu senden.

## Zusätzliche Wetterfelder

Neben den grundlegenden Messgrößen sieht die Dokumentation weitere Felder vor. Nicht alle Geräte und Anwendungen unterstützen sie.

| Feld | Bedeutung | Hinweise |
| --- | --- | --- |
| `Lxxx` | Solare Bestrahlungsstärke | 0-999 W/m². |
| `lxxx` | Solare Bestrahlungsstärke | Ab 1000 W/m²; zur dreistelligen Zahl werden 1000 addiert. |
| `sxxx` | Schneefall der letzten 24 Stunden | Zoll; im vollständigen Bericht kollidiert `s` nicht mit der Position des Windgeschwindigkeitsfeldes. |
| `#xxx` | Rohzähler des Regenmessers | Keine einheitliche Einheit; die Interpretation hängt vom Gerät ab. |

Beispielsweise bedeutet `L700` 700 W/m², während `l123` für 1123 W/m² steht. In einem Bericht ohne Positionsangabe ist die Kennung `s` bereits für die Windgeschwindigkeit belegt. Daher darf dort kein Schneefallfeld hinzugefügt werden, das zu einer solchen Kollision führt.

### Pegelmessstellen: Erweiterung von 2006

Im Juni 2006 wurde die Nutzung von APRS zur Übermittlung von Wasserständen und zur Anzeige von Hochwasser beschrieben. Die Symbole `/w` (Pegelstation) und `\w` (Hochwasser) wurden eingeführt. Zudem wurde vorgeschlagen, Wasserstandsmessungen in herkömmliche Wetterdaten aufzunehmen.

Ein Beispiel für das historisch von Pegelmessstellen im FIRENET-Netz verwendete Format ist dieses APRS-Objekt:

```text
;09428508 *061713z3401.40N/11424.75Ww3.57gh/82cfs
```

Dabei beschreibt `3.57gh` den gemessenen Pegelstand und `82cfs` den Durchfluss in Kubikfuß pro Sekunde. Es handelt sich um eine **textuelle Objektbeschreibung**, nicht um das Feld `Fxxxx` eines herkömmlichen WX-Berichts. Laut der Aktualisierung vom März 2011 wurde damals dieses Format tatsächlich verwendet, obwohl bereits zuvor Erweiterungen des Wetterformats vorgeschlagen worden waren.

Die vorgeschlagenen zusätzlichen Messwerte innerhalb von WX umfassten folgende Felder:

| Feld | Bedeutung laut Erweiterungsdokumentation |
| --- | --- |
| `Fxxxx` | Wasserstand relativ zu einem Bezugsniveau in Zehntelfuß; positive und negative Werte sind möglich. |
| `Vxxx` | Versorgungsspannung in Zehntelvolt, z. B. `V128` = 12,8 V. |
| `Zxx` | Gerätetypkennung, die in der erweiterten Sensorbeschreibung vorgesehen ist. |

Für eine Wetterstation mit Pegelmesser wurde vorgeschlagen, die herkömmliche WX-Struktur beizubehalten, beispielsweise `.../...t...V128F+123` (in diesem Beispiel 12,3 Fuß oberhalb des Bezugsniveaus), statt ein völlig neues Berichtsformat einzuführen. Die Beschreibungen von 2011 unterscheiden zwischen Pegelmessstellen, die eigene Textdaten senden, und Wetterstationen, die erweiterte Messfelder übertragen. Das Symbol einer Pegelmessstelle allein garantiert nicht, dass WX-Felder vorhanden sind.

### Fukushima und der Vorschlag zur Strahlungsmessung von 2011

Nach dem Unfall im Kernkraftwerk Fukushima Daiichi im März 2011 entstand auch der Bedarf, Strahlungsmesswerte zu übertragen. Bob Bruninga nahm in *APRS 1.2.1 Weather Updates to the Spec* vom 24. März 2011 ausdrücklich auf die Ereignisse in Japan Bezug. Die vorgeschlagene Lösung nutzte das bestehende Wetterberichtsformat, anstatt ein vollständig neues Übertragungsverfahren zu entwickeln.

Das neue Feld `Xxxx` sollte die Strahlungsdosisleistung in Nanosievert pro Stunde (`nSv/h`) kodieren. Auf den Buchstaben `X` sollten drei Ziffern folgen: zwei signifikante Ziffern und ein Zehnerexponent. Beispiele:

| Feld | Interpretation | Ergebnis |
| --- | --- | --- |
| `X123` | 12 × 10³ nSv/h | 12 µSv/h |
| `X456` | 45 × 10⁶ nSv/h | 45 mSv/h |

Parallel dazu wurde vorgeschlagen, Sensortypen oder Gefahren mittels Overlays auf vorhandenen Symbolen zu kennzeichnen: das normale Wettersymbol für Hintergrundmessungen, das Overlay `R` für eine Station zur Strahlungsüberwachung und ein entsprechendes Overlay auf dem Gefahrensymbol beim Überschreiten eines festgelegten Grenzwerts. So ließe sich der vorhandene Mechanismus zur Kartendarstellung von Sensoren weiterverwenden und zwischen einer Messung und einer angezeigten Gefahr unterscheiden.

**Der Status dieser Lösung ist wichtig:** Das Dokument vom März 2011 beschreibt `Xxxx` als *vorgeschlagene* Erweiterung. Die Unterstützung dieses Feldes und der vorgeschlagenen Overlays darf nicht bei allen heutigen Anwendungen vorausgesetzt werden; ebenso wenig gehört es zwingend zu APRS101. Auch bei `Fxxxx`, `Vxxx` und `Zxx` muss die tatsächliche Kompatibilität der empfangenden Software berücksichtigt werden.

## Symbole und Erkennung von Wetterdaten

Das klassische Wetterstationssymbol ist `/_`; die alternative Symboltabelle ermöglicht auch `\_`. Die Klarstellungen zu APRS 1.1 berücksichtigen außerdem `/W` und `\W` als weitere wetterstationsbezogene Symbole. Das Dokument von 2011 schlägt vor, die Sensorerkennung mithilfe von Wettersymbolen mit Overlays und Gefahrensymbolen zu vereinheitlichen. Dieser spätere Vorschlag ändert nichts an den Dekodierregeln für die grundlegenden WX-Felder.

Das bedeutet nicht, dass jedes Paket mit dem Zeichen `_` ein WX-Bericht ist. In einem Positionsbericht muss der Symbolcode an seiner vorgesehenen Stelle erkannt und die Syntax der nachfolgenden Daten geprüft werden. In einem Bericht ohne Position erfüllt `_` eine andere Funktion: Es ist das erste Byte des Information-Feldes und damit der DTI.

## CWOP: von APRS-Stationen zur professionellen Wetterbeobachtung

Das WX-Format wird auch außerhalb von Amateurfunknetzen eingesetzt. Ein Beispiel ist das **Citizen Weather Observer Program (CWOP)**, das aus APRSWXNET und dem Amateurfunkumfeld hervorging. Das Programm ermöglicht es Freiwilligen, Messwerte ihrer privaten Wetterstationen in einen gemeinsamen meteorologischen Datenbestand einzubringen. Teilnehmen können sowohl Funkamateure als auch Beobachter, die ihre Berichte ohne Funkübertragung direkt über das Internet einspeisen.

Seit dem 1. Juli 2001 fließen CWOP-Beobachtungen in das von der US-amerikanischen NOAA entwickelte System **MADIS (Meteorological Assimilation Data Ingest System)** ein. MADIS integriert Messwerte aus zahlreichen unabhängigen Quellen, vereinheitlicht ihre Formate, Einheiten und Zeitstempel und führt automatische Qualitätskontrollen durch. Deren Ergebnisse werden den Beobachtungen zugeordnet, damit Datennutzer die Zuverlässigkeit einzelner Messwerte berücksichtigen können.

Bei einer Amateurfunkstation kann ein WX-Bericht über Funk und ein IGate in APRS-IS gelangen. CWOP-Stationen können ihre Daten außerdem über eine geeignete Internetverbindung übertragen. Ein vereinfachter Datenfluss für **an CWOP teilnehmende Stationen** sieht so aus:

```text
WX-Station -> Funk -> IGate -> APRS-IS --+
                                        +-> CWOP / APRSWXNET -> NOAA MADIS
WX-Station -> Internet ------------------+                         |
                                                                  +-> Wetterdienste
                                                                  +-> Forschungseinrichtungen
                                                                  +-> Hochschulen und weitere Nutzer
```

Dies ist ein Funktionsschema und keine Darstellung aller internen Verbindungen. Die Art, wie MADIS die Daten abruft, hat sich im Laufe der Zeit geändert: Seit 2023 verweist NOAA auf eine direkte Übernahme von den Servern von APRSWXNET und CWOP anstelle des früheren Weges über den Dienst findU.

CWOP-Daten stehen einer großen Gruppe meteorologischer Nutzer zur Verfügung, darunter Vorhersagebüros des US-amerikanischen **National Weather Service (NWS)**, Forschungszentren, Hochschulen und private Organisationen. Sie können Beobachtungen professioneller Stationen ergänzen und zur lokalen Wetterüberwachung, Vorhersageverifikation und Modellierung beitragen. Der NWS verweist außerdem auf die Nutzung solcher Beobachtungen bei der Erstellung von Wettervorhersagen und -warnungen. Daraus folgt jedoch nicht, dass jeder einzelne Messwert in sämtlichen genannten Anwendungen eingesetzt wird.

### Stationsqualität und Registrierung sind wichtig

Der praktische Wert solcher Berichte hängt nicht allein von einer korrekten WX-Syntax ab. Ebenso wichtig sind die richtige Aufstellung der Sensoren, korrekte Einheiten und Messzeitpunkte, aktuelle Stationskoordinaten und die Vermeidung von Messfehlern. Die MADIS-Qualitätskontrolle kann manche Auffälligkeiten erkennen und fragwürdige Werte kennzeichnen, ersetzt aber keine fachgerechte Installation der Wetterstation.

**Nicht jeder Wetterbericht, der in APRS-IS sichtbar ist, gelangt automatisch in MADIS.** Für die Teilnahme an CWOP sind eine Registrierung und die korrekte Konfiguration des Datenübertragungswegs erforderlich. NOAA stellt ein gesondertes Formular für neue Teilnehmer und zur Aktualisierung vorhandener Stationen bereit, auch für Funkamateure, die ihr Rufzeichen verwenden.

CWOP zeigt die weiterreichende Bedeutung des WX-Formats: Ein korrekt kodiertes APRS-Paket kann mehr sein als eine Information auf einer Karte; es kann Teil eines Systems zur Erfassung und Weitergabe von Messwerten für die professionelle Meteorologie werden.

## Messmethodik: APRS-Konformität und CWOP-Datenqualität

Ein korrekt aufgebauter WX-Frame garantiert noch keine verlässlichen Daten. Die APRS-Spezifikation beschreibt Format und Bedeutung der Felder. Dagegen enthält der [CWOP-Leitfaden von 2005](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf) Empfehlungen zur Messmethodik, Geräteleistung und Aufstellung der Sensoren. Beide Dokumente sollten gemeinsam gelesen werden, ihre Anforderungen dürfen jedoch nicht gleichgesetzt werden. Ziel einer eigenen Implementierung sollten korrekte APRS-Berichte und eine möglichst hohe Qualität der an CWOP/MADIS übertragenen Beobachtungen sein, nicht die Zusage, jede Qualitätskontrolle zu bestehen.

### Wind: zwei unterschiedliche Methoden zur Wertermittlung

| Parameter | Klassisches APRS WX | Empfehlungen des CWOP-Leitfadens von 2005 |
| --- | --- | --- |
| Mittlere Windgeschwindigkeit | Mittelwert der letzten 1 Minute | Mittelwert der letzten 2 Minuten |
| Windrichtung | Richtung, aus der der Wind weht, in Grad | Mittlere Richtung der letzten 2 Minuten, bezogen auf geografisch Nord |
| Böe `gxxx` | Höchste Geschwindigkeit der letzten 5 Minuten | Höchster Geschwindigkeitsmesswert der letzten 10 Minuten |
| Abtastung | `WX.TXT` beschreibt als historisches Beispiel vier Messwerte im Abstand von 15 Sekunden zur Berechnung des Minutenmittels | Der CWOP-Leitfaden empfiehlt, Sensoren mindestens alle 5 Sekunden auszulesen |

Diese Abweichung ist dokumentiert. Die Autoren des CWOP-Leitfadens führten selbst die Änderung der APRS-Zeiträume von **1 auf 2 Minuten** und von **5 auf 10 Minuten** als vorgeschlagene Änderungen des Formats auf. Deshalb dürfen die CWOP-Zeiträume nicht als in APRS101 übernommene Felddefinitionen dargestellt werden. Wichtig ist außerdem, dass `ddd/sss` und `gxxx` keine Metadaten enthalten, aus denen der Empfänger den verwendeten Messzeitraum erkennen könnte. Software für beide Einsatzzwecke sollte die Rohmesswerte aufbewahren und getrennte Ergebnisse für ein APRS-Profil und ein CWOP-Messprofil berechnen. Das Profil des ausgesendeten Berichts muss bewusst gewählt und dokumentiert werden.

Zur Ermittlung der mittleren Windgeschwindigkeit genügt das passende Zeitfenster. Richtungen dürfen dagegen nicht mit einem gewöhnlichen arithmetischen Mittel berechnet werden: Werte von 359° und 1° stehen für Nordwind und nicht für 180°. Hierfür ist ein zirkulärer Mittelwert oder ein geeignetes Vektorverfahren erforderlich, das berücksichtigt, wie der Sensor seine Werte liefert. Bei Windstille und unzuverlässiger Richtungsangabe sollte keine Scheingenauigkeit erzeugt werden. Zudem muss das im CWOP-Leitfaden beschriebene Maximum von Momentanmesswerten nicht anderen Böendefinitionen entsprechen, etwa dem maximalen Drei-Sekunden-Mittel nach der WMO-Methodik.

### Niederschlag: drei unabhängige Zeiträume

Die zuverlässigste Berechnungsgrundlage ist eine durchgehende, zeitgestempelte Folge von Niederschlagszuwächsen, etwa den Ereignissen eines Kippwaagen-Regenmessers. Daraus berechnet der Generator getrennt `rxxx` für die letzten 60 Minuten, `pxxx` für die gleitenden letzten 24 Stunden und `Pxxx` für den Zeitraum seit der **lokalen Mitternacht am Stationsstandort**. `p` darf weder aus `P` abgeleitet noch das gleitende Zeitfenster durch die Niederschlagssumme seit Tagesbeginn ersetzt werden. Kodiert werden die Ergebnisse unabhängig von der Sensoreinheit in Hundertstel Zoll.

Nach einem Neustart des Geräts oder einem Verlust der Messhistorie darf keine unvollständige Summe übertragen werden, als decke sie den gesamten vorgeschriebenen Zeitraum ab. Wenn sich ein bestimmtes Zeitfenster aus dem Archiv nicht rekonstruieren lässt, muss der entsprechende Messwert als nicht verfügbar gekennzeichnet oder das optionale Feld weggelassen werden. Beim Zurücksetzen eines Regenmesserzählers ist tatsächlicher Niederschlag von dem durch den Reset ausgelösten Sprung zu unterscheiden. Wichtig sind außerdem die korrekte Zeitzone der Station und der Erhalt der Zeitstempel der Niederschlagszuwächse.

### Temperatur, Luftfeuchtigkeit und Luftdruck

APRS legt Einheiten und Kodierung dieser Felder fest, schreibt jedoch kein einheitliches Mittelungsintervall vor. Der CWOP-Leitfaden von 2005 **empfiehlt** einen Temperaturmittelwert der letzten 5 Minuten sowie einen Luftfeuchtigkeitsmittelwert der letzten Minute, der zur Berechnung des Taupunkts verwendet wird. Dies sind Qualitätsempfehlungen und keine zusätzlichen vorgeschriebenen Bytes eines WX-Berichts. Jeder übertragene Wert sollte sich auf den tatsächlichen Messzeitpunkt beziehen und nicht auf irgendeinen zuletzt gespeicherten Wert.

Besondere Aufmerksamkeit verdient das Feld `bxxxxx`: APRS legt seine Einheit (Zehntel hPa) fest, aber eine formal korrekte Kodierung sagt noch nichts darüber aus, **welchen Luftdruck** das Messgerät ausgibt. Der CWOP-Leitfaden von 2005 nennt als vorgesehenen Parameter das *Altimeter Setting* (QNH), also einen nach der entsprechenden Methode reduzierten Luftdruck statt des unkorrigierten Messwerts auf Sensorhöhe. QNH darf ohne Prüfung auch nicht mit dem meteorologischen Luftdruck auf Meereshöhe (QFF) gleichgesetzt werden. Vor der Übermittlung von Daten an CWOP müssen die Art des von der Station ausgegebenen Werts, ihre Kalibrierung sowie die Empfehlungen der verwendeten Software geprüft werden.

### Sensoraufstellung und Qualitätskontrolle

Fehler durch einen ungeeigneten Aufstellungsort lassen sich durch einen Algorithmus nicht beheben. Der CWOP-Leitfaden empfiehlt ein strahlungsgeschütztes, belüftetes Thermometer etwa 1,5 m über repräsentativem Untergrund, einen Windmesser nach Möglichkeit in 10 m Höhe an einem möglichst freien Standort sowie einen waagerecht montierten Regenmesser, der vor Strömungsstörungen geschützt ist. In bebauten Gebieten können Kompromisse unvermeidbar sein, sie sollten jedoch dokumentiert werden. Die Metadaten der Station, insbesondere Koordinaten und Höhe, müssen dem tatsächlichen Messstandort entsprechen.

[MADIS](https://madis.ncep.noaa.gov/madis_qc.shtml) prüft je nach Parameter und verfügbarer Prüfstufe Wertebereiche, innere Konsistenz, zeitliche Änderungen und räumliche Übereinstimmung. Die Ergebnisse werden den Beobachtungen als Qualitätskennzeichnungen zugeordnet. Dies ist kein universeller Test, der eine gesamte Station dauerhaft zertifiziert: Selbst ein korrekter Messwert kann als verdächtig markiert werden, während ein syntaktisch gültiger Frame fehlerhafte Werte enthalten kann. Betreiber sollten die Rückmeldungen regelmäßig auswerten, ihre Messwerte mit geeigneten Referenzstationen vergleichen und die Kalibrierung kontrollieren.

### Hinweise für Entwickler von WX-Software

Trenne drei Verarbeitungsschritte voneinander: **Erfassung der Messproben**, **Berechnung der Beobachtungswerte** und **APRS-Kodierung**. So verändert eine andere Sendefrequenz nicht versehentlich die Mittelungsintervalle, und derselbe Messdatenstrom kann unterschiedliche Beobachtungsprofile versorgen. Insbesondere:

1. Speichere für jede Messprobe Zeitstempel, ursprüngliche Einheit und Gültigkeitsinformation sowie eine ausreichende Historie für das längste verwendete Messzeitfenster.
2. Erkenne fehlende Daten, Kommunikationsverluste zum Sensor, das Zurücksetzen von Zählern und nach dem Start unvollständige Zeitfenster. Wandle diese Situationen nicht in Nullmessungen um.
3. Berechne statistische Größen anhand der Rohdaten und runde beziehungsweise konvertiere erst anschließend in die Einheiten des APRS-Frames. Vermeide wiederholte Umrechnungen und zwischenzeitliche Rundungen.
4. Dokumentiere die verwendete Methodik, Mittelungsintervalle und Sensorkonfiguration. Das erleichtert die korrekte Interpretation der Daten und die Untersuchung möglicher MADIS-Qualitätskennzeichnungen.

Allein die Protokollkonformität garantiert weder die Aufnahme der Daten in CWOP noch die positive Bewertung jedes einzelnen Messwerts. Dafür sind außerdem die Registrierung der Station, ein korrekt eingerichteter Übertragungsweg, zuverlässige Messungen und eine fortlaufende Qualitätskontrolle nötig.

## Hinweise zur Implementierung

Unabhängig von der Messmethodik müssen WX-Generatoren und -Parser die syntaktischen Vorgaben des Protokolls einhalten. Besonders wichtig sind folgende Regeln:

1. Bevorzuge den Complete Weather Report, der Position und Messwerte in einer einzigen Übertragung zusammenfasst.
2. Halte beim Ein- und Auslesen die Protokolleinheiten ein. Rechne erst für die Darstellung oder vor der Kodierung der zu sendenden Werte in metrische Einheiten beziehungsweise aus ihnen um.
3. Behandle `r`, `p` und `P` als drei verschiedene Niederschlagsmesszeiträume.
4. Prüfe die **Anwesenheit der Pflichtfelder** unabhängig davon, ob die eigentlichen Messwerte verfügbar sind. Beim unkomprimierten vollständigen Bericht müssen `ddd/sss` und `t` erhalten bleiben, beim Bericht ohne Position Zeitstempel, `c`, `s`, `g` und `t`.
5. Setze einen fehlenden Messwert nicht mit null gleich. Berücksichtige Punkte, Leerzeichen und das zulässige Weglassen von Feldern. Bevorzuge Punkte, da sie Textmitschnitte leichter lesbar machen.
6. Erkenne die unterschiedlichen Windkodierungen in vollständigen Berichten, Berichten ohne Position und Berichten mit komprimierter Position.
7. Unterscheide klassische WX-Felder von späteren Erweiterungen und ermögliche das Ignorieren unbekannter Felder. Gehe nicht davon aus, dass alle Clients das 2011 vorgeschlagene `Xxxx` unterstützen.
8. Verwechsle einen Messbericht nicht mit einer Wetterwarnung. Gefahrenmeldungen, darunter NWS-WARN und andere Warnsysteme, nutzen separate Mechanismen.
9. Ändere die Semantik der Felder nicht allein deshalb, weil eine Anwendung mit CWOP zusammenarbeitet. Dokumentiere das Messprofil und unterscheide APRS-Formatvorgaben von Empfehlungen zur Datenqualität.

## Quellen

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF), Kapitel 12: Weather Reports.
- [APRS 1.1: Weather Specification Comments](https://www.aprs.org/aprs11/spec-wx.txt), WB4APR, aktualisiert am 24. März 2011.
- [APRS 1.2.1: Weather Updates to the Spec](https://www.aprs.org/aprs12/weather-new.txt), WB4APR, 24. März 2011; enthält auch vorgeschlagene Erweiterungen.
- [Water Gauges in APRS](https://www.aprs.org/aprs12/watergage.txt), WB4APR, 2006; aktualisiert am 24. März 2011.
- [IAEA: Informationen zum Unfall von Fukushima Daiichi](https://www.iaea.org/newscenter/news/fukushima-nuclear-accident-update-log-20), Dokumentation der Ereignisse vom März 2011.
- WB4APR, *WX.TXT: Using APRS in Weather and SKYWARN Applications*, Version 8.3.5 vom 10. März 1999, aktualisiert am 18. August 2010 (historisches Dokument).
- [NOAA MADIS: Citizen Weather Observer Program Data](https://madis.ncep.noaa.gov/madis_cwop.shtml), Ziele und Geschichte von CWOP sowie Datenverarbeitung.
- [NOAA MADIS: APRSWXNET/CWOP Snow Project](https://madis.ncep.noaa.gov/snow_project.shtml), Datennutzer und Anwendungsbeispiele der Beobachtungen.
- [NOAA NWS: Join CWOP](https://www.weather.gov/pub/JoinCWOP), Nutzung der Berichte für Vorhersagen und Wetterwarnungen.
- [NOAA MADIS: Registration and Update Form](https://madis.ncep.noaa.gov/cwop_signup.shtml), Anforderungen zur Stationsanmeldung.
- [CWOP Weather Station Siting, Performance, and Data Quality Guide](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf), Version 1.0 vom 8. März 2005; Messempfehlungen und historische Liste vorgeschlagener APRS-Änderungen.
- [NOAA MADIS: Quality Control](https://madis.ncep.noaa.gov/madis_qc.shtml), Überblick über die Qualitätskontrolle und Kennzeichnungen von Beobachtungen.
- [NOAA MADIS: Meteorological Surface Quality Control](https://madis.ncep.noaa.gov/madis_sfc_qc.shtml), Umfang und Stufen der Qualitätsprüfung meteorologischer Bodenbeobachtungen.
- [WMO Guide to Meteorological Instruments and Methods of Observation](https://www.weather.gov/media/epz/mesonet/CWOP-WMO8.pdf), Referenz zu internationalen Definitionen der Wind- und Böenmessung.
- [NOAA MADIS: Recent Updates](https://madisqa.ncep.noaa.gov/madis_recent.shtml), Änderung der Erfassung von CWOP-Daten im Jahr 2023.
