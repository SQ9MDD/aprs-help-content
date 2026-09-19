---
title: Struktur eines APRS-Pakets
description: Eine kurze Einführung in die Bestandteile eines APRS-Pakets.
---

Ein APRS-Paket enthält eine sendende Station, eine Zielkennung, einen optionalen Pfad und ein Informationsfeld.

```text
SOURCE>DESTINATION,PATH:information field
```

Das Informationsfeld teilt der empfangenden Software mit, ob das Paket eine Position, Nachricht, ein Objekt, Telemetrie oder einen anderen APRS-Datentyp enthält.

## Warum das wichtig ist

Das getrennte Lesen der einzelnen Teile ist der schnellste Weg, ein Paket zu diagnostizieren, das nicht wie erwartet angezeigt wird.
