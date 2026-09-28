---
title: "Positionsmeldungen"
---


Positionsmeldungen gehören zu den grundlegenden APRS-Datentypen. Sie übertragen den Standort einer Station und optional Angaben zu Bewegung, Eigenschaften oder Status. Eine Position kann von einem GNSS-Empfänger stammen, manuell eingegeben oder als fester Standort programmiert sein. Sowohl mobile als auch ortsfeste Stationen senden Positionsmeldungen.


## Formate von Positionsmeldungen


APRS definiert mehrere Verfahren zur Übermittlung einer Position:

- **Uncompressed Position**: geografische Koordinaten in lesbarer Textform.

- **Compressed Position**: mit Base91 codierte Koordinaten zur Verringerung der Meldungslänge.

- **Mic-E**: ein Format, das einen Teil der Positionsinformationen in der AX.25-Zieladresse und den Rest im Informationsfeld codiert.


Die Spezifikation beschreibt außerdem Positionsdaten in NMEA-Sätzen und Meldungen mit Maidenhead-Locator. Dafür gelten eigene Kennungen und Auswertungsregeln.


## Kennungen von Positionsmeldungen


Bei unkomprimierten und komprimierten Meldungen kennzeichnet das erste Zeichen des Informationsfeldes, der Data Type Identifier (DTI), den Meldungstyp:

| DTI | Bedeutung |
|---|---|
| `!` | Position ohne Zeitstempel; die Station deklariert keine APRS-Nachrichtenfähigkeit. |
| `=` | Position ohne Zeitstempel; die Station deklariert APRS-Nachrichtenfähigkeit. |
| `/` | Position mit Zeitstempel; die Station deklariert keine APRS-Nachrichtenfähigkeit. |
| `@` | Position mit Zeitstempel; die Station deklariert APRS-Nachrichtenfähigkeit. |


Der DTI legt nicht fest, ob die Koordinaten komprimiert sind. Das ergibt sich aus der Struktur der folgenden Daten. Mic-E verwendet eigene Kennungen: `'` und `` ` ``.


## Aufbau einer Meldung


Je nach Format kann eine Positionsmeldung die folgenden Elemente enthalten:

| Element | Bedeutung |
|---|---|
| DTI | Datentypkennung. |
| Timestamp | Optionaler Zeitstempel. |
| Position | Geografische Koordinaten. |
| Symbol | Kennung des grafischen Stationssymbols. |
| Position extension | Optionale Positionserweiterung. |
| Comment | Kommentar und zusätzliche Informationen. |


Vorhandensein, Reihenfolge und Codierung der Felder hängen vom Meldungsformat ab.


### Beispiel einer unkomprimierten Meldung


```text
SQ9MDD>APRS:!5003.50N/01956.00E-
```

Im Beispiel kennzeichnet `!` eine Meldung ohne Zeitstempel, `5003.50N` und `01956.00E` sind die Koordinaten und `/` sowie `-` bestimmen Symboltabelle und Symbolcode.


## Zusätzliche Informationen


Positionsmeldungen können ergänzende Daten enthalten. Die Spezifikation sieht unter anderem Folgendes vor:

- **Course/Speed**: Kurs und Geschwindigkeit.

- **PHG**: Sendeleistung, Antennenhöhe, Gewinn und Richtwirkung.

- **RNG**: angegebene Reichweite der Station.

- **BRG/NRQ**: Angaben für die Funkpeilung.

- **Altitude**: Höhe, unter anderem mit der Erweiterung `/A=`.


Verfügbarkeit und Codierung hängen vom Meldungsformat ab; nicht jede Erweiterung ist in jedem Format zulässig.


## Positionsgenauigkeit und Mehrdeutigkeit


Die Auflösung der Koordinaten entspricht nicht der tatsächlichen Messgenauigkeit. APRS definiert **Position Ambiguity**, womit bewusst ein Gebiet statt eines genauen Punktes angegeben werden kann. Die Erweiterung **DAO** ermöglicht die Übertragung zusätzlicher Koordinatenpräzision.

Diese Mechanismen dienen unterschiedlichen Zwecken und müssen getrennt interpretiert werden.


## Stationspositionen sowie Objekte und Elemente


Eine gewöhnliche Positionsmeldung beschreibt die durch die Quelladresse des Rahmens bezeichnete Station. APRS unterstützt außerdem **Objects** und **Items**, mit denen die Position eines benannten Objekts oder Elements unabhängig vom Rufzeichen der sendenden Station übertragen werden kann.

Objekte und Elemente nutzen APRS-Positionscodierungsverfahren, besitzen jedoch eigene Formate und Identifikationsregeln. Sie werden separat dokumentiert.


## Weiterführende Dokumentation


Eigene Artikel behandeln unkomprimierte Positionen, Zeitstempel, Position Ambiguity, Positionserweiterungen, Höhe und Präzision, Symbole, Positionskommentare und komprimierte Formate einschließlich Mic-E.


## Quelle


[APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF).