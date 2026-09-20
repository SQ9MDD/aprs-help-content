---
title: Source address, callsign and SSID
description: How APRS identifies the source of a packet using the AX.25 Source Address field, and the roles of the callsign and SSID.
template: doc
tableOfContents: true
---

In the textual representation of an APRS frame, the first element is the source address:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

In this example:

```text
SQ9MDD-7
```

is the **Source Address** of the AX.25 frame. It identifies the station that originated the packet.

It does not necessarily identify the station from which the packet was received directly over the radio channel. If a digipeater retransmits the frame, the Source Address still identifies its original source.

This article deals only with this part of the addressing. The elements following the `>` character are described separately.

## What is the Source Address made of?

An AX.25 source address consists of a callsign and an SSID:

```text
CALLSIGN-SSID
```

For:

```text
SQ9MDD-7
```

these are:

```text
callsign: SQ9MDD
SSID:     7
```

**SSID** stands for **Secondary Station Identifier**.

The callsign and SSID are separate elements of an AX.25 address. The hyphen is only part of its textual representation.

## Callsign

A classic AX.25 address provides up to six positions for the callsign part.

Examples of valid values are:

```text
SQ9MDD
SP5ABC
N0CALL
AB1CDE
```

The string:

```text
SQ5AUTO
```

contains seven characters, so it does not fit in the callsign field of a classic AX.25 address.

The SSID does not count towards this limit because it is encoded separately. Therefore:

```text
SQ9MDD-15
```

still contains the six-character callsign `SQ9MDD`.

AX.25 addresses use uppercase letters and digits. Software may accept lowercase input and normalize it to uppercase.

## SSID

The SSID allows several different addresses to use the same base callsign:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

These are four different AX.25 addresses. They may represent different devices or different roles operating under the same base callsign.

The SSID range is:

```text
0-15
```

This follows from the AX.25 format, where four bits are allocated to this value.

Therefore:

```text
SQ9MDD-15
```

is a valid address, while:

```text
SQ9MDD-16
```

is outside the SSID range defined by AX.25.

### SSID 0

The value `0` is normally omitted from the textual representation.

Therefore:

```text
SQ9MDD
```

and:

```text
SQ9MDD-0
```

represent the same AX.25 address.

In normal APRS use, the first form is the one most commonly seen.

## Why does the SSID matter?

The complete Source Address identifies a particular logical station.

If several devices using the same base callsign are to operate in APRS at the same time, different SSIDs allow them to be distinguished. Each device can then independently transmit its own position, status, messages or other data.

If two devices use the same Source Address, at the addressing level they appear to APRS as the same packet source. This can lead, for example, to positions, statuses or station history being mixed together.

## Does the SSID number define the station type?

Not absolutely.

The APRS community uses popular conventions for some SSID numbers, for example `-7` for portable or handheld equipment and `-9` for mobile stations.

These are operating conventions, not the fundamental purpose of the SSID field.

The primary role of the SSID is to distinguish logical station addresses. The device type should therefore not be inferred solely from the SSID number.

## Source Address identifies the packet source

The Source Address identifies the station that originated the packet, but it does not have to be the name of the item described by the APRS data.

APRS objects are a good example. A station:

```text
SQ9MDD-4
```

may transmit a packet describing an object named:

```text
REPEATER
```

The Source Address is still:

```text
SQ9MDD-4
```

while `REPEATER` is the object name contained in the APRS data.

The Source Address therefore answers the question:

**"Where did this packet originate?"**

It does not always answer:

**"What do the data in this packet describe?"**

## Examples of source addresses

| Address | Valid | Notes |
|---|---|---|
| `SQ9MDD` | yes | callsign `SQ9MDD`, SSID 0 |
| `SQ9MDD-0` | yes | the same address with SSID 0 written explicitly |
| `SQ9MDD-7` | yes | SSID 7 |
| `SQ9MDD-15` | yes | highest SSID value |
| `SQ9MDD-16` | no | SSID outside the 0-15 range |
| `SQ5AUTO` | no | callsign contains seven characters |

## What comes next?

In the example frame:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

we now know the meaning of the first element:

```text
SQ9MDD-7
```

The next article describes the **Destination Address**, the field immediately following the `>` character, and how APRS uses it, including as a TOCALL.
