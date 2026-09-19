---
title: Frequenzobjekte
description: APRS-Objekte zur Veröffentlichung lokaler Funkfrequenzen.
---

Frequenzobjekte veröffentlichen nützliche lokale Funkressourcen auf der APRS-Karte: einen empfohlenen Sprach-Repeater, einen EchoLink- oder IRLP-Knoten, ein Winlink-Gateway oder ein Netz. Es sind normale APRS-Objekte mit einem strukturierten Frequenznamen oder -kommentar.

## Information lokal halten

Ein Frequenzobjekt ist nur dort nützlich, wo Reisende es nutzen können. Sende es zurückhaltend und entsprechend dem lokalen RF-Betriebsplan; unnötige Weiterleitungen erhöhen die Auslastung eines gemeinsamen Kanals.

## Frequenz im Kommentar

Die gut lesbare Form enthält eine Frequenz und `MHz`, optional Ton, Offset und Reichweite:

```text
146.805MHz T107 R25m
```

`T107` beschreibt einen 107,x-Hz-Ton, `R25m` eine nominelle Reichweite von 25 Meilen. Die vollständige Konvention definiert auch CTCSS/DCS, Modulation, Offset und Kilometer. Sie ist eine Konvention für kompatible APRS-Clients und kein Ersatz für den lokalen Frequenzplan.

## Frequenz als Objektname

Bei einem Repeater kann die Frequenz im Objektnamen stehen; zusätzliche Zeichen sorgen für Eindeutigkeit. Der Kommentar enthält dann Ton, Offset, Reichweite und eine kurze Beschreibung.

```text
;147.105md*111111zDDMM.hhN/DDDMM.hhWrT107 R25m Lokaler Repeater
```

Dies ist nur ein Beispiel. Tatsächliche Koordinaten, Symbol, Namenszusatz und Sendeintervall müssen für die jeweilige Station gewählt werden.

## Spezielle Dienste

EchoLink, IRLP, WiRES und Winlink können denselben Objektmechanismus nutzen. Ihre Namenskonventionen helfen mobilen Clients beim Erkennen des Dienstes; Verfügbarkeit und Status sollten jedoch vom Betreiber oder einer zuverlässigen lokalen Quelle stammen.

## Quellen

- [freqspec.txt — Frequenzformate und Dienstobjekt-Konventionen](/APRS-SPEC/freqspec.txt)
- [PROTOCOL.TXT — APRS-Objektrahmen](/APRS-SPEC/PROTOCOL.TXT)
