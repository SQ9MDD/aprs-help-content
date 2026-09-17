---
title: APRS Objects
description: How APRS represents a named object on the map.
---

An APRS object is a named map report sent by a station on behalf of something else: a repeater, weather sensor, event point, vehicle or other asset. It uses the same position information as a normal position report, preceded by a nine-character name.

## Basic form

```text
;OBJECT___*DDHHMMzDDMM.hhN/DDDMM.hhW$comment
```

- `;` identifies an object report.
- `OBJECT___` is a fixed nine-character object name; spaces pad shorter names.
- `*` marks the object as live.
- The remaining position fields follow the normal APRS position rules.

Names are case-sensitive. Choose a clear, stable name so receivers can recognise later updates as the same object.

## Updating and removing

Send a new report with the same object name to update its position or comment. An object can be marked as killed by replacing the live marker with `_`. Receivers should then stop displaying it, while retaining the report as history where their software supports that.

Only the station responsible for an object should remove it. This prevents competing reports from making an active object appear and disappear unexpectedly.

## Object or item?

Use an **object** when the name is fixed at nine characters and a timestamp is useful. An APRS **item** has a variable-length name and no timestamp, so it is better suited to simpler short-lived reports.

## Sources

- [PROTOCOL.TXT — object format, replacement and killed objects](/APRS-SPEC/PROTOCOL.TXT)
- [Objects.txt — object behaviour and display notes](/APRS-SPEC/Objects.txt)
