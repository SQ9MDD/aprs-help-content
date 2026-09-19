---
title: APRS-Rahmentypen
description: Referenz der APRS-Datentypkennungen (DTI) im Informationsfeld.
---

Eine APRS-Übertragung verwendet normalerweise einen AX.25-UI-Rahmen. Dessen Informationsfeld beginnt mit einer **Datentypkennung** (DTI): einem Zeichen, das die Interpretation der folgenden Daten bestimmt.

```text
QUELLE>ZIEL,PFAD:DTI Daten
```

Diese Seite beschreibt die in diesem Feld übertragenen APRS-Datenarten. Sie ersetzt nicht die Regeln für AX.25-Adressierung, Pfade, Positionsformate oder einzelne Wetter- und Telemetriefelder.

## Wichtige Regeln

- Die DTI ist normalerweise das erste Zeichen des Informationsfelds. Das historische Positionsformat `!` ist eine Ausnahme: Es darf an jeder Stelle innerhalb der ersten 40 Zeichen stehen, um bestimmte ältere TNC-Digipeater zu unterstützen.
- Eine DTI kennzeichnet das **Format**, nicht zwingend einen eigenen AX.25-Rahmentyp. Nachrichten, Bestätigungen, Bulletins und Ankündigungen verwenden zum Beispiel alle `:`.
- Eine Position kann unkomprimiert, komprimiert, Mic-E-kodiert oder als roher GPS/NMEA-Satz übertragen werden. Die DTI wählt die Formatfamilie; die folgenden Zeichen bestimmen die Variante.
- Als veraltet, reserviert oder „nicht verwenden“ gekennzeichnete Kennungen dürfen nicht gesendet werden. Ein Empfänger kann sie dennoch in altem Verkehr finden.

## Wichtigste betriebliche Typen

| DTI | Typ | Inhalt |
|---|---|---|
| `!` | Position ohne Zeitstempel, ohne Messaging | Stationsposition; auch das historische Wetterstationsformat Ultimeter 2000. |
| `=` | Position ohne Zeitstempel, mit Messaging | Position einer Station mit APRS-Nachrichtenunterstützung. |
| `/` | Position mit Zeitstempel, ohne Messaging | Position mit Zeitstempel. |
| `@` | Position mit Zeitstempel, mit Messaging | Zeitgestempelte Position einer messagingfähigen Station. |
| `'` oder `` ` `` | Mic-E-Position | Kompakte Mic-E-Position; zugehörige Daten sind teilweise in der Zieladresse kodiert. Ältere Varianten existieren ebenfalls; das TM-D700 verwendet `'` für aktuelle Mic-E-Daten. |
| `$` | Rohe GPS/NMEA-Daten | Ein direkt in APRS übertragener GPS-Satz oder das historische Ultimeter-2000-Format. |
| `;` | Objekt | Benanntes Kartenobjekt, das von einer Station erzeugt und kontrolliert wird; die Daten enthalten einen aktiven oder gelöschten Zustand. |
| `)` | Element | Kürzeres benanntes Kartenelement. Es kann aktiv oder gelöscht sein, hat aber keinen Zeitstempel und kein Eigentümerfeld. |
| `_` | Wetter ohne Position | Wetterbeobachtungen ohne APRS-Position. |
| `#` oder `*` | Peet-Bros-U-II-Wetter | Wetterdaten in den Peet-Bros-Ultimeter-II-Formaten. |
| `T` | Telemetrie | Nicht-Mic-E-Telemetrie, typischerweise Sequenznummer, Analogwerte und Digitalbits. |
| `:` | Nachrichtenfamilie | Adressat plus Text; auch Format für Bestätigungen, Ablehnungen, Bulletins, Ankündigungen und gerichtete Abfragen. |
| `>` | Status | Einzeiliger Status oder Einsatztext der Station, optional mit UTC-Zeitstempel. |
| `<` | Stationsfähigkeiten | Fähigkeits-Token, normalerweise als Antwort auf eine IGate-Abfrage. |
| `?` | Allgemeine Abfrage | Einmalige Broadcast-Abfrage, etwa `?APRS?`, `?WX?` oder `?IGATE?`. |
| `}` | Verkehr von Drittanbietern | Gekapseltes APRS-Paket, das über ein anderes Netz weitergeleitet wurde, mit ursprünglichem Pfadkopf und Nutzdaten. |
| `{` | Benutzerdefinierte Daten | Zugewiesene oder experimentelle Erweiterung: `{`, ein Zeichen Benutzer-ID, ein Zeichen Pakettyp und Anwendungsdaten. |
| `,` | Ungültige oder Testdaten | Daten, die keinem Standard-APRS-Format entsprechen, etwa ein von einem Tracker gemeldeter ungültiger GPS-Fix. |
| `%` | Peilung | Peilformat Agrelo DFJr/MicroFinder. |

## Positions- und Standortberichte

Die vier normalen Positionskennungen unterscheiden das Vorhandensein eines Zeitstempels und die APRS-Nachrichtenfähigkeit. Die Präfixe zeigen den Unterschied; die Koordinatensyntax ist an anderer Stelle definiert.

| Präfix | Bedeutung |
|---|---|
| `!4903.50N/07201.75W...` | Position ohne Zeitstempel, ohne Messaging. |
| `=4903.50N/07201.75W...` | Position ohne Zeitstempel, Messaging vorhanden. |
| `/092345z4903.50N/07201.75W...` | Zeitgestempelte Position, ohne Messaging. |
| `@092345z4903.50N/07201.75W...` | Zeitgestempelte Position, Messaging vorhanden. |

