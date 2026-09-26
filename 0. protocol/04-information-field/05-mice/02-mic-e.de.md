---
title: "Mic-E: Geschichte, Anwendungen und Datenformat"
description: "APRS-Positionskodierung in der AX.25-Zieladresse und im Informationsfeld, Geschichte von Mic-E, Erweiterungen und Dekodierungsregeln."
---


**Mic-E (Microphone Encoder)** ist ein Format für kompakte APRS-Positionsmeldungen. Charakteristisch ist, dass es nicht nur das AX.25-Informationsfeld, sondern auch die Zieladresse nutzt. Die sechs Zeichen dieser Adresse enthalten den kodierten Breitengrad und weitere Informationen; das Informationsfeld ergänzt die Meldung um Längengrad, Kurs, Geschwindigkeit und Symbol. Mic-E ist weder ein eigenständiges Sicherungsschichtprotokoll noch eine Variante des Base-91-basierten Formats *Compressed Position*.

Das Format überträgt viele Informationen in einem kurzen Rahmen. Laut *APRS Protocol Reference* ist ein vollständiger minimaler Mic-E-Rahmen ohne Digipeater-Adressen und optionale Daten 25 Byte lang, ohne AX.25-FCS und Flags. Diese Angabe bezieht sich auf den gesamten Rahmen, nicht nur auf das Informationsfeld.

## Geschichte

Mic-E wurde 1994 von Bob Bruninga, WB4APR, und N3MIM entwickelt. Ursprünglich handelte es sich um einen Hardware-Mikrofonencoder, mit dem APRS-Daten über ein Funkgerät gesendet werden konnten. Auf den Prototyp folgte der TAPR-Mic-E-Bausatz; das Format kam auch in PIC-Encoder-Geräten zum Einsatz.

Während die formale APRS-Spezifikation entstand, befanden sich die Kenwood-Funkgeräte TH-D7 und TM-D700 bereits in Entwicklung. Deshalb wurden Gerätekennungen im optionalen Textfeld vorgesehen: `>` für den TH-D7 und `]` für den TM-D700. Die im Jahr 2000 veröffentlichte APRS-Spezifikation 1.0 dokumentierte Positionskodierung, Mic-E-Nachrichtencodes, Telemetrie und Statustext.

Mit weiteren Geräten zeigten sich die Grenzen des ursprünglichen Kennungssystems. Der TM-D710 behielt die Familienkennung `]`; ein abschließendes `=` unterscheidet das Modell. 2007–2008 wurde die Kennzeichnung um Kategorien für andere Geräte erweitert: `` ` `` bezeichnete nachrichtenfähige Geräte, `'` einseitig sendende Tracker. Anschließend wurde ein abschließendes Hersteller- und Versionszeichenpaar (`Mv`) eingeführt. Zusätzliche Informationen wurden im Text untergebracht, ohne die grundlegende Positionskodierung zu verändern, um die Kompatibilität mit älteren Empfängern zu erhalten.

*Mic-E TYPE CODES* dokumentiert weitere Kennungen und Geräte mit Aktualisierungen bis zum 4. Juni 2019. Es handelt sich um eine historische Liste, nicht um ein vollständiges Verzeichnis aller späteren Modelle.

## Anwendungen

Mic-E wurde in Mikrofonencodern, eigenständigen Trackern und APRS-Funkgeräten eingesetzt. Das kompakte Format eignet sich für mobile Stationen, die regelmäßig Position, Kurs und Geschwindigkeit senden. Mit einem Mic-E-Nachrichtencode lässt sich zusätzlich einer der vordefinierten oder ein benutzerdefinierter Status übermitteln.

Empfänger, Digipeater und IGate verarbeiten weiterhin einen AX.25-Rahmen. Die APRS-Software muss jedoch erkennen, dass die scheinbare Zieladresse Daten und keine gewöhnliche TOCALL-Anwendungskennung enthält. Sie darf deshalb nicht wie die Zieladresse eines herkömmlichen APRS-Rahmens interpretiert werden.

## Datenaufteilung im Rahmen

Mic-E verwendet einen standardmäßigen AX.25-UI-Rahmen. Die Daten verteilen sich wie folgt:

