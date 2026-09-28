---
title: APRS-Symbole
description: Codierung und Interpretation von APRS-Symbolen, Symboltabellen, Overlays, praktische Anwendungen und vollständige Übersicht aller 188 Symbole.
template: doc
tableOfContents: true
---

Ein APRS-Symbol ist nicht nur ein Bild, das eine Position auf einer Karte kennzeichnet. Es ist zugleich **ein kompakter Informationsträger für Art, Funktion und Eigenschaften einer Station oder eines Objekts**. Ein geeignetes Symbol kann ein Fahrzeug, einen Digipeater oder eine Heimstation kennzeichnen. Ein Overlay kann beispielsweise die Stromversorgung, die Funktionen eines Geräts oder die Art einer Aktivität im Gelände näher beschreiben.

APRS überträgt **den Symbolcode und nicht die Grafik**. In typischen Positionsmeldungen genügen zwei Zeichen: eine Tabellenkennung und ein Symbolzeichen. Ein Overlay fügt kein drittes Zeichen hinzu, sondern nimmt die Stelle der Kennung für die Alternativtabelle ein. Die empfangende Anwendung interpretiert diese Kombination anhand ihrer eigenen Symboltabelle. So lassen sich zusätzliche Informationen übertragen, ohne die Meldung um ein weiteres Zeichen zu verlängern.

## Codierung eines Symbols in einer Positionsmeldung

In einer unkomprimierten Positionsmeldung steht das Tabellenzeichen unmittelbar hinter dem Breitengrad und das Symbolzeichen unmittelbar hinter dem Längengrad:

```text
SQ9MDD>APRS:!5003.50N/01956.00E>
                     ^         ^
                     |         |
                  Tabelle    Symbol
```

Das Zeichenpaar `/>` bezeichnet ein Auto: `/` wählt die Primärtabelle, `>` das Symbol innerhalb dieser Tabelle. Die gezeigte Zeile ist eine Textdarstellung eines Pakets und keine Aufzeichnung sämtlicher Bytes des AX.25-Funkrahmens.

| Element | Funktion |
|---|---|
| **Tabellenzeichen** | Wählt die Primärtabelle, Alternativtabelle oder eine Variante eines alternativen Symbols mit Overlay. |
| **Symbolzeichen** | Bezeichnet den Eintrag in der ausgewählten Tabelle und bestimmt zusammen mit einem möglichen Overlay die Bedeutung des Codes. |
| **Grafik** | Wird von der empfangenden Anwendung gespeichert oder erzeugt; sie wird nicht im Paket übertragen. |

Symbole werden auch in anderen Formaten von Positionsmeldungen verwendet, unter anderem bei komprimierten Positionen und Mic-E. Position und Codierung der Zeichen können abweichen, es geht jedoch weiterhin um die Kennzeichnung eines Symbols und nicht um die Übertragung einer Bitmap.

## Primär- und Alternativtabelle

Die beiden APRS-Tabellen enthalten jeweils 94 Einträge:

- `/` wählt die **Primärtabelle**, die unter anderem für typische Stationen und Fahrzeuge verwendet wird;
- `\` wählt die **Alternativtabelle**, die auch Symbolfamilien umfasst, die durch Overlays erweitert werden.

Die Bedeutung ergibt sich aus beiden Zeichen. Beispielsweise ist `/>` ein Auto aus der Primärtabelle, während `\>` das Basissymbol für ein Fahrzeug aus der Alternativtabelle bezeichnet. Entsprechend steht `/-` für ein Haus aus der Primärtabelle; `\-` ist ein alternatives Symbol, dessen Overlays Eigenschaften einer Heimstation beschreiben können.

## Overlays und zusätzliche Bedeutung

Ein **Overlay** ist ein Buchstabe `A`-`Z` oder eine Ziffer `0`-`9`, der beziehungsweise die **das Zeichen `\` ersetzt**. Es handelt sich weder um ein zusätzliches Feld noch wird dadurch der aus zwei Zeichen bestehende Symbolcode verlängert.

```text
Alternativtabelle:     \-    Haus (Basissymbol)
Mit Overlay S:         S-    Haus mit Solarversorgung

                       ^
                       Overlay statt Tabellenzeichen
```

Das Basissymbol legt die Kategorie fest, das Overlay kann eine bestimmte Variante kennzeichnen. Seine Bedeutung **hängt vom Symbolzeichen ab**: `S-` bezieht sich auf die Stromversorgung eines Hauses, `S;` kennzeichnet eine SOTA-Aktivität und `S#` beschreibt eine Digipeater-Funktion. Es gibt kein universelles Wörterbuch, in dem ein Buchstabe bei allen Symbolen dieselbe Bedeutung hat.

