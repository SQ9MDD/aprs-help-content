---
title: APRS-Objekte
description: Wie APRS ein benanntes Objekt auf der Karte darstellt.
---

Ein APRS-Objekt ist ein benannter Kartenbericht, den eine Station für etwas anderes sendet: einen Repeater, Wettersensor, Veranstaltungsort, ein Fahrzeug oder eine andere Ressource. Es verwendet dieselben Positionsdaten wie ein normaler Positionsbericht, davor steht ein Name mit neun Zeichen.

## Grundform

```text
;OBJECT___*DDHHMMzDDMM.hhN/DDDMM.hhW$Kommentar
```

- `;` kennzeichnet einen Objektbericht.
- `OBJECT___` ist ein fester Name mit neun Zeichen; kürzere Namen werden mit Leerzeichen aufgefüllt.
- `*` kennzeichnet ein aktives Objekt.
- Die restlichen Positionsfelder folgen den üblichen APRS-Regeln.

Bei Namen wird zwischen Groß- und Kleinschreibung unterschieden. Wähle einen klaren, stabilen Namen, damit Empfänger spätere Berichte als Aktualisierung desselben Objekts erkennen.

## Aktualisieren und entfernen

Sende einen neuen Bericht mit demselben Objektnamen, um Position oder Kommentar zu ändern. Ein Objekt kann als gelöscht markiert werden, indem `*` durch `_` ersetzt wird. Empfänger sollen es dann nicht mehr anzeigen, den Bericht aber – soweit ihre Software dies unterstützt – in der Historie behalten.

Nur die verantwortliche Station sollte ein Objekt entfernen. Das vermeidet widersprüchliche Berichte, durch die ein aktives Objekt abwechselnd erscheint und verschwindet.

## Objekt oder Item?

Nutze ein **Objekt**, wenn der Name genau neun Zeichen hat und ein Zeitstempel hilfreich ist. Ein APRS-**Item** hat einen Namen variabler Länge und keinen Zeitstempel; es eignet sich daher für einfachere, kurzlebige Meldungen.

## Quellen

- [PROTOCOL.TXT — Objektformat, Ersetzen und Löschen](/APRS-SPEC/PROTOCOL.TXT)
- [Objects.txt — Verhalten und Darstellung von Objekten](/APRS-SPEC/Objects.txt)