| Feld | Mic-E-Information |
| --- | --- |
| Zieladresse, Zeichen 1–3 | Erste drei Breitengradziffern und Nachrichtenbits A/B/C |
| Zieladresse, Zeichen 4 | Vierte Breitengradziffer und N/S-Kennung |
| Zieladresse, Zeichen 5 | Fünfte Breitengradziffer und Längengrad-Offset |
| Zieladresse, Zeichen 6 | Sechste Breitengradziffer und E/W-Kennung |
| SSID der Zieladresse | Historischer APRS-Pfadcode |
| Informationsfeld, Byte 1 | Mic-E-Datentypkennung (DTI) |
| Informationsfeld, Bytes 2–4 | Längengrad |
| Informationsfeld, Bytes 5–7 | Geschwindigkeit und Kurs |
| Informationsfeld, Bytes 8–9 | Symbolcode und Symboltabellenkennung |
| Weitere Bytes | Optionale Telemetrie oder Statustext mit Erweiterungen |

Die sechs Zeichen der Zieladresse werden als gültige AX.25-Adresszeichen kodiert. Sie stellen damit jedoch nicht das Rufzeichen einer Zielstation dar. Die Quelladresse bleibt die Kennung der sendenden Station.

## Breitengrad in der Zieladresse

Die sechs Breitengradziffern entsprechen der Schreibweise `DDMM.hh`: zwei Grad-, zwei Minuten- und zwei Hundertstelminutenziffern. Jedes Adresszeichen kodiert eine Ziffer und abhängig von seiner Position weitere Informationen.

| Zeichenbereich | Breitengradziffer | Zusätzliche Information |
| --- | --- | --- |
| `0`–`9` | `0`–`9` | Bit `0`; an Position 4–6 jeweils S, Offset `+0`, E |
| `A`–`J` | `0`–`9` | Bit `1` einer benutzerdefinierten Nachricht an Position 1–3 |
| `P`–`Y` | `0`–`9` | Bit `1` einer Standardnachricht; an Position 4–6 jeweils N, Offset `+100`, W |
| `K`, `L`, `Z` | Keine Ziffer | Sonderwerte für Positionsungenauigkeit |

An den Positionen 4–6 werden die Zeichen `A`–`K` nicht verwendet. Die genaue Bedeutung der Sonderzeichen hängt von ihrer Position ab. Beispielsweise kodiert die Zieladresse `S32U6T` den Breitengrad **33°25,64′ N**, die Nachrichtenbits `100` (Standardstatus *Returning*), den Längengrad-Offset `+0` und die westliche Hemisphäre. Dieses Beispiel stammt direkt aus Kapitel 10 der Spezifikation.

### Mic-E-Nachrichtencode

Die ersten drei Adresszeichen enthalten die Bits A, B und C. Von null verschiedene Kombinationen können eine Standardnachricht oder eine benutzerdefinierte Nachricht übermitteln. Sind alle drei Bits null, bedeutet dies *Emergency*.

| A | B | C | Standard | Benutzerdefiniert |
| --- | --- | --- | --- | --- |
| 1 | 1 | 1 | M0: Off Duty | C0: Custom-0 |
| 1 | 1 | 0 | M1: En Route | C1: Custom-1 |
| 1 | 0 | 1 | M2: In Service | C2: Custom-2 |
| 1 | 0 | 0 | M3: Returning | C3: Custom-3 |
| 0 | 1 | 1 | M4: Committed | C4: Custom-4 |
| 0 | 1 | 0 | M5: Special | C5: Custom-5 |
| 0 | 0 | 1 | M6: Priority | C6: Custom-6 |
| 0 | 0 | 0 | Emergency | Emergency |

Bei Standardnachrichten müssen alle gesetzten Bits standardmäßig kodiert sein, bei benutzerdefinierten Nachrichten entsprechend benutzerdefiniert. Eine Mischung beider Kodierungsarten bei gesetzten Bits ergibt einen unbekannten Nachrichtentyp. Die Unterstützung benutzerdefinierter Nachrichten ist optional und fehlte den ursprünglichen Mic-E-Geräten. Ein Mic-E-Nachrichtencode ist keine APRS-Textnachricht mit Bestätigungsmechanismus.

### SSID der Zieladresse