Die Erweiterung aus dem Jahr 2007 sieht Overlays für alle alternativen Symbole vor, doch **nicht jede Kombination hat eine festgelegte Bedeutung**. Man sollte Kombinationen nicht eigenmächtig interpretieren und nicht davon ausgehen, dass andere Anwendungen eine nicht definierte Kombination erkennen. Der Mechanismus erlaubt sehr viele Kombinationen, deren Nutzen aber von abgestimmten Definitionen und ihrer Implementierung abhängt.

Ursprünglich war ein Overlay als Ziffer oder Buchstabe gedacht, der über das Basissymbol gelegt wird. Die Dokumentation erlaubt jedoch auch eine vollständig andere Grafik für eine bestimmte Kombination, wenn sich ihre Bedeutung so verständlicher darstellen lässt.

## Praktische Verwendung von Symbolen und Overlays

### Heimstation: Stromversorgung und Anwesenheit des Operators

![Basissymbol für ein Haus aus der Alternativtabelle](./_img/verG/a12.gif)

Die Familie des alternativen Haussymbols `\-` zeigt besonders deutlich, dass ein Symbol mehr als nur die Art eines Objekts vermitteln kann. Laut der bereitgestellten Erweiterungsliste sind folgende Kombinationen möglich:

| Code | Bedeutung |
|---|---|
| `\-` | Haus, alternatives Basissymbol; historisch zur Kennzeichnung einer HF-Station verwendet. |
| `B-` | Batterieversorgung oder Betrieb unabhängig vom öffentlichen Stromnetz. |
| `C-` | Kombination alternativer Energiequellen. |
| `E-` | Notstromversorgung bei Ausfall des Stromnetzes. |
| `G-` | Geothermische Energie. |
| `H-` | Wasserkraft. |
| `S-` | Solarenergie. |
| `W-` | Windenergie. |
| `O-` | Operator an der Station anwesend. |
| `5-`, `6-` | Für die jeweilige Region unübliche Netzfrequenz: 50 beziehungsweise 60 Hz. |

Beispiel einer Positionsmeldung einer Heimstation mit dem Overlay `S`:

```text
SQ9MDD>APRS:!5003.50NS01956.00E-
```

Hier ersetzt das `S` unmittelbar nach dem Breitengrad das Zeichen `\`, während das abschließende `-` das Haussymbol festlegt. **Die beiden Zeichen `S-` genügen, um eine Solarversorgung anzugeben.** Dies ist weder ein zusätzliches Feld noch eine Erweiterung des Kommentars oder ein drittes Symbolzeichen.

Diese Angabe beschreibt eine erklärte Eigenschaft der Station und ist keine Messung ihres aktuellen Zustands. `E-` beweist nicht, dass die Notstromversorgung gerade läuft; ebenso ist `S-` keine Telemetrie über die Energieerzeugung. Ein Symbolcode enthält nur ein Overlay. Sind mehrere Eigenschaften wichtig, sollten sie durch einen Kommentar, Telemetrie oder andere vorgesehene APRS-Verfahren ergänzt werden.

**Historischer Hinweis:** In älteren Listen stand `C-` für einen Amateurfunkclub. In der bereitgestellten Revision der Datei `symbols-new.txt` von 2017 bezeichnet `C-` dagegen eine Kombination alternativer Energiequellen; **der Amateurfunkclub wurde nach `Ch` verschoben** (Gebäudefamilie `\h`). Beim Vergleich älterer Programme und Tabellen muss diese Änderung berücksichtigt werden.

### Digipeater: Informationen über die Funktion

![Digipeater-Basissymbol aus der Alternativtabelle](./_img/verG/a02.gif)

Ein Digipeater-Overlay kann nicht nur den Gerätetyp, sondern auch seine Funktion kennzeichnen:

| Code | Bedeutung laut Erweiterungsliste |
|---|---|
| `/#` | Allgemeines Digipeater-Symbol aus der Primärtabelle. |
| `1#` | WIDE1-1-Digipeater. |
| `A#` | Digipeater mit alternativem Eingang, beispielsweise auf einer anderen Frequenz. |
| `E#` | Digipeater mit Notstromversorgung. |
| `I#` | Digipeater mit zusätzlicher IGate-Funktion. |
| `V#` | Digipeater, der das Viscous-Verfahren nutzt. |

`I#` bedeutet nicht dasselbe wie das eigenständige Symbol für ein APRS-IS-Gateway. Der erste Code bezeichnet einen Digipeater mit Zusatzfunktion, während der zweite zu einer anderen Symbolfamilie für Gateways gehört. Die Wahl hängt davon ab, welche Funktion auf der Karte hervorgehoben werden soll.

### IGate: Richtung und Art der Weiterleitung

![Gateway-Basissymbol aus der Alternativtabelle](./_img/verG/a05.gif)

Die Familie `\&` kann die Funktion eines Gateways beschreiben:

| Code | Bedeutung |
|---|---|
| `I&` | Allgemeines IGate-Symbol; die Dokumentation empfiehlt nach Möglichkeit ein genaueres Overlay. |
| `R&` | Reines Empfangs-IGate auf der HF-Seite, ohne Weiterleitung von Nachrichten zurück zum Funkkanal. |
| `T&` | Sendendes IGate mit auf einen Hop begrenztem Nachrichtenpfad. |
| `2&` | Sendendes IGate mit einem Nachrichtenpfad über zwei Hops; laut Liste ist dies im Allgemeinen nicht empfehlenswert. |

Das Symbol hilft also dabei, eine reine Empfangsstation von einem Gateway zu unterscheiden, das auch Nachrichten zum Funkkanal weiterleitet. Es beschreibt jedoch die deklarierte Funktion und bestätigt weder den aktuellen Verbindungszustand zu APRS-IS noch die korrekte Konfiguration.

### Aktivitäten im Gelände und Veranstaltungen

Verschiedene Overlays der Symbolfamilie für portablen Betrieb kennzeichnen unterschiedliche Aktivitäten:

| Code | Bedeutung |
|---|---|
| `/;` | Basissymbol für portablen Betrieb / einen Lagerplatz. |
| `F;` | Field Day. |
| `I;` | IOTA (*Islands on the Air*). |
| `S;` | SOTA (*Summits on the Air*). |
| `W;` | WOTA (*Wainwrights on the Air*). |

Dies ist ein Beispiel für Overlay-Bedeutungen, die weder mit der Ausstattung noch mit der Stromversorgung zusammenhängen. Dasselbe Basissymbol kann zeigen, an welcher Aktivität eine Station teilnimmt.

### Fahrzeuge und besondere Einrichtungen

In der Fahrzeugfamilie `\>` steht beispielsweise `B>` für ein batteriebetriebenes Fahrzeug, `P>` für einen Plug-in-Hybrid und `S>` für ein solarbetriebenes Fahrzeug. In der Familie der Schutzunterkünfte `\z` bezeichnet `Ez` eine Einrichtung mit Notstromversorgung und `Tz` eine medizinische Sichtungsstelle (*Triage*). So lassen sich besondere Eigenschaften oder Funktionen kennzeichnen, ohne die Symboldarstellung zu erweitern.

Das gewählte Symbol sollte die tatsächliche Rolle des Objekts angeben. Symbole für Einsatzkräfte, Vorfälle oder Gefahren sollten nicht allein wegen ihrer grafischen Wirkung verwendet werden.

## Symbole auswählen und interpretieren

Ein Symbol sollte in erster Linie **die aktuelle Rolle der Station oder des Objekts** wiedergeben. Typische Möglichkeiten sind `/[` für eine Person zu Fuß, `/>` für ein Auto, `/b` für ein Fahrrad, `/-` für eine Heimstation, `/#` für einen Digipeater, `/r` für eine Relaisstation und `/_` für eine Wetterstation. Zusätzliche Details sollten nur dann mit einem Overlay codiert werden, wenn für die Kombination eine dokumentierte Bedeutung existiert.

Zum Lesen einer unkomprimierten Positionsmeldung sucht man das Tabellenzeichen unmittelbar nach dem Breitengrad und das Symbolzeichen nach dem Längengrad. Danach werden **beide Zeichen gemeinsam als ein Code** interpretiert. Beispielsweise enthält `!5003.50N/01956.00E>` das Autosymbol `/>`, während `!5003.50NS01956.00E-` den Code `S-` einer solarversorgten Heimstation enthält.

Das Symbol muss von den übrigen Meldungsdaten unterschieden werden. Es bezeichnet eine Kategorie oder Eigenschaft, ersetzt aber weder den Kommentar noch Telemetrie oder Angaben über den tatsächlichen Zustand. Wenn eine Eigenschaft betrieblich wichtig ist, sollte sie auch für Benutzer verständlich sein, deren Software die neuesten Overlays nicht unterstützt.

## Warum dasselbe Symbol unterschiedlich aussehen kann

Die Symbolgrafiken werden lokal in der jeweiligen Anwendung gespeichert oder erzeugt. Ein Programm mit einer älteren Tabelle kann eine neue Kombination nur als Basissymbol anzeigen, den Overlay-Buchstaben weglassen oder die Kombination anders darstellen als ein moderner Client. Auch vollständig kompatible Anwendungen können unterschiedliche Grafikstile nutzen, ohne die Bedeutung des Codes zu verändern.

Die Erweiterungsdokumentation betont die Kompatibilität: Zu häufige Änderungen der Symbolbedeutungen und fehlende Overlay-Unterstützung können zu unterschiedlichen Interpretationen desselben Pakets führen. Daher sollte Software **beide empfangenen Symbolzeichen beibehalten**, selbst wenn sie eine Kombination noch nicht grafisch darstellen kann. Ein unbekanntes Overlay sollte nicht dazu führen, dass eine gültige Positionsmeldung verworfen wird.

Im praktischen Betrieb sollte man sich nicht ausschließlich auf ein ungewöhnliches Symbol verlassen. Wichtige Informationen, etwa die Funktion des Objekts, seine Frequenz oder die Verfügbarkeit von Diensten, lassen sich zusätzlich in einem geeigneten APRS-Kommentar angeben.

