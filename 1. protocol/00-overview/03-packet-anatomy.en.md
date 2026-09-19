---
title: APRS Packet Structure
description: A short introduction to the parts of an APRS packet.
---

An APRS packet includes an originating station, a destination identifier, an optional path and an information field.

```text
SOURCE>DESTINATION,PATH:information field
```

The information field tells receiving software whether the packet carries a position, message, object, telemetry or another APRS data type.

## Why it matters

Reading the parts separately is the quickest way to diagnose a packet that is not displayed as expected.
