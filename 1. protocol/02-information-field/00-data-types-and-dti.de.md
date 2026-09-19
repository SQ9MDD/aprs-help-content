---
title: Datentypen und DTI
description: Wie das APRS-Information-Feld aufgebaut ist, welche Hauptdatentypen das Protokoll definiert, was der Data Type Identifier ist und warum DTI nicht dasselbe wie ein APRS-Datentyp ist.
template: doc
tableOfContents: true
---

Die eigentlichen APRS-Daten werden im **Information**-Feld eines AX.25-UI-Frames übertragen.

In einer textuellen Monitordarstellung, zum Beispiel im TNC2-Format, ist das Information-Feld der Teil hinter dem Trennzeichen `:`:

```text
SQ9MDD-4>APBOX0,WIDE1-1:!5213.45N/02100.12E...
                            ^^^^^^^^^^^^^^^^^^^^^^
                            Information-Feld
```

Das Trennzeichen `:` selbst ist kein zusätzliches Byte des AX.25-Information-Feldes. Es gehört zur textuellen Darstellung des Frames.

APRS definiert kein einziges universelles Format für das gesamte Information-Feld. Position, Nachricht, Wetterbericht, Telemetrie oder Objekt werden unterschiedlich codiert. Vor der Interpretation muss ein Empfänger daher feststellen, mit welcher Art und Variante von Daten er es zu tun hat.

Der grundlegende Mechanismus dafür ist der **Data Type Identifier**, kurz **DTI**.

## Allgemeines Modell des Information-Feldes

APRS Protocol Reference 1.0.1 beschreibt das allgemeine Modell des Information-Feldes als:

```text
DTI | APRS Data | APRS Data Extension | Comment
```

Das bedeutet nicht, dass jedes Paket alle vier Teile enthalten muss.

Im klassischen Modell:

- `DTI` ist 1 Byte lang,
- `APRS Data` hat eine vom Format abhängige Länge,
- die klassische `APRS Data Extension` ist, falls vorhanden, 7 Byte lang,
- `Comment` ist optional und seine Länge hängt vom jeweiligen Format ab.

`APRS Data Extension` ist ein Begriff aus der Basisspezifikation und umfasst klassische Erweiterungen wie `course/speed`, `PHG`, `RNG` oder DF-Signaldaten. Diese 7 Byte dürfen jedoch nicht als einziger Platz für alle späteren APRS-Erweiterungen verstanden werden. Spätere Spezifikationen führten zusätzliche Erweiterungen ein, die im Kommentar codiert werden, zum Beispiel `!DAO!` oder Base91 Comment Telemetry.

Auch der Kommentar ist nicht in jedem Paket einfach nur "der restliche Text". In manchen Formaten kann er weitere Daten mit eigener definierter Syntax enthalten.

## Hauptdatentypen von APRS

Die APRS Protocol Reference unterscheidet **10 Hauptdatentypen von APRS**. Dies ist eine semantische Klassifikation und beantwortet vor allem die Frage:

**Was überträgt dieses Paket?**

Es ist keine Liste von DTI-Werten. Ein Datentyp kann mehrere unterschiedliche DTIs verwenden, und manche DTIs haben historisch überladene Bedeutungen.

| APRS-Datentyp | Typische DTI und Formate | Bedeutung |
|---|---|---|
| **Position** | `!`, `=`, `/`, `@`, Mic-E `` ` `` und `'`, rohe NMEA-Daten `$` | Position der Station. Die vier grundlegenden Positions-DTIs unterscheiden Timestamp und Messaging-Fähigkeit. Mic-E besitzt eine eigene Codierung, während `$` rohe NMEA-Sätze transportieren kann. |
| **Direction Finding** | gewöhnlich `!`, `=`, `/`, `@`; historisch auch `%` | Ein Standard-DF-Bericht ist eine Variante eines Positionsberichts mit passendem Symbol und DF-Daten, z. B. `/BRG/NRQ`. `%` wurde vom Format Agrelo DFJr / MicroFinder verwendet. |
| **Objects and Items** | `;` für Object, `)` für Item | Beschreibt ein Element, das nicht die Quellstation selbst ist. Object und Item haben getrennte DTIs und unterschiedliche Syntax. |
| **Weather** | `_`, außerdem `!`, `=`, `/`, `@`; historisch `!`, `#`, `$`, `*` für rohe WX-Formate | `_` kennzeichnet einen Wetterbericht ohne Position. Wetter kann auch an einen Positionsbericht angehängt werden. Alte Rohformate von Wetterstationen erscheinen weiterhin in der Dokumentation, werden aber von APRS 1.1 für neue Implementierungen nicht empfohlen. |
| **Telemetry** | `T` im klassischen Format | Klassische Telemetrie verwendet `T#...`-Pakete. Telemetrie kann jedoch auch an anderen Stellen vorkommen, historisch in Mic-E oder später als Base91 Comment Telemetry im Positionskommentar. |
| **Messages, Bulletins and Announcements** | `:` | Alle verwenden denselben DTI. Ob die Daten eine normale Nachricht, ein Bulletin oder Announcement sind, ergibt sich aus der folgenden Syntax. |
| **Queries** | `?` für allgemeine Queries; `:` für gerichtete Queries | Allgemeine Queries beginnen mit `?`. Eine Query an eine konkrete Station wird als APRS-Nachricht codiert und verwendet deshalb DTI `:`. |
| **Responses** | kein einzelner eigener DTI | Eine Antwort verwendet das Format der zurückgegebenen Daten, z. B. Position, Objekt, Wetter, Status oder Nachricht. Eine Station-Capabilities-Antwort verwendet DTI `<`. |
| **Status** | `>` | Einzeiliger Stationsstatus. Er kann normalen Text und in definierten Formaten zusätzliche Informationen enthalten. |
| **Other** | unter anderem `{`, `}`, `,` | Andere Mechanismen wie User-Defined Format, third-party traffic und ausdrücklich als invalid/test markierte Daten. |

Schon diese Tabelle zeigt eine der wichtigsten Eigenschaften von APRS:

**Datentyp und DTI stehen nicht in einer Eins-zu-eins-Beziehung.**

## Was ist DTI?

Der **Data Type Identifier** ist das erste Byte des Information-Feldes und bestimmt, wie der restliche Inhalt interpretiert werden muss.

In der Praxis arbeitet DTI als erster Selektor für den Parser.

Beispiel:

```text
!5213.45N/02100.12E...
```

beginnt mit `!`, daher erwartet der Parser ein Positionsformat ohne Timestamp und ohne Deklaration von APRS-Messaging-Fähigkeit.

```text
>QRV 145.500 MHz
```

beginnt mit `>`, daher werden die folgenden Daten als Status interpretiert.

```text
;REPEATER*...
```

beginnt mit `;`, daher folgt der restliche Inhalt dem APRS-Objektformat.

```text
:SP9XYZ  :Test
```

beginnt mit `:`, daher wird es gemäß dem APRS-Nachrichtenformat interpretiert.

DTI ist also nicht einfach der Name eines Datentyps. Er wählt eine konkrete Syntaxvariante und kann gleichzeitig zusätzliche Informationen tragen.

### DTI sollte das erste Byte sein

Das ursprüngliche APRS 1.0.1 enthielt eine Ausnahme für DTI `!`, die mit alten X1J-Digipeatern zusammenhing. Nach dieser alten Regel konnte `!` sogar bis zur 40. Position des Information-Feldes stehen.

APRS 1.1 entfernte diese Ausnahme später. Heute sollte `!`, wie die anderen DTIs, am Anfang des Information-Feldes stehen.

Neue Implementierungen sollten die historische X1J-Variante nicht erzeugen.

## Ein Datentyp, mehrere DTIs

Das klarste Beispiel ist **Position**.

APRS besitzt vier grundlegende DTIs für normale Positionsberichte:

| DTI | Timestamp | APRS messaging |
|---|---|---|
| `!` | nein | nein |
| `=` | nein | ja |
| `/` | ja | nein |
| `@` | ja | ja |

Alle vier gehören zum selben Hauptdatentyp:

```text
Position
```

wählen aber unterschiedliche Varianten des Formats.

Als Matrix:

| | Ohne Timestamp | Mit Timestamp |
|---|---|---|
| Ohne APRS messaging | `!` | `/` |
| Mit APRS messaging | `=` | `@` |

DTI übermittelt hier also gleichzeitig:

1. dass das Paket ein Positionsbericht ist,
2. ob das Format einen Timestamp enthält,
3. ob die Station APRS-Messaging-Fähigkeit deklariert.

Dies zeigt gut, warum die Bezeichnung "Frame-Typ" für jeden einzelnen DTI zu stark vereinfacht.

## Capabilities - zwei unterschiedliche Begriffe

In APRS gibt es zwei Mechanismen im Zusammenhang mit Stationsfähigkeiten, die leicht verwechselt werden.

### Messaging capability im Positions-DTI

Die DTIs `=` und `@` bedeuten einen Positionsbericht **with APRS messaging**.

Die DTIs `!` und `/` bedeuten die entsprechenden Varianten **without APRS messaging**.

Diese Information ist Bestandteil der Bedeutung des jeweiligen Positions-DTI. Sie ist kein separater Datenblock, der an den Bericht angehängt wird.

### Station Capabilities und DTI `<`

Ein davon getrennter Mechanismus ist das formale **Station Capabilities**-Paket mit DTI:

```text
<
```

APRS101 beschreibt Capabilities als kommagetrennte Liste von Tokens:

```text
TOKEN
```

oder:

```text
TOKEN=VALUE
```

Klassisches Beispiel:

```text
<IGATE,MSG_CNT=43,LOC_CNT=14
```

In APRS101 ist ein solches Paket eine Antwort auf die Query `?IGATE?`.

Daher dürfen:

```text
= oder @
```

also die Messaging-Fähigkeit als Bestandteil einer Positionsbericht-Variante,

nicht mit:

```text
<
```

also einem separaten Station-Capabilities-Format,

verwechselt werden.

Mic-E besitzt außerdem eigene Type- und Geräteidentifikationscodes. Dies ist wiederum ein unabhängiger Mechanismus und darf weder mit DTI `<` noch mit der Messaging-Fähigkeit von Positionsberichten vermischt werden.

## DTI bestimmt nicht immer die vollständige Semantik

DTI ist der primäre Einstiegspunkt für den Parser, aber ein einzelnes Zeichen bestimmt nicht immer alles eindeutig.

Beispiele:

- `:` bedeutet Nachrichtenformat, doch erst die folgenden Felder unterscheiden normale Nachricht, Bulletin, Announcement oder directed query,
- `!`, `=`, `/` und `@` können normale Position, DF-Daten oder an die Position angehängte Wetterdaten transportieren,
- `!` und `$` haben zusätzlich alte überladene Bedeutungen im Zusammenhang mit Rohformaten von Wetterstationen,
- eine Antwort auf eine Query hat keinen einheitlichen DTI, weil die Antwort z. B. Position, Status, Wetter, Objekt oder Station Capabilities sein kann,
- Mic-E verwendet gleichzeitig DTI, Information-Feld und codierte Daten in der Destination Address.

Ein korrekter APRS-Parser kann seine Analyse deshalb nicht bei einem einfachen `switch(DTI)` beenden. DTI wählt eine Regelfamilie, aber das konkrete Format kann weitere Erkennung erfordern.

## Vollständige DTI-Tabelle aus APRS101