Die ursprüngliche Spezifikation sieht vor, über die SSID der Zieladresse einen herkömmlichen Pfad oder einen der historischen generischen APRS-Pfade zu kodieren. Dieses Feld ist nicht mit einer Anwendungskennung gleichzusetzen; die historische Pfadkodierung ist auch keine Empfehlung für die Konfiguration heutiger Netze. Die tatsächliche Digipeater-Adressliste bleibt, sofern vorhanden, ein separates Feld des AX.25-Rahmens.

## Informationsfeld

Der grundlegende Teil des Mic-E-Informationsfeldes besteht aus neun Bytes:

```text
DTI | d+28 | m+28 | h+28 | SP+28 | DC+28 | SE+28 | SYMBOL | TABLE
 1  |   2  |   3  |   4  |   5   |   6   |   7   |    8   |   9
```

Die folgenden Bytes sind optional. Nicht alle Bytes des grundlegenden Teils müssen druckbare ASCII-Zeichen sein. Entfernt die Übertragung oder eine Textkonvertierung Steuerzeichen, verschieben sich die übrigen Bytes und die Dekodierung wird fehlerhaft. Die Spezifikation verlangt, Pakete mit einem Informationsfeld von weniger als neun Bytes zu verwerfen.

### DTI und eine historische Inkompatibilität

Ursprünglich kennzeichnete `` ` `` eine aktuelle GPS-Position und `'` eine veraltete Position. Der Kenwood TM-D700 verwendete jedoch den Apostroph für eine aktuelle Position. Die Spezifikation dokumentiert diese Abweichung und schlägt vor, den TM-D700 anhand der Kennung `]` am Anfang des Statustextes zu erkennen. Frühe Betageräte verwendeten außerdem `0x1c` und `0x1d`.

Das **erste Byte des Informationsfeldes (DTI)** darf nicht mit der **Gerätekennung** verwechselt werden, die erst nach dem neun Byte langen Grundteil auftreten kann. Beide Stellen können `` ` `` oder `'` enthalten, haben aber unterschiedliche Bedeutungen.

### Längengrad

Der Längengrad wird in den Bytes `d+28`, `m+28` und `h+28` übertragen. Die E/W-Hemisphäre und der Offset `+0` oder `+100` sind aus der Zieladresse zu lesen.

Dekodierung der Grade:

1. `d = Wert_Byte_2 - 28`.
2. Zeigt die Adresse einen Offset `+100`, `100` addieren.
3. Liegt das Ergebnis im Bereich `180–189`, `80` subtrahieren.
4. Liegt das Ergebnis im Bereich `190–199`, `190` subtrahieren.

Diese ungewöhnlichen Korrekturen sind aufgrund der Kodierung der Gradbereiche `0–9` und `100–109` erforderlich. Ein gültiges Grad-Byte liegt im Dezimalbereich `38–127`.

Dekodierung der Minuten:

1. `m = Wert_Byte_3 - 28`.
2. Falls `m >= 60`, `60` subtrahieren.

Dekodierung der Hundertstelminuten: `h = Wert_Byte_4 - 28`. Das Ergebnis entspricht Hundertstelminuten. Die ursprünglichen Bytewerte sind zu erhalten und die dekodierten Wertebereiche zu prüfen.

### Geschwindigkeit und Kurs

Die nächsten drei Bytes kodieren die Geschwindigkeit in Knoten und den Kurs in Grad. Die Spezifikation sieht Geschwindigkeiten von `0–799` Knoten und Kurse von `0–360°` vor; `0` steht für einen unbekannten oder unbestimmten Kurs, `360` für genau Nord.

Der Decoder rekonstruiert die Werte, nachdem er von jedem der drei Bytes `28` abgezogen hat:

```text
sp = byte_5 - 28
dc = byte_6 - 28
se = byte_7 - 28

speed  = sp * 10 + floor(dc / 10)
course = (dc % 10) * 100 + se

if speed >= 800: speed -= 800
if course >= 400: course -= 400
```

In der Praxis müssen zwei historische Kodierungsverfahren für bestimmte `SP+28`- und `DC+28`-Werte berücksichtigt werden, die in den Tabellen der Spezifikation beschrieben sind. Der obige Dekodierungsalgorithmus berücksichtigt beide Varianten durch seine abschließenden Korrekturen. Werte außerhalb der zulässigen Bereiche sind zurückzuweisen oder als fehlerhaft zu kennzeichnen, statt sie automatisch als gültige Position darzustellen.

