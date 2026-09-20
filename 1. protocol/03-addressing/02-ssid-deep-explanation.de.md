---
title: SSID in AX.25 und APRS
description: Technische Erklärung des AX.25-SSID-Feldes, der APRS-Konventionen und der Unterschiede zwischen RF- und APRS-IS-Adressierung.
template: doc
tableOfContents: true
---

SSID, **Secondary Station Identifier**, ist Bestandteil einer AX.25-Adresse. APRS verwendet dieses Feld entsprechend seiner Protokollfunktion und zusätzlich nach Konventionen, die auf den typischen Einsatzzweck einer Station hinweisen.

Zu unterscheiden sind:

- SSID als Adressfeld in AX.25,
- in APRS verwendete SSID-Empfehlungen,
- historische Verwendungen der SSID,
- Kennungen, die ausschließlich in APRS-IS oder anderen Systemen existieren.

## SSID in AX.25

Eine AX.25-Adresse besteht aus sechs Oktetten für das Rufzeichen und einem siebten Oktett, das unter anderem das vier Bit breite SSID-Feld enthält.

Für eine Quell- oder Zieladresse:

```text
bit:   7 6 5 4 3 2 1 0
       C R R S S S S E
```

Für eine Digipeater-Adresse:

```text
bit:   7 6 5 4 3 2 1 0
       H R R S S S S E
```

Bedeutung:

- `SSSS` - SSID,
- `R` - reservierte Bits,
- `C` - Command/Response,
- `H` - Has Been Repeated,
- `E` - Extension Bit.

Die SSID belegt vier Bits, daher liegt ihr Wertebereich bei `0..15`.

Diese Begrenzung gilt für jede tatsächliche AX.25-Adresse, unabhängig davon, ob sie als Quelladresse, Zieladresse oder Digipeater-Adresse verwendet wird.

## SSID in APRS

APRS verändert das AX.25-SSID-Format nicht. Für Quellstationen haben sich jedoch Konventionen etabliert, die auf den vorgesehenen Einsatzzweck einer Station hinweisen.

Dabei handelt es sich um **Empfehlungen**, nicht um APRS-Syntax und nicht um eine Validierungsregel.

Klassische APRS-Empfehlungen:

| SSID | Typische Verwendung |
|---|---|
| `-0` | primäre Station des Operators, meist ortsfest und nachrichtenfähig |
| `-1` | allgemeine zusätzliche Station |
| `-2` | allgemeine zusätzliche Station |
| `-3` | allgemeine zusätzliche Station |
| `-4` | allgemeine zusätzliche Station |
| `-5` | andere Netze oder Systeme |
| `-6` | besondere Aktivität, Satelliten, andere Bänder usw. |
| `-7` | Handfunkgerät oder andere tragbare Station |
| `-8` | Boot, Wohnmobil oder zweites primäres Mobilgerät |
| `-9` | primäre Mobilstation, normalerweise kommunikationsfähig |
| `-10` | Internet, iGate, EchoLink, Winlink und ähnliche Anwendungen |
| `-11` | Ballon, Flugzeug, Raumfahrzeug |
| `-12` | APRStt, DTMF, RFID, Geräte, Einweg-Tracker |
| `-13` | Wetterstation |
| `-14` | Fahrzeug eines Berufskraftfahrers oder ähnliche Anwendung |
| `-15` | allgemeine zusätzliche Station |

Auf AX.25-Ebene ist `CALL-9` lediglich eine Station mit der SSID `9`. Die Interpretation als primäre Mobilstation ergibt sich aus der APRS-Konvention.

Software sollte diese Tabelle weder zum Verwerfen von Paketen noch zum Erzwingen eines Stationstyps verwenden.

## `-9` und `-12`

In den klassischen APRS-Empfehlungen ist `-9` in erster Linie für die primäre Mobilstation eines Operators vorgesehen, die üblicherweise auch Kommunikation über APRS oder Sprache ermöglicht.

`-12` eignet sich unter anderem für Einweg-Tracker.

Ein in einem Fahrzeug eingebautes Gerät muss daher nicht automatisch `-9` verwenden. Sendet es ausschließlich Positionsdaten und besitzt keine Rückkommunikationsmöglichkeit, kann `-12` seine Rolle besser beschreiben.

## Historische Verwendung der SSID zur Symbolauswahl

In frühen APRS-Versionen wurde die SSID auch zur Auswahl des Symbols für Tracker verwendet, die rohe NMEA-0183-Daten übertrugen.

Dieser Mechanismus wurde später durch andere Verfahren zur Symbolcodierung ersetzt, unter anderem durch das `GPSxyz`-Schema über die Zieladresse sowie durch APRS-Positionsformate, die das Symbol direkt enthalten.

Eine moderne Source-SSID sollte daher nicht zur Bestimmung des Stationssymbols verwendet werden.

## Source-, Destination- und Digipeater-SSID

Der Frame:

```text
SQ9MDD-9>APRS-2,WIDE1-1:
```

enthält drei unabhängige SSID-Werte:

```text
SQ9MDD-9     source SSID = 9
APRS-2       destination SSID = 2
WIDE1-1      digipeater SSID = 1
```

Auf AX.25-Ebene werden alle gleich codiert, ihre Bedeutung in APRS hängt jedoch von ihrer Position ab.

Empfehlungen wie `-7` handheld, `-9` mobile oder `-13` weather beziehen sich hauptsächlich auf die **Source-SSID**. Destination-SSIDs und SSIDs im Pfad können von anderen APRS-Mechanismen verwendet werden und sollten nicht anhand der Tabelle für Stationsrollen interpretiert werden.

## APRS-IS und die Grenzen von AX.25

APRS-IS verwendet eine textuelle Paketdarstellung und erlaubt nach dem Bindestrich ein Suffix aus einem oder zwei alphanumerischen Zeichen, sofern die übrigen APRS-IS-Formatregeln eingehalten werden.

Damit ist beispielsweise folgende Kennung möglich:

```text
SQ9MDD-D
```

Eine solche Kennung kann von einer Station verwendet werden, die auf APRS-IS existiert, beispielsweise von einem Gateway oder einem System, das APRS mit einem anderen Netz verbindet.

Sie kann jedoch nicht als native AX.25-Quelladresse auf RF codiert werden, da das AX.25-SSID-Feld ausschließlich den vier Bit breiten Wert `0..15` enthält.

Ein APRS-IS -> RF-Gateway darf daher nicht davon ausgehen, dass jede gültige APRS-IS-Kennung direkt in ein AX.25-Adressfeld übernommen werden kann.

## Third-party traffic

APRS definiert einen Mechanismus für **third-party traffic**, bei dem der ursprüngliche Header und die ursprünglichen Daten im Informationsfeld eines anderen gültigen AX.25-Frames transportiert werden können.

Damit können Informationen über eine Station aus einem anderen Netz erhalten bleiben, der AX.25-Adressraum wird dadurch jedoch nicht erweitert. Eine Kennung wie `SQ9MDD-D` kann in Third-Party-Daten vorkommen, wird dadurch aber nicht zu einer nativen AX.25-Quelladresse.

## Kennungen außerhalb des Bereichs `0..15`

In der APRS-Dokumentation finden sich historische Beispiele für Suffixe wie:

```text
-63
-tt
-ID
-A ... -Z
```

Sie wurden für Objekte oder Stationen verwendet, die von Internet-basierten oder anderen Systemen erzeugt wurden. Es handelt sich nicht um zusätzliche Werte der vier Bit breiten AX.25-SSID.

Für eine native RF-Adresse gilt weiterhin der Bereich `0..15`.

## APRS-Objekte

Der Name eines APRS-Objekts wird im Informationsfeld und nicht im AX.25-Adressfeld übertragen und kann bis zu neun Zeichen enthalten.

Namen wie:

```text
TEST-63
TEST-ID
WX-A
```

können daher Endungen enthalten, die wie SSIDs aussehen. Diese Endungen sind jedoch Teil des Objektnamens und unterliegen nicht der Vier-Bit-Begrenzung der AX.25-SSID.

## Konsequenzen für Gateways und Software

Eine APRS-Implementierung sollte die Validierung einer nativen AX.25-Adresse von der Validierung einer in APRS-IS verwendeten Kennung trennen.

Bei einer direkt über RF übertragenen Adresse muss die SSID im Bereich `0..15` liegen.

Software darf nicht automatisch davon ausgehen, dass jede in APRS-IS vorhandene Kennung:

- in eine AX.25-Adresse umgewandelt werden kann,
- über RF erneut ausgesendet werden kann,
- als Source Address verwendet werden kann,
- über KISS als native Frame-Adresse an ein TNC übergeben werden kann.

Dies ist besonders wichtig für APRS-IS -> RF-Gateways, DMR/APRS- und D-STAR/APRS-Integrationen, Hotspots, Telemetriesysteme und Anwendungen, die ausschließlich im Internet existierende APRS-Stationen erzeugen.

## Interpretation der SSID in Software

Die SSID kann als zusätzliche Information über die vorgesehene Rolle einer Station verwendet werden, sollte aber die im Paket enthaltenen Daten nicht überschreiben.

Sendet `CALL-9` ein bestimmtes APRS-Symbol, sollte die Software das Symbol aus dem Paket verwenden. Sendet `CALL-13` keine Wetterdaten, wird das Paket durch die SSID allein nicht zu einem Wetterpaket.

SSID-Konventionen dienen in erster Linie der Organisation von Stationen und ihrer leichteren Erkennung durch Operatoren.

## Zusammenfassung

Die wichtigsten Regeln:

```text
AX.25 SSID
    4 Bit
    Bereich 0..15

APRS source SSID
    verwendet die AX.25-SSID
    kann zusätzlich den Empfehlungen 0..15 folgen

APRS-IS
    kann Kennungen enthalten, die keine
    direkte AX.25-Darstellung besitzen

APRS-Objekt
    der Name befindet sich im information field
    ein Suffix nach einem Bindestrich muss keine SSID sein

third-party traffic
    kann eine textuelle Darstellung einer Station
    außerhalb von RF transportieren, erweitert AX.25 aber nicht
```

Bei der Interpretation eines Wertes nach einem Bindestrich muss daher zuerst festgestellt werden, ob er Teil einer tatsächlichen AX.25-Adresse, einer APRS-IS-Kennung oder eines im Informationsfeld übertragenen Namens ist.

## Quellen

- [AX.25 Link Access Protocol for Amateur Packet Radio, Version 2.2](https://www.ax25.net/AX25.2.2-Jul%2098-2.pdf)
- [APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [Bob Bruninga WB4APR, APRS SSID Recommendations](https://github.com/SQ9MDD/APRSBox/blob/main/APRS-SPEC/SSIDs.txt)
- [APRS-IS - Connecting to APRS-IS](https://www.aprs-is.net/connecting.aspx)
- [APRS-IS - Server Design](https://www.aprs-is.net/ServerDesign.aspx)