Die folgende Tabelle zeigt die in APRS Protocol Reference 1.0.1 definierten Bedeutungen zusammen mit den wichtigsten heutigen Hinweisen.

| DTI | Bedeutung | Hinweise |
|---|---|---|
| `0x1c` | Current Mic-E Data, Rev 0 beta | Sehr altes Betaformat. |
| `0x1d` | Old Mic-E Data, Rev 0 beta | Sehr altes Betaformat. |
| `!` | Position ohne Timestamp, no APRS messaging | Historisch auch Ultimeter 2000 WX. Die alte X1J-Ausnahme, die `!` später im Feld erlaubte, wurde entfernt. |
| `"` | Unused | Nicht als neuen DTI verwenden. |
| `#` | Peet Bros U-II Weather Station | Rohes historisches WX-Format. Neue Implementierungen sollten es nicht wählen. |
| `$` | Raw GPS data oder Ultimeter 2000 | Bei GPS typischerweise rohe NMEA-Sätze. Historisch durch ein WX-Format überladen. |
| `%` | Agrelo DFJr / MicroFinder | Historisches spezialisiertes Direction-Finding-Format. |
| `&` | Reserved - Map Feature | In APRS101 reserviert. |
| `'` | Mic-E | APRS101 beschreibt ihn als Old Mic-E, aber der Kenwood TM-D700 verwendete ihn für Current Mic-E. Praktisch nach Mic-E-Regeln behandeln. |
| `(` | Unused | Nicht als neuen DTI verwenden. |
| `)` | Item | Item-Format ist definiert, APRS 1.1 empfiehlt seine Verwendung auf RF wegen Kompatibilitätsproblemen jedoch nicht. |
| `*` | Peet Bros U-II Weather Station | Rohes historisches WX-Format. |
| `+` | Reserved - Shelter data with time | In APRS101 reserviert. |
| `,` | Invalid data oder test data | Paket bewusst als nicht standardkonforme oder Testdaten markiert. |
| `-` | Unused | Nicht als neuen DTI verwenden. |
| `.` | Reserved - Space weather | In APRS101 reserviert. |
| `/` | Position mit Timestamp, no APRS messaging | Einer der vier grundlegenden Positions-DTIs. |
| `0`-`9` | Do not use | Nicht für DTI vorgesehen. |
| `:` | Message | Derselbe DTI wird für Nachrichten, Bulletins, Announcements und directed queries verwendet. |
| `;` | Object | APRS-Objektbericht. |
| `<` | Station Capabilities | Capability-Tokenformat, z. B. `<IGATE,MSG_CNT=43,LOC_CNT=14`. |
| `=` | Position ohne Timestamp, with APRS messaging | Einer der vier grundlegenden Positions-DTIs. |
| `>` | Status | Statusbericht. |
| `?` | Query | General query. Directed queries verwenden Nachrichtenformat `:`. |
| `@` | Position mit Timestamp, with APRS messaging | Einer der vier grundlegenden Positions-DTIs. |
| `A`-`S` | Do not use | Für neue DTIs nicht vorgesehener Bereich. |
| `T` | Telemetry data | Klassisches Telemetrieformat `T#...`. |
| `U`-`Z` | Do not use | Für neue DTIs nicht vorgesehener Bereich. |
| `[` | Maidenhead grid locator beacon | In APRS101 als obsolete markiert. |
| `\` | Unused | Nicht als neuen DTI verwenden. |
| `]` | Unused | Nicht als neuen DTI verwenden. |
| `^` | Unused | Nicht als neuen DTI verwenden. |
| `_` | Weather Report ohne Position | Standardmäßiger positionless weather report. |
| `` ` `` | Current Mic-E Data | Standard-DTI für Mic-E. APRS101 nennt eine Ausnahme im Zusammenhang mit TM-D700. |
| `a`-`z` | Do not use | Nicht für DTI vorgesehen. |
| `{` | User-Defined APRS packet format | Reservierter Mechanismus für anwendungsdefinierte Formate. |
| `|` | Do not use als DTI | In APRS101 mit historischem TNC stream switching verbunden. Spätere Erweiterungen können `|` innerhalb der Daten verwenden, z. B. Base91 Comment Telemetry. |
| `}` | Third-party traffic | Wrapper für ein APRS-Paket, das über ein zwischengeschaltetes Netz transportiert wurde. Das innere Paket besitzt eigenen Header und eigene APRS-Daten. |
| `~` | Do not use als DTI | Historisch mit TNC stream switching verbunden. |