Die Spezifikation enthält ein Beispiel mit **86 Knoten und Kurs 194°**: `SP+28` kann `t` oder `$` sein, `DC+28` kann `]` oder `Y` sein und `SE+28` ist `z`.

### Symbol und Symboltabelle

Das achte Byte ist der Symbolcode, das neunte die Symboltabellen- oder Overlay-Kennung. Ihre Reihenfolge ist umgekehrt zur üblichen textuellen APRS-Symbolschreibweise, bei der die Tabelle vor dem Symbolcode steht. Das Dokument zur Entwicklung von Mic-E führt diese Reihenfolge auf die spätere Ergänzung der alternativen Symboltabelle im ursprünglichen Format zurück.

## Positionsungenauigkeit

Mic-E unterstützt die bewusste Verringerung der Genauigkeit einer gemeldeten Position. Die Ungenauigkeit wird in den sechs Breitengradzeichen gekennzeichnet; derselbe Ungenauigkeitsgrad muss auch auf den Längengrad angewendet werden. Im Spezifikationsbeispiel `T4SQZZ` sind die letzten beiden Breitengradziffern unbestimmt; bei der Dekodierung des Längengrades müssen die entsprechenden letzten beiden Genauigkeitsziffern ebenfalls ignoriert werden. Software darf solche Daten nicht als präzise Koordinaten darstellen.

## Optionale Telemetrie und Statustext

Nach den neun grundlegenden Bytes können **entweder Mic-E-Telemetrie oder Statustext** folgen. Die Interpretation richtet sich nach dem ersten Byte dieses optionalen Teils.

| Kennzeichen | Bedeutung gemäß APRS 1.0 |
| --- | --- |
| `` ` `` | Zwei druckbare hexadezimale Telemetriewerte; Kanäle 1 und 3 |
| `'` | Fünf druckbare hexadezimale Telemetriewerte |
| `0x1d` | Fünf binäre Telemetriewerte; historische Betageräte |

Bei der druckbaren Variante benötigt jeder Wert `0–255` zwei Hexadezimalzeichen. Das Spezifikationsbeispiel `'7200007100` entspricht fünf Werten: `114`, `0`, `0`, `113`, `0`.

Handelt es sich beim optionalen Teil um Statustext, darf er nicht mit Zeichen beginnen, die als Telemetriekennzeichen reserviert sind; andernfalls würde er falsch interpretiert. Der Text kann einen Kommentar, einen Maidenhead-Locator und die Höhe enthalten. Die Spezifikation erlaubt außerdem eine standardmäßig formatierte APRS-Position innerhalb des Mic-E-Statustextes; der Empfänger kann diese anstelle der in Mic-E kodierten Position verwenden. Historisch war dies unter anderem beim Betrieb eines Encoders ohne GPS-Empfänger nützlich.

### Maidenhead-Locator

Im Statustext kann ein Locator stehen. Folgt darauf ein Kommentar, verlangt die Spezifikation eine geeignete Trennung des Textes. Bei der Locator-Erkennung muss eine gegebenenfalls vorangestellte Gerätekennung berücksichtigt werden.

### Höhe im Format `aaa}`

Die Mic-E-Höhe wird durch drei Base-91-Zeichen und ein abschließendes `}` kodiert. Der Bezugspunkt liegt 10.000 m unter dem mittleren Meeresspiegel. Zur Kodierung der Höhe in Metern:

```text
v = hoehe_m + 10000
zeichen_1 = floor(v / (91 * 91)) + 33
zeichen_2 = floor(v / 91) % 91 + 33
zeichen_3 = v % 91 + 33
```

Bei der Dekodierung wird dieser Vorgang umgekehrt und `10000` subtrahiert. Die Spezifikation nennt das Beispiel einer Höhe von 200 Fuß (etwa 61 m), kodiert als `"4T}`. Spätere Empfehlungen platzieren das optionale `aaa}` unmittelbar nach der Gerätetypkennung und vor dem übrigen Text.

## Entwicklung des Textfeldes

Die ursprünglichen Geräte übertrugen Statustext ohne umfangreiche Kennung. Spätere Erweiterungen wurden im selben Bereich untergebracht, damit ältere Empfänger weiterhin die grundlegende Position dekodieren konnten. Die Dokumente von WB4APR beschreiben folgende Elemente:

