---
title: Destination Address, TOCALL und ALTNET in APRS
description: Wie APRS das AX.25-Feld Destination Address verwendet, was ein TOCALL ist, wie Software und Geräte identifiziert werden und wozu ALTNET dient.
template: doc
tableOfContents: true
---

In einem APRS-Frame:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

ist das Element:

```text
APBOX0
```

die **Destination Address**, also das Zieladressfeld des AX.25-Frames.

Die Bezeichnung kann irreführend sein. Im klassischen AX.25 bezeichnet die Destination Address tatsächlich das Ziel eines Frames, APRS verwendet dieses Feld jedoch zusätzlich zur Übertragung eigener Informationen.

In typischen modernen APRS-Paketen steht dort meistens ein **TOCALL**, also eine Kennung der Software, des Geräts oder der Firmware, die das Paket erzeugt hat.

Destination Address und TOCALL sind jedoch nicht gleichbedeutend. Ein TOCALL ist nur eine der Möglichkeiten, wie APRS das Destination-Address-Feld nutzt.

## Destination Address in APRS

Die Destination Address verwendet dasselbe AX.25-Adressformat wie die Source Address. Das Feld kann bis zu sechs Zeichen und eine SSID enthalten.

APRS nutzt es jedoch auf verschiedene Arten. Je nach Pakettyp kann es unter anderem enthalten:

- einen TOCALL zur Identifikation von Software oder Gerät,
- eine generische APRS-Adresse,
- von Mic-E codierte Daten,
- den Namen eines ALTNET,
- historische Informationen aus zusätzlichen APRS-Mechanismen.

Daher darf die Zeichenfolge hinter dem Zeichen `>` nicht automatisch als Rufzeichen eines Empfängers interpretiert werden.

## TOCALL

Die heute häufigste Verwendung der Destination Address besteht darin, die Software oder das Gerät zu identifizieren, das ein Paket erzeugt hat.

Beispiel:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

Hier ist:

```text
SQ9MDD-4
```

die Source Address, während:

```text
APBOX0
```

die als TOCALL verwendete Destination Address ist.

`APBOX0` ist die APRSBox-Kennung, die in der aktuellen APRS-Gerätekennungsdatenbank registriert ist.

Ein TOCALL ermöglicht empfangender APRS-Software zu erkennen, welche Anwendung, Firmware oder welches Gerät ein Paket erzeugt hat. Er bezeichnet nicht den Empfänger des Pakets.

## Aktuelles TOCALL-Register

Die aktuellen Zuteilungen von APRS-Kennungen werden in folgendem Repository gepflegt:

[github.com/aprsorg/aprs-deviceid](https://github.com/aprsorg/aprs-deviceid)

Die primäre Quelldatei ist:

[tocalls.yaml](https://github.com/aprsorg/aprs-deviceid/blob/main/tocalls.yaml)

Das Repository enthält die aktuelle Datenbank für TOCALL- und Mic-E-Kennungen. Die YAML-Datei ist die Quelle, aus der weitere maschinenlesbare Formate erzeugt werden.

Der Quellcode der Suchoberfläche für diese Kennungen wird separat gepflegt:

[github.com/aprsorg/aprs-deviceid-web](https://github.com/aprsorg/aprs-deviceid-web)

Bei der Entwicklung eines neuen APRS-Programms oder Geräts sollte nicht einfach eine zufällige `APxxxx`-Kennung gewählt und dauerhaft verwendet werden. Die Zuteilung sollte in der aktuellen Datenbank geprüft und registriert werden.

## Wie sehen TOCALLs aus?

Viele moderne Kennungen beginnen mit:

```text
AP
```

gefolgt von Zeichen, die ein bestimmtes Projekt, einen Hersteller oder eine Gerätefamilie identifizieren.

Beispiele aus der aktuellen Datenbank können so aussehen:

```text
APBOX0
APDW??
APDR??
```

Nicht jeder Eintrag ist ein einzelner exakter Wert. Das Register unterstützt auch Platzhaltermuster, sodass ein Eintrag eine Familie von Versionen oder Geräten beschreiben kann.

Für die Erkennung von TOCALLs sollte daher die aktuelle Datenbank verwendet werden, anstatt eine statische Liste manuell zu pflegen.

## Generische Destination Addresses

Nicht jede gültige APRS-Destination-Address ist der TOCALL eines bestimmten Programms.

Die APRS-Spezifikation definiert auch generische Werte für bestimmte Arten von Verkehr, zum Beispiel:

```text
APRS
BEACON
CQ
QST
TEST
WX
```

Die historische Spezifikation beschreibt eine größere Gruppe solcher Adressen und erlaubt bei einigen davon zusätzliche Zeichen.

Sie gehören zu den Möglichkeiten, wie APRS die Destination Address verwendet, identifizieren aber keine konkrete Anwendung auf dieselbe Weise wie moderne TOCALLs.

## Destination Address ist nicht der Empfänger einer APRS-Nachricht

Diese Unterscheidung ist bei Nachrichten besonders wichtig.

Beispiel:

```text
SQ9MDD-7>APBOX0,WIDE1-1::SP9XYZ   :Test
```

enthält:

```text
Source Address:       SQ9MDD-7
Destination Address: APBOX0
```

während der Empfänger der APRS-Nachricht:

```text
SP9XYZ
```

ist.

Der Empfänger befindet sich innerhalb des Information-Feldes, also in den APRS-Daten.

`APBOX0` bleibt hier der TOCALL und identifiziert die Software, die das Paket erzeugt hat.

## Mic-E

Mic-E ist eine der wichtigsten Ausnahmen von der vereinfachten Annahme:

```text
Destination Address = TOCALL
```

Bei Mic-E-Paketen wird ein Teil der Information direkt in der Destination Address codiert.

Die sechs Zeichen dieses Feldes sind an der Codierung von Position und Mic-E-Status beteiligt. Eine Zeichenfolge, die wie eine ungewöhnliche Zieladresse aussieht, kann daher tatsächlich Daten enthalten.

Die TOCALL-Erkennung muss deshalb den Pakettyp berücksichtigen. Nicht jede Destination Address darf bedingungslos mit `tocalls.yaml` verglichen werden.

## ALTNET

**ALTNET**, also Alternate Network, verwendet die Destination Address als Kennung eines getrennten APRS-Netzes oder einer Verkehrsgruppe.

Ein experimentelles Netz könnte zum Beispiel:

```text
TEST
```

als Destination Address verwenden.

In diesem Fall ist `TEST` weder ein Software-TOCALL noch das Rufzeichen eines Empfängers. Es kennzeichnet ein logisches Netz.

ALTNET ermöglicht die Nutzung derselben APRS-Infrastruktur, während bestimmter Verkehr von der normalen Netzdarstellung getrennt bleibt. Software kann dadurch nur Pakete anzeigen oder verarbeiten, die zum ausgewählten ALTNET gehören.

ALTNET ist ein weiterer Grund, warum Destination Address nicht mit TOCALL gleichgesetzt werden darf.

## Historische Nutzung der Destination-SSID für Routing

Die ursprüngliche APRS-Spezifikation definierte außerdem die Verwendung der **SSID der Destination Address** zur Auswahl eines generischen Digipeater-Pfades.

Für SSIDs von `-1` bis `-7` waren historisch folgende Pfade definiert:

| Destination SSID | Historischer Pfad |
|---|---|
| `-0` | normalen VIA-Pfad verwenden |
| `-1` | `WIDE-1` |
| `-2` | `WIDE-2` |
| `-3` | `WIDE-3` |
| `-4` | `WIDE-4` |
| `-5` | `WIDE-5` |
| `-6` | `WIDE-6` |
| `-7` | `WIDE-7` |

Die Werte `-8` bis `-15` waren für Richtungsvarianten vorgesehen, darunter Nord, Süd, Ost und West.

Daher stammen Hinweise, dass die Destination-SSID die gewünschte Anzahl von Weiterleitungen bestimmen konnte.

Zum Beispiel darf:

```text
APRS-2
```

jedoch nicht mit dem heutigen:

```text
WIDE2-2
```

gleichgesetzt werden.

Es handelt sich um unterschiedliche Mechanismen.

Die Pfadcodierung in der Destination-SSID entstand vor dem WIDEn-N-Verfahren und war unter anderem für sehr einfache Tracker vorgesehen, bei denen wenige Schalter direkt die Destination-SSID auswählen konnten.

In neuerer APRS-Dokumentation ist dieser Mechanismus als **obsolete** gekennzeichnet. Moderne Implementierungen sollten den Pfad im dafür vorgesehenen AX.25-Feld Digipeater Addresses übertragen.

## Und die C-Bits?

Das siebte Oktett einer AX.25-Adresse enthält neben der SSID auch Bits, die mit dem Command/Response-Mechanismus von AX.25 zusammenhängen.

Sie sind weder Teil des TOCALL noch bestimmen sie die APRS-Bedeutung der Destination Address.

In realen APRS-Implementierungen kommen unterschiedliche Kombinationen dieser Bits vor. APRS-Software sollte daher nicht versuchen, die Bedeutung eines TOCALL, ALTNET oder einer anderen Verwendung der Destination Address aus der Kombination der C-Bits abzuleiten.

## Wie ist die Destination Address zu interpretieren?

Für ein typisches modernes Paket:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

kann man lesen:

```text
SQ9MDD-4   Source Address
APBOX0     als TOCALL verwendete Destination Address
WIDE1-1    Pfadelement
```

Diese Interpretation darf jedoch nicht mechanisch auf jeden APRS-Frame angewendet werden.

Die Destination Address kann unterschiedliche Funktionen erfüllen:

```text
TOCALL
generische APRS-Adresse
Mic-E
ALTNET
historische APRS-Mechanismen
```

Die korrekte Interpretation hängt daher vom Pakettyp und vom Protokollkontext ab.