Jede dieser Formen enthält ein Symbol und kann Positionserweiterungen oder einen Kommentar tragen. Komprimierte Positionen, Kurs/Geschwindigkeit, Höhe, Funkreichweite, Wetterdaten und andere Erweiterungen sind daher keine eigenen DTIs.

Mic-E ist ebenfalls ein Positionsbericht, seine Daten sind jedoch kompakt zwischen Zieladresse und Informationsfeld kodiert. Ein Decoder muss Mic-E erkennen und darf die Daten nicht als gewöhnlichen Breiten-/Längengradtext behandeln. Ein mit `$` beginnender roher GPS/NMEA-Bericht ist ein weiteres positionsbezogenes Format und benötigt einen NMEA-Satzparser.

## Kartenobjekte und Elemente

`; ` leitet ein **Objekt** ein. Es enthält Objektname, Aktiv- (`*`) oder Löschmarkierung (`_`), Zeitstempel, Position und Symbol. Ein Objekt kann einen Repeater, einen Veranstaltungspunkt, einen Vorfall, eine Wetterstation oder einen Dienst darstellen, den sein Eigentümer auf die Karte setzt.

`) ` leitet ein **Element** ein. Es enthält einen Elementnamen und eine Aktiv- (`!`) oder Löschmarkierung (`_`), gefolgt von Positionsdaten. Es eignet sich für kurze Kartenobjekte, wenn weder Eigentümer noch Zeitstempel benötigt werden.

Ein Objekt oder Element ist nicht die meldende Station. Das Quellrufzeichen kennzeichnet die sendende Station; der Name in den Nutzdaten bezeichnet die dargestellte Entität.

## Wetter und Telemetrie

Wetterdaten können auf mehrere Arten gesendet werden:

- `_` leitet einen Wetterbericht ohne Position ein.
- Einem Positions-, Objekt- oder Elementbericht können Wetterdaten folgen. Die DTI bleibt dann die der Position, des Objekts oder Elements und wird nicht zu `_`.
- `#` und `*` kennzeichnen die historischen Wetterformate Peet Bros U-II.

`T` leitet Standardtelemetrie außerhalb von Mic-E ein. Der Grundbericht beginnt mit `T#` und enthält eine Sequenznummer, bis zu fünf Analogkanäle und acht Digitalbits. Metadaten, Parameter, Einheiten und Bit-Sense-Informationen verwenden das Telemetrie-Nachrichtenformat und beginnen daher mit `:` statt mit `T`.

## Nachrichten, Bulletins, Abfragen und Antworten

Die Nachrichtenfamilie verwendet einen Adressaten fester Breite:

```text
:ADRESSAT :Nachrichtentext{ID
```

Der Text kann eine persönliche Nachricht, `ack`/`rej`, ein Bulletin (Adressat wie `BLN...`), eine Ankündigung oder eine gerichtete Abfrage sein. Eine Nachrichtenkennung dient der Bestätigungsverarbeitung; APRS-Abfragen sind einmalig und dürfen keine Kennung tragen oder bestätigt werden.

`? ` ist die DTI für eine allgemeine Broadcast-Abfrage. Gerichtete Abfragen verwenden `:`, da sie an eine einzelne Station adressiert sind. Antworten haben keine einheitliche separate DTI: Je nach angeforderten Daten können sie eine Position, ein Objekt, Wetter, Status, Nachricht, Route, einen Gehört-Bericht oder einen Fähigkeitsbericht enthalten.

## Kapselung und Erweiterungen

`} ` bedeutet, dass die Nutzdaten ein APRS-Paket enthalten, das ein Drittanbieternetz durchlaufen hat. Ein Parser sollte Quelle, Ziel, Pfad und innere DTI des eingebetteten Pakets wiederherstellen, bevor er die Originaldaten dekodiert; es ist kein bloßes Kommentarpräfix.

`{ ` ist für ein dokumentiertes benutzerdefiniertes Format bestimmt. Nach der DTI ordnet ein zweistelliger Kopf die Daten einem Autor/einer Anwendung und einem Paketuntertyp zu. `{{` ist die ausdrücklich experimentelle Form. Nicht unterstützte Formate sollen sicher ignoriert werden.

## Historische, reservierte und verbotene Kennungen

Diese Kennungen stehen in der gelieferten APRS-1.0.1-Referenz, sind aber keine normale Wahl für neue Sender.

| Kennung | Status in der Referenz |
|---|---|
| `0x1C`, `0x1D` | Aktuelle und alte Mic-E-Rev-0-Beta-Daten; historische nicht druckbare Kennungen. |
| `[` | Veralteter Maidenhead-Gitterlocator-Beacon. |
| `&` | Für ein Kartenmerkmal reserviert. |
| `+` | Für Shelter-Daten mit Zeit reserviert. |
| `.` | Für Weltraumwetter reserviert. |
| `"`, `(`, `-`, `\`, `]`, `^` | Unbenutzt. |
| `A`–`S`, `U`–`Z`, `a`–`z`, `0`–`9` | Nicht als DTI verwenden. |
| `|`, `~` | Nicht verwenden: TNC-Stream-Switch-Zeichen. |

## Quellenbasis

Diese Übersicht folgt der gelieferten *APRS Protocol Reference, APRS Protocol Version 1.0.1* (Kapitel 5, „APRS Data Type Identifiers“). Die mitgelieferten Notizen zu Wetter, Nachrichten, Parser und benutzerdefinierten Formaten erläutern die zugehörigen Formate und historischen Fälle.