| Element | Bedeutung | Position |
| --- | --- | --- |
| `T` | Optionale Gerätetypkennung | Anfang des Statustextes |
| `aaa}` | Optionale Base-91-Höhe | Unmittelbar nach `T`, falls vorhanden |
| `FFF.FFFMHz` | Optionale Frequenz | Anfang des eigentlichen Textes, nach einer möglichen Höhenangabe |
| Freitext | Stationskommentar | Nach Feldern mit festgelegter Position |
| `/A=xxxxxx` | Ältere Höhenschreibweise | Weiter hinten im Kommentar, falls verwendet |
| `!DAO!` | Erweiterte Positionsgenauigkeit und Datum | Empfohlen gegen Ende des Textes |
| `Mv` | Hersteller- und Versionskennung | Textende im neueren Kennungssystem |

Diese Übersicht verbindet Elemente aus unterschiedlichen Entwicklungsphasen von Mic-E. Nicht alle sind vorgeschrieben oder gleichzeitig vorhanden. `!DAO!` bezeichnet hier die in der APRS-1.2-Dokumentation beschriebene Erweiterungsfamilie und keinen wörtlich zu sendenden Wert. Die genaue Bedeutung und Validierung müssen der Dokumentation dieser Erweiterung folgen.

Wenn Höhe und Frequenz gemeinsam vorkommen, lautet die empfohlene Reihenfolge `T`, `aaa}`, `FFF.FFFMHz`, gefolgt von Text und weiteren Zusätzen. Das Dokument von 2008 empfiehlt außerdem ein Leerzeichen nach der Frequenz, um die Kompatibilität mit älteren Yaesu-FTM-350-Geräten zu gewährleisten.

## Geräteidentifikation

Die Gerätekennung gehört zum **optionalen Text**, nicht zur Zieladresse oder zum DTI. Die ursprüngliche Spezifikation sah unter anderem `>` für den Kenwood TH-D7 und `]` für den TM-D700 vor. Spätere Vereinbarungen erweiterten diese Kennungen:

| Form | Interpretation in der WB4APR-Dokumentation |
| --- | --- |
| `>...` | Kenwood-TH-D7-Familie |
| `]...` | Kenwood TM-D700 |
| `]...=` | Kenwood TM-D710 |
| `>...=` | Kenwood TH-D72 |
| `>...^` | Kenwood TH-D74 |
| `` `...Mv `` | Anderes nachrichtenfähiges Mic-E-Gerät |
| `'...Mv` | Anderer einseitig sendender Mic-E-Tracker |

Die Auslassungspunkte in dieser Tabelle stehen für Text variabler Länge, nicht für eine bestimmte Bytezahl. Bei späteren Geräten kann die Kombination aus Anfangszeichen und abschließendem `Mv` Hersteller und Modell kennzeichnen. *Mic-E TYPE CODES* führt unter anderem Modelle von Yaesu, Byonics, AnyTone und SCS auf, wurde jedoch zuletzt 2019 aktualisiert. Kennungen außerhalb der bekannten Tabelle dürfen nicht dazu führen, dass eine ansonsten gültige Mic-E-Position verworfen wird.

Auch bei der Textanzeige ist Kompatibilität wichtig: Eine erkannte Typkennung kann aus dem für den Bediener sichtbaren Kommentar entfernt und separat als Geräteinformation angezeigt werden. Unbekannte Zeichen am Textende dürfen nicht pauschal entfernt werden, da sie Teil des Kommentars sein können.

## Dekodierungsbeispiele

### Zieladresse `S32U6T`

Beispiel aus der APRS Protocol Reference:

- Sechs Ziffern: `332564`, entsprechend **33°25,64′**.
- Das vierte Zeichen `U` kennzeichnet die nördliche Hemisphäre.
- Das fünfte Zeichen `6` kennzeichnet den Längengrad-Offset `+0`.
- Das sechste Zeichen `T` kennzeichnet die westliche Hemisphäre.
- Nachrichtenbits: `100`, Standardstatus **M3: Returning**.

Die Adresse allein enthält nicht den vollständigen Längengrad: Seine Zahlenwerte müssen dem Informationsfeld entnommen werden.

### Längengrad aus dem Informationsfeld

Im Beispiel der Spezifikation haben die Bytes `(`, `_`, `f` die Dezimalwerte `40`, `95` und `102`. Mit Offset `+100` und W-Kennung erhält der Decoder:

```text
Grad:        40 - 28 + 100 = 112
Minuten:     95 - 28 - 60 = 7
Hundertstel: 102 - 28 = 74
Ergebnis:    112°07,74′ W
```

### Geschwindigkeit und Kurs aus dem Informationsfeld

Dasselbe Beispiel verwendet die Bytes `n`, `"`, `O`:

```text
sp = 110 - 28 = 82
dc =  34 - 28 = 6
se =  79 - 28 = 51

speed  = 82 * 10 + 0 = 820; 820 - 800 = 20 Knoten
course = 6 * 100 + 51 = 651; 651 - 400 = 251°
```

Ergebnis: **20 Knoten bei Kurs 251°**. Die abschließenden Bytes `j/` bezeichnen das Geländewagensymbol aus der primären Symboltabelle. Diese Beispiele zeigen die Dekodierung einzelner Formatbestandteile; sie dürfen nicht ohne Prüfung aller Felder zu einem neuen Rahmen zusammengesetzt werden.

## Hinweise zur Implementierung

Ein Mic-E-Decoder sollte Bytes verarbeiten und nicht annehmen, dass das gesamte Informationsfeld gewöhnlicher Text ist. Empfohlene Verarbeitungsschritte:

1. AX.25-Rahmen und Länge des Informationsfeldes prüfen (mindestens neun Bytes).
2. Mic-E-DTI erkennen und die sechs Zieladresszeichen dekodieren; dabei Standard-, benutzerdefinierte und unbekannte Nachrichten unterscheiden.
3. Koordinaten aus beiden Feldern rekonstruieren und Positionsungenauigkeit berücksichtigen.
4. Geschwindigkeit, Kurs, Symbol und Symboltabelle dekodieren.
5. Optionale Telemetrie oder Statustext erkennen, ohne deren Kennzeichen mit dem DTI zu verwechseln.
6. Im Statustext nur Erweiterungen und Gerätekennungen mit bekannter Syntax erkennen; unbekannte Daten erhalten.
7. Die kodierte Zieladresse bei der Weiterleitung nicht verändern. Änderungen ihrer Zeichen können Koordinaten, Nachrichtencode oder Hemisphärenkennungen verändern.

Bei der Umwandlung von Daten in Text ist besonders auf nicht druckbare Mic-E-Bytes zu achten. Bereits der Verlust eines einzigen Bytes kann die korrekte Dekodierung der gesamten Meldung verhindern. Software sollte außerdem historische Abweichungen bestimmter Geräte berücksichtigen, statt anzunehmen, dass allein der DTI die Aktualität einer GPS-Position eindeutig festlegt.

## Quellen und Umfang

- *APRS Protocol Reference*, APRS Protocol Version 1.0, Document Version 1.0.1, 29. August 2000, Kapitel 10: **Mic-E Data Format**. Grundlegende Kodierung der Zieladresse, des Informationsfeldes, der Nachrichten, der Positionsungenauigkeit, der Telemetrie und des Textes.
- Bob Bruninga, WB4APR, [*Mic-E TEST EXAMPLES*](https://www.aprs.org/aprs12/mic-e-examples.txt), 10. Dezember 2008. Entwicklung des Textfeldes, Reihenfolge der Erweiterungen und Beispiele zur Abwärtskompatibilität.
- Bob Bruninga, WB4APR, [*Mic-E TYPE CODES*](https://www.aprs.org/aprs12/mic-e-types.txt), aktualisiert am 4. Juni 2019. Historische und spätere Gerätekennungen.

Die Dokumente von 2008 und 2019 ergänzen Themen, die die ursprüngliche Spezifikation aus dem Jahr 2000 nicht vollständig behandelt. Die Gerätekennungen bilden kein vollständiges Verzeichnis sämtlicher heutiger Mic-E-Implementierungen. Allgemeine APRS-Erweiterungsregeln wie die vollständige `!DAO!`-Syntax und die universelle Frequenzschreibweise sind eigenständige Themen; hier wird nur ihre Platzierung im Mic-E-Statustext behandelt.