Die APRS101-DTI-Tabelle spiegelt den Stand des Protokolls um das Jahr 2000 wider. Spätere Addenda korrigierten einige Empfehlungen und kennzeichneten ältere Mechanismen als veraltet oder nicht empfohlen.

## Weather zeigt, warum die DTI-Tabelle allein nicht genügt

Wetter ist ein besonders gutes Beispiel für die geschichtete Struktur von APRS.

Ein Bericht ohne Position kann beginnen mit:

```text
_
```

Wetterdaten können außerdem an einen Positionsbericht angehängt sein, der mit:

```text
!
=
/
@
```

beginnt.

Zusätzlich definierte APRS101 rohe Formate bestimmter Wetterstationen mit:

```text
!
#
$
*
```

APRS 1.1 empfiehlt, dass neue Geräte keine rohen Wetterstationsformate senden, sondern die Daten in Standard-APRS-Formate umwandeln.

Der DTI-Wert allein kann daher erfordern, den folgenden Inhalt sowie historische Varianten zu berücksichtigen.

## Telemetrie hat ebenfalls nicht immer DTI `T`

Klassische APRS-Telemetrie hat die Form:

```text
T#001,123,045,255,010,100,10110011
```

und verwendet DTI:

```text
T
```

Das bedeutet jedoch nicht, dass alle Telemetriedaten in APRS in einem `T`-Paket vorkommen müssen.

Historische Mic-E-Telemetrie nutzte den Mic-E-Mechanismus und wurde später nach Einführung der Mic-E Type Codes als veraltet betrachtet.

Die spätere **Base91 Comment Telemetry** erlaubt Telemetriedaten direkt im Kommentar eines Positionsberichts, zum Beispiel zwischen `|...|`. In diesem Fall bleibt der DTI des gesamten Pakets der Positions-DTI, z. B. `!`, `=` oder Mic-E, während die Telemetrie als Erweiterung tiefer in den Daten liegt.

Dies zeigt erneut:

**Ein APRS-Datentyp ist nicht dasselbe wie der DTI des ersten Bytes.**

## User-Defined APRS packet format

Der DTI:

```text
{
```

ist formal für das **User-Defined APRS packet format** vorgesehen.

APRS101 definiert den Beginn eines solchen Pakets als:

```text
{UX...
```

wobei:

- `{` der DTI ist,
- `U` eine ein Byte lange User ID ist,
- `X` eine ein Byte lange Kennung des konkreten Pakettyps dieses Autors ist,
- danach die vom Autor definierten Formatdaten folgen.

Beispiel aus APRS101:

```text
{Q1qwerty
```

bedeutet:

```text
DTI:         {
User ID:     Q
Packet Type: 1
Data:        qwerty
```

Für Experimente wurde eine User ID gleich `{` vorgesehen, daher kann ein experimentelles Paket beginnen mit:

```text
{{
```

Beispiel:

```text
{{zasdfg
```

Im späteren Register experimenteller Formate erschienen unter anderem Header wie:

```text
{BT
{BP
{Dx
{KY
{Q1
{Q2
{Sx
```

User-Defined Format bedeutet nicht, dass jedes APRS-Programm ein solches Paket verstehen muss. APRS101 geht ausdrücklich davon aus, dass diese Formate optional sind und eine Anwendung unbekannte User IDs und Packet Types sicher ignorieren soll.

