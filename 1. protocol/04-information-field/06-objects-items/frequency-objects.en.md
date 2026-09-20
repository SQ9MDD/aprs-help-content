---
title: Frequency Objects
description: APRS objects that advertise local radio frequencies.
---

Frequency objects publish useful local radio resources on the APRS map: a recommended voice repeater, EchoLink or IRLP node, Winlink gateway, or a net. They are normal APRS objects with a structured frequency name or comment.

## Keep the information local

A frequency object is useful only where a traveller can act on it. Send it conservatively and follow the local RF operating plan; unnecessary relays increase congestion on a shared channel.

## Frequency in a comment

The common readable form is a frequency followed by `MHz`, optionally with tone, offset and range:

```text
146.805MHz T107 R25m
```

`T107` describes a 107.x Hz tone and `R25m` a nominal range of 25 miles. The full frequency convention also defines CTCSS/DCS, modulation, offset and kilometre forms. Treat these as structured conventions for compatible APRS clients—not as a substitute for the local frequency plan.

## Frequency as an object name

For a repeater, the frequency can appear in the object name, followed by extra characters that make it unique. The comment then carries tone, offset, range and a short human-readable note.

```text
;147.105md*111111zDDMM.hhN/DDDMM.hhWrT107 R25m Local repeater
```

This is an illustrative layout. Actual coordinates, symbol, object-name suffix and timing must be selected for the real station.

## Special services

EchoLink, IRLP, WiRES and Winlink can use the same object mechanism. Their naming conventions help mobile clients identify the service, but availability and status should come from the operator or a reliable local source.

## Sources

- [freqspec.txt — frequency formats and service-object conventions](/APRS-SPEC/freqspec.txt)
- [PROTOCOL.TXT — APRS object frame](/APRS-SPEC/PROTOCOL.TXT)