## Vollständige Symboltabelle

Die folgende Übersicht enthält alle 188 Einträge der bereitgestellten Tabellen: 94 aus der Primär- und 94 aus der Alternativtabelle. Die Zeilen sind nach demselben Symbolzeichen gruppiert. Die Grafiken stammen aus dem lokalen Satz `_img/verG` und zeigen die Basissymbole, nicht sämtliche möglichen Overlay-Varianten.

**Legende:** „frei“ bezeichnet einen in der angegebenen Liste nicht belegten Eintrag. „Overlay-Basis“ kennzeichnet ein Symbol, dessen Bedeutung durch einen Buchstaben oder eine Ziffer präzisiert werden kann.

| Primärcode | Symbol | Bedeutung | Alternativcode | Symbol | Bedeutung |
|---|:---:|---|---|:---:|---|
| `/!` | ![Polizei / Sheriff](./_img/verG/00.gif) | Polizei / Sheriff. | `\!` | ![Notruf / Alarm](./_img/verG/a00.gif) | Notruf / Alarm; Overlay-Basis. |
| `/"` | ![Reserviert (früher Regen)](./_img/verG/01.gif) | Reserviert (früher Regen). | `\"` | ![Reserviert](./_img/verG/a01.gif) | Reserviert. |
| `/#` | ![Digipeater](./_img/verG/02.gif) | Digipeater. | `\#` | ![Digipeater mit Overlay / grüner Stern](./_img/verG/a02.gif) | Digipeater mit Overlay / grüner Stern. |
| `/$` | ![Telefon](./_img/verG/03.gif) | Telefon. | `\$` | ![Bank oder Geldautomat](./_img/verG/a03.gif) | Bank oder Geldautomat. |
| `/%` | ![DX-Cluster](./_img/verG/04.gif) | DX-Cluster. | `\%` | ![Kraftwerk](./_img/verG/a04.gif) | Kraftwerk; Overlay-Basis. |
| `/&` | ![HF-Gateway](./_img/verG/05.gif) | HF-Gateway. | `\&` | ![Gateway](./_img/verG/a05.gif) | Gateway; IGate-Overlays. |
| `/'` | ![Kleinflugzeug](./_img/verG/06.gif) | Kleinflugzeug. | `\'` | ![Unfall- / Ereignisort](./_img/verG/a06.gif) | Unfall- / Ereignisort. |
| `/(` | ![Mobile Satellitenstation](./_img/verG/07.gif) | Mobile Satellitenstation. | `\(` | ![Bewölkung](./_img/verG/a07.gif) | Bewölkung; Basis für Wolkenvarianten. |
| `/)` | ![Rollstuhl](./_img/verG/08.gif) | Rollstuhl. | `\)` | ![Firenet MEO / MODIS-Erdbeobachtung](./_img/verG/a08.gif) | Firenet MEO / MODIS-Erdbeobachtung. |
| `/*` | ![Schneemobil](./_img/verG/09.gif) | Schneemobil. | `\*` | ![Frei](./_img/verG/a09.gif) | Frei. |
| `/+` | ![Rotes Kreuz](./_img/verG/10.gif) | Rotes Kreuz. | `\+` | ![Kirche](./_img/verG/a10.gif) | Kirche. |
| `/,` | ![Boy Scouts](./_img/verG/11.gif) | Boy Scouts. | `\,` | ![Girl Scouts](./_img/verG/a11.gif) | Girl Scouts. |
| `/-` | ![Haus / VHF-QTH](./_img/verG/12.gif) | Haus / VHF-QTH. | `\-` | ![Haus (historisch HF-Station)](./_img/verG/a12.gif) | Haus (historisch HF-Station); Basis einer Overlay-Familie, darunter `O-` für anwesenden Operator. |
| `/.` | ![X-Markierung](./_img/verG/13.gif) | X-Markierung. | `\.` | ![Uneindeutige Position (großes Fragezeichen)](./_img/verG/a13.gif) | Uneindeutige Position (großes Fragezeichen). |
| `//` | ![Roter Punkt](./_img/verG/14.gif) | Roter Punkt. | `\/` | ![Zielpunkt / Wegpunkt](./_img/verG/a14.gif) | Zielpunkt / Wegpunkt. |
| `/0` | ![Kreis, veraltetes Symbol](./_img/verG/15.gif) | Kreis, veraltetes Symbol. | `\0` | ![Kreis](./_img/verG/a15.gif) | Kreis; IRLP, EchoLink, WiRES und Overlays. |
| `/1` | ![Frei / historisch nummerierter Kreis](./_img/verG/16.gif) | Frei / historisch nummerierter Kreis. | `\1` | ![Frei](./_img/verG/a16.gif) | Frei. |
| `/2` | ![Frei / historisch nummerierter Kreis](./_img/verG/17.gif) | Frei / historisch nummerierter Kreis. | `\2` | ![Frei](./_img/verG/a17.gif) | Frei. |
| `/3` | ![Frei / historisch nummerierter Kreis](./_img/verG/18.gif) | Frei / historisch nummerierter Kreis. | `\3` | ![Frei](./_img/verG/a18.gif) | Frei. |
| `/4` | ![Frei / historisch nummerierter Kreis](./_img/verG/19.gif) | Frei / historisch nummerierter Kreis. | `\4` | ![Frei](./_img/verG/a19.gif) | Frei. |
| `/5` | ![Frei / historisch nummerierter Kreis](./_img/verG/20.gif) | Frei / historisch nummerierter Kreis. | `\5` | ![Frei](./_img/verG/a20.gif) | Frei. |
| `/6` | ![Frei / historisch nummerierter Kreis](./_img/verG/21.gif) | Frei / historisch nummerierter Kreis. | `\6` | ![Frei](./_img/verG/a21.gif) | Frei. |
| `/7` | ![Frei / historisch nummerierter Kreis](./_img/verG/22.gif) | Frei / historisch nummerierter Kreis. | `\7` | ![Frei](./_img/verG/a22.gif) | Frei. |
| `/8` | ![Frei / historisch nummerierter Kreis](./_img/verG/23.gif) | Frei / historisch nummerierter Kreis. | `\8` | ![Netzwerkknoten mit 802.11 oder einem anderen Netzwerk](./_img/verG/a23.gif) | Netzwerkknoten mit 802.11 oder einem anderen Netzwerk. |
| `/9` | ![Frei / historisch nummerierter Kreis](./_img/verG/24.gif) | Frei / historisch nummerierter Kreis. | `\9` | ![Tankstelle](./_img/verG/a24.gif) | Tankstelle. |
| `/:` | ![Feuer](./_img/verG/25.gif) | Feuer. | `\:` | ![Frei (Hagel als Overlay weitergeführt)](./_img/verG/a25.gif) | Frei (Hagel als Overlay weitergeführt). |
| `/;` | ![Zeltplatz / portabler Betrieb](./_img/verG/26.gif) | Zeltplatz / portabler Betrieb. | `\;` | ![Park oder Picknickplatz](./_img/verG/a26.gif) | Park oder Picknickplatz; unterstützt Veranstaltungs-Overlays. |
| `/<` | ![Motorrad](./_img/verG/27.gif) | Motorrad. | `\<` | ![Warnhinweis / einzelne Wetterwarnflagge](./_img/verG/a27.gif) | Warnhinweis / einzelne Wetterwarnflagge. |
| `/=` | ![Lokomotive](./_img/verG/28.gif) | Lokomotive. | `\=` | ![Freie Symbolfamilie mit Overlays](./_img/verG/a28.gif) | Freie Symbolfamilie mit Overlays. |
| `/>` | ![Auto](./_img/verG/29.gif) | Auto. | `\>` | ![Fahrzeug](./_img/verG/a29.gif) | Fahrzeug; Overlay-Basis. |
| `/?` | ![Dateiserver](./_img/verG/30.gif) | Dateiserver. | `\?` | ![Informationsstelle](./_img/verG/a30.gif) | Informationsstelle. |
| `/@` | ![Vorhergesagter Positionspunkt (H/C)](./_img/verG/31.gif) | Vorhergesagter Positionspunkt (H/C). | `\@` | ![Hurrikan / tropischer Sturm](./_img/verG/a31.gif) | Hurrikan / tropischer Sturm. |
| `/A` | ![Hilfsstation](./_img/verG/32.gif) | Hilfsstation. | `\A` | ![Kasten](./_img/verG/a32.gif) | Kasten; DTMF, RFID, XO und weitere Overlays. |
| `/B` | ![BBS / PBBS](./_img/verG/33.gif) | BBS / PBBS. | `\B` | ![Frei (Schneetreiben als Overlay weitergeführt)](./_img/verG/a33.gif) | Frei (Schneetreiben als Overlay weitergeführt). |
| `/C` | ![Kanu](./_img/verG/34.gif) | Kanu. | `\C` | ![Küstenwache](./_img/verG/a34.gif) | Küstenwache. |
| `/D` | ![Frei](./_img/verG/35.gif) | Frei. | `\D` | ![Depot](./_img/verG/a35.gif) | Depot; Overlay-Basis. |
| `/E` | ![Augensymbol](./_img/verG/36.gif) | Augensymbol; Veranstaltung oder auffälliger Punkt. | `\E` | ![Rauch und weitere Sichtweitencodes](./_img/verG/a36.gif) | Rauch und weitere Sichtweitencodes. |
| `/F` | ![Landwirtschaftliches Fahrzeug / Traktor](./_img/verG/37.gif) | Landwirtschaftliches Fahrzeug / Traktor. | `\F` | ![Frei (gefrierender Regen als Overlay weitergeführt)](./_img/verG/a37.gif) | Frei (gefrierender Regen als Overlay weitergeführt). |
| `/G` | ![Sechsstelliger Maidenhead-Locator](./_img/verG/38.gif) | Sechsstelliger Maidenhead-Locator. | `\G` | ![Frei (Schneeschauer als Overlay weitergeführt)](./_img/verG/a38.gif) | Frei (Schneeschauer als Overlay weitergeführt). |
| `/H` | ![Hotel](./_img/verG/39.gif) | Hotel. | `\H` | ![Dunst](./_img/verG/a39.gif) | Dunst; auch Basis für Gefahrensymbole. |
| `/I` | ![TCP/IP-Station im Funknetz](./_img/verG/40.gif) | TCP/IP-Station im Funknetz. | `\I` | ![Regenschauer](./_img/verG/a40.gif) | Regenschauer. |
| `/J` | ![Frei](./_img/verG/41.gif) | Frei. | `\J` | ![Frei (Blitz als Overlay weitergeführt)](./_img/verG/a41.gif) | Frei (Blitz als Overlay weitergeführt). |
| `/K` | ![Schule](./_img/verG/42.gif) | Schule. | `\K` | ![Kenwood-Funkgerät](./_img/verG/a42.gif) | Kenwood-Funkgerät. |
| `/L` | ![Mit APRS verbundener Computernutzer](./_img/verG/43.gif) | Mit APRS verbundener Computernutzer. | `\L` | ![Leuchtturm](./_img/verG/a43.gif) | Leuchtturm. |
| `/M` | ![MacAPRS](./_img/verG/44.gif) | MacAPRS. | `\M` | ![MARS](./_img/verG/a44.gif) | MARS; Overlays für die Art des Dienstes. |
| `/N` | ![Station des National Traffic System](./_img/verG/45.gif) | Station des National Traffic System. | `\N` | ![Navigationsboje](./_img/verG/a45.gif) | Navigationsboje. |
| `/O` | ![Ballon](./_img/verG/46.gif) | Ballon. | `\O` | ![Amateur-Rakete / Ballonfamilie mit Overlays](./_img/verG/a46.gif) | Amateur-Rakete / Ballonfamilie mit Overlays. |
| `/P` | ![Polizei](./_img/verG/47.gif) | Polizei. | `\P` | ![Parkplatz](./_img/verG/a47.gif) | Parkplatz. |
| `/Q` | ![Frei](./_img/verG/48.gif) | Frei. | `\Q` | ![Erdbeben](./_img/verG/a48.gif) | Erdbeben. |
| `/R` | ![Freizeitfahrzeug / Wohnmobil](./_img/verG/49.gif) | Freizeitfahrzeug / Wohnmobil. | `\R` | ![Restaurant](./_img/verG/a49.gif) | Restaurant. |
| `/S` | ![Space Shuttle](./_img/verG/50.gif) | Space Shuttle. | `\S` | ![Satellit / PACSAT](./_img/verG/a50.gif) | Satellit / PACSAT. |
| `/T` | ![SSTV](./_img/verG/51.gif) | SSTV. | `\T` | ![Gewitter](./_img/verG/a51.gif) | Gewitter. |
| `/U` | ![Bus](./_img/verG/52.gif) | Bus. | `\U` | ![Sonniges Wetter](./_img/verG/a52.gif) | Sonniges Wetter. |
| `/V` | ![ATV / Quad](./_img/verG/53.gif) | ATV / Quad. | `\V` | ![VORTAC-Navigationshilfe](./_img/verG/a53.gif) | VORTAC-Navigationshilfe. |
| `/W` | ![Station des National Weather Service](./_img/verG/54.gif) | Station des National Weather Service. | `\W` | ![NWS-Station mit Overlays](./_img/verG/a54.gif) | NWS-Station mit Overlays. |
| `/X` | ![Hubschrauber](./_img/verG/55.gif) | Hubschrauber. | `\X` | ![Apotheke](./_img/verG/a55.gif) | Apotheke. |
| `/Y` | ![Segeljacht](./_img/verG/56.gif) | Segeljacht. | `\Y` | ![Funkgeräte und APRS-Geräte](./_img/verG/a56.gif) | Funkgeräte und APRS-Geräte. |
| `/Z` | ![WinAPRS](./_img/verG/57.gif) | WinAPRS. | `\Z` | ![Frei](./_img/verG/a57.gif) | Frei. |
| `/[` | ![Person / Fußgängerstation](./_img/verG/58.gif) | Person / Fußgängerstation. | `\[` | ![Wall Cloud](./_img/verG/a58.gif) | Wall Cloud; auch Personensymbol mit Overlay. |
| `/\` | ![Dreieck, Funkpeilung](./_img/verG/59.gif) | Dreieck, Funkpeilung. | `\\` | ![Neues GPS-Symbol mit Overlays](./_img/verG/a59.gif) | Neues GPS-Symbol mit Overlays. |
| `/]` | ![Post / Postamt](./_img/verG/60.gif) | Post / Postamt. | `\]` | ![Frei](./_img/verG/a60.gif) | Frei. |
| `/^` | ![Großflugzeug](./_img/verG/61.gif) | Großflugzeug. | `\^` | ![Luftfahrt](./_img/verG/a61.gif) | Luftfahrt; weitere Flugzeugtypen durch Overlays. |
| `/_` | ![Wetterstation](./_img/verG/62.gif) | Wetterstation. | `\_` | ![Wetterstation / grüner Digi mit Overlay](./_img/verG/a62.gif) | Wetterstation / grüner Digi mit Overlay. |
| <code>/&#96;</code> | ![Parabolantenne](./_img/verG/63.gif) | Parabolantenne. | <code>&#92;&#96;</code> | ![Regen](./_img/verG/a63.gif) | Regen; Niederschlagsarten durch Overlays. |
| `/a` | ![Rettungswagen](./_img/verG/64.gif) | Rettungswagen. | `\a` | ![ARRL, ARES, Winlink, D-STAR und weitere Overlays](./_img/verG/a64.gif) | ARRL, ARES, Winlink, D-STAR und weitere Overlays. |
| `/b` | ![Fahrrad](./_img/verG/65.gif) | Fahrrad. | `\b` | ![Frei (Staub oder Sand als Overlay weitergeführt)](./_img/verG/a65.gif) | Frei (Staub oder Sand als Overlay weitergeführt). |
| `/c` | ![Einsatzleitstelle](./_img/verG/66.gif) | Einsatzleitstelle. | `\c` | ![Zivilschutz](./_img/verG/a66.gif) | Zivilschutz; RACES, SATERN und weitere Overlays. |
| `/d` | ![Feuerwehr](./_img/verG/67.gif) | Feuerwehr. | `\d` | ![DX-Spot nach Rufzeichen](./_img/verG/a67.gif) | DX-Spot nach Rufzeichen. |
| `/e` | ![Pferd / Reitsport](./_img/verG/68.gif) | Pferd / Reitsport. | `\e` | ![Schneeregen](./_img/verG/a68.gif) | Schneeregen. |
| `/f` | ![Feuerwehrfahrzeug](./_img/verG/69.gif) | Feuerwehrfahrzeug. | `\f` | ![Trichterwolke](./_img/verG/a69.gif) | Trichterwolke. |
| `/g` | ![Segelflugzeug](./_img/verG/70.gif) | Segelflugzeug. | `\g` | ![Sturmwarnflaggen](./_img/verG/a70.gif) | Sturmwarnflaggen. |
| `/h` | ![Krankenhaus](./_img/verG/71.gif) | Krankenhaus. | `\h` | ![Laden / Amateurfunkmesse](./_img/verG/a71.gif) | Laden / Amateurfunkmesse; `Ch` bezeichnet einen Amateurfunkclub. |
| `/i` | ![IOTA (Islands on the Air)](./_img/verG/72.gif) | IOTA (Islands on the Air). | `\i` | ![Kasten / interessanter Punkt](./_img/verG/a72.gif) | Kasten / interessanter Punkt. |
| `/j` | ![Jeep](./_img/verG/73.gif) | Jeep. | `\j` | ![Straßenbauarbeiten](./_img/verG/a73.gif) | Straßenbauarbeiten. |
| `/k` | ![Lastwagen](./_img/verG/74.gif) | Lastwagen. | `\k` | ![Spezialfahrzeug, SUV, ATV oder 4×4](./_img/verG/a74.gif) | Spezialfahrzeug, SUV, ATV oder 4×4. |
| `/l` | ![Laptop](./_img/verG/75.gif) | Laptop. | `\l` | ![Fläche: Rechteck, Kreis, Linie oder Dreieck](./_img/verG/a75.gif) | Fläche: Rechteck, Kreis, Linie oder Dreieck. |
| `/m` | ![Mic-E-Relais](./_img/verG/76.gif) | Mic-E-Relais. | `\m` | ![Wertanzeige / Wegweiser](./_img/verG/a76.gif) | Wertanzeige / Wegweiser. |
| `/n` | ![Netzwerkknoten](./_img/verG/77.gif) | Netzwerkknoten. | `\n` | ![Dreieck mit Overlay](./_img/verG/a77.gif) | Dreieck mit Overlay. |
| `/o` | ![Notfall-Einsatzzentrale (EOC)](./_img/verG/78.gif) | Notfall-Einsatzzentrale (EOC). | `\o` | ![Kleiner Kreis](./_img/verG/a78.gif) | Kleiner Kreis. |
| `/p` | ![ROVER / Hund](./_img/verG/79.gif) | ROVER / Hund. | `\p` | ![Frei (teilweise bewölkt als Overlay weitergeführt)](./_img/verG/a79.gif) | Frei (teilweise bewölkt als Overlay weitergeführt). |
| `/q` | ![Maidenhead-Locator (128-m-Beschreibung)](./_img/verG/80.gif) | Maidenhead-Locator (128-m-Beschreibung). | `\q` | ![Frei](./_img/verG/a80.gif) | Frei. |
| `/r` | ![Relaisstation](./_img/verG/81.gif) | Relaisstation. | `\r` | ![Toiletten](./_img/verG/a81.gif) | Toiletten. |
| `/s` | ![Motorboot / Schiff](./_img/verG/82.gif) | Motorboot / Schiff. | `\s` | ![Schiff / Boot mit Overlay](./_img/verG/a82.gif) | Schiff / Boot mit Overlay. |
| `/t` | ![Lkw-Rastplatz](./_img/verG/83.gif) | Lkw-Rastplatz. | `\t` | ![Tornado](./_img/verG/a83.gif) | Tornado. |
| `/u` | ![18-rädriger Lkw](./_img/verG/84.gif) | 18-rädriger Lkw. | `\u` | ![Lkw mit Overlay](./_img/verG/a84.gif) | Lkw mit Overlay. |
| `/v` | ![Van / Kleintransporter](./_img/verG/85.gif) | Van / Kleintransporter. | `\v` | ![Van / Kleintransporter mit Overlay](./_img/verG/a85.gif) | Van / Kleintransporter mit Overlay. |
| `/w` | ![Wasserstation](./_img/verG/86.gif) | Wasserstation. | `\w` | ![Hochwasser, Lawine oder Erdrutsch](./_img/verG/a86.gif) | Hochwasser, Lawine oder Erdrutsch. |
| `/x` | ![xAPRS / Unix](./_img/verG/87.gif) | xAPRS / Unix. | `\x` | ![Verkehrsunfall oder Hindernis auf der Straße](./_img/verG/a87.gif) | Verkehrsunfall oder Hindernis auf der Straße. |
| `/y` | ![Yagi-Antenne am QTH](./_img/verG/88.gif) | Yagi-Antenne am QTH. | `\y` | ![Skywarn](./_img/verG/a88.gif) | Skywarn. |
| `/z` | ![Frei](./_img/verG/89.gif) | Frei. | `\z` | ![Schutzunterkunft mit Overlay](./_img/verG/a89.gif) | Schutzunterkunft mit Overlay. |
| `/{` | ![Frei](./_img/verG/90.gif) | Frei. | `\{` | ![Frei (Nebel als Overlay weitergeführt)](./_img/verG/a90.gif) | Frei (Nebel als Overlay weitergeführt). |
| <code>/&#124;</code> | ![TNC-Datenstrom-Umschalter](./_img/verG/91.gif) | TNC-Datenstrom-Umschalter. | <code>&#92;&#124;</code> | ![TNC-Datenstrom-Umschalter](./_img/verG/a91.gif) | TNC-Datenstrom-Umschalter. |
| `/}` | ![Frei](./_img/verG/92.gif) | Frei. | `\}` | ![Frei](./_img/verG/a92.gif) | Frei. |
| `/~` | ![TNC-Datenstrom-Umschalter](./_img/verG/93.gif) | TNC-Datenstrom-Umschalter. | `\~` | ![TNC-Datenstrom-Umschalter](./_img/verG/a93.gif) | TNC-Datenstrom-Umschalter. |

| Zusätzliche Bilddatei | Symbol | Hinweise |
|---|:---:|---|
| `x.gif` | ![Rotes Kreuz](./_img/verG/x.gif) | Zusätzliche Kopie der Grafik des Roten Kreuzes; der Symbolcode in der Primärtabelle lautet `/+`. |

## Quellen und Hinweise zu den Symbolverzeichnissen

- [APRS Symbols - master symbol list](http://www.aprs.org/symbols/symbolsX.txt) - Primär- und Alternativtabelle einschließlich Kennzeichnungen für durch Overlays erweiterte Symbole.
- [APRS symbol overlays and extensions](http://www.aprs.org/symbols/symbols-new.txt) - Beispiele und festgelegte Overlay-Bedeutungen für Häuser, Digipeater, IGates, Fahrzeuge und weitere Symbolfamilien.
- [Overlay Extension to APRS Symbol Set](http://www.aprs.org/symbols/symbols-overlays.txt) - Begründung für die Erweiterung des Symbolsatzes sowie Überlegungen zur Abwärtskompatibilität.
- [Background on Updating APRS Symbols](http://www.aprs.org/symbols/symbols-background.txt) - Anzeige von Symbolen und Einschränkungen älterer Software.

Die vollständige Tabelle zeigt **Basissymbole** und ihre für diesen Artikel zusammengestellten Beschreibungen. Overlay-Varianten sind gesondert in der Erweiterungsliste nachzuschlagen. Bei Abweichungen zwischen älteren Tabellen und neueren Verzeichnissen ist das Revisionsdatum der jeweiligen Definition zu prüfen, insbesondere bei Codes mit geänderter Bedeutung wie `C-`.