Der `{`-Mechanismus ist damit eine kontrollierte Möglichkeit, APRS zu erweitern, ohne nicht standardisierte Daten als einen der Standardformate auszugeben.

## Third-party traffic und DTI `}`

Der DTI:

```text
}
```

bedeutet **third-party traffic**.

In diesem Format enthält das Information-Feld ein eingepacktes originales APRS-Paket einschließlich Header und Daten.

Schematisch:

```text
}SOURCE>DEST,PATH:DTI...
```

Der erste DTI des äußeren Pakets ist `}`, aber das innere Paket besitzt seinen eigenen DTI und muss erneut gemäß dessen Format interpretiert werden.

Dies ist ein Sonderfall, bei dem der Parser nach Erkennung von `}` wieder die Analyse eines vollständigen APRS-Pakets innerhalb der Daten beginnt.

## Zeichen `|` und `~` - historische Einschränkung

APRS101 markiert `|` und `~` als Zeichen, die nicht als DTI verwendet werden sollen, weil alte TNCs sie zum Umschalten von Streams verwendeten.

In der ursprünglichen Spezifikation wurde diese Einschränkung auch breiter im Zusammenhang mit Kommentaren behandelt.

Die spätere Base91 Comment Telemetry-Spezifikation verwendet jedoch absichtlich:

```text
|...|
```

als Begrenzer für Telemetriedaten in einem Kommentar.

Die Autoren dieser Erweiterung weisen darauf hin, dass die Einschränkung aus dem sehr alten CONVerse-Modus klassischer TNCs stammt, während das Zeichen `|` in der Praxis moderne APRS-Netze und APRS-IS korrekt durchläuft.

Ein moderner Parser sollte daher nicht die alte Regel anwenden, dass `|` niemals in APRS-Daten vorkommen darf. Es ist weiterhin kein Standard-DTI, kann aber innerhalb eines konkreten Formats oder einer Erweiterung eine definierte Bedeutung besitzen.

## Praktische Regel

Die sicherste Methode zur Interpretation des Information-Feldes lautet:

1. erstes Byte als DTI lesen,
2. die durch DTI angegebene Formatfamilie wählen,
3. berücksichtigen, dass ein Hauptdatentyp mehrere DTIs besitzen kann,
4. zusätzliche DTI-Semantik berücksichtigen, z. B. Timestamp und APRS messaging bei Positionsberichten,
5. bei überladenen oder historischen Formaten die folgende Syntax prüfen,
6. klassische Data Extension nur dort parsen, wo das gewählte Format sie vorsieht,
7. nach den Hauptdaten Erweiterungen und Informationen im Kommentar berücksichtigen,
8. bei `{` die Regeln der konkreten User ID und des Packet Type anwenden,
9. bei `}` das innere Paket auspacken und dessen DTI von vorn interpretieren,
10. nicht annehmen, dass ein unbekannter DTI sicher als Klartext behandelt werden kann.

DTI ist damit der erste Schlüssel zur Dekodierung des Information-Feldes, doch die vollständige Bedeutung eines Pakets ergibt sich aus der Kombination von:

```text
Hauptdatentyp
+
DTI
+
Formatvariante
+
optionale Capabilities
+
Erweiterungen
+
Kommentar
```

## Quellen

Dieser Artikel basiert auf:

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [APRS 1.2 Addendum Proposals](https://www.aprs.org/aprs12.html)
- [APRS User Defined Data Formats List](https://www.aprs.org/aprs11/expfmts.txt)
- [APRS Documentation Project - APRS 1.2 compilation](https://github.com/wb2osz/aprsspec)
- [Base91 Comment Telemetry specification](https://github.com/hessu/aprs-specs/blob/master/aprs-base91-comment-telemetry.txt)
