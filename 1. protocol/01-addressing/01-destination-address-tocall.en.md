---
title: Destination Address, TOCALL and ALTNET in APRS
description: How APRS uses the AX.25 Destination Address field, what a TOCALL is, how it identifies software and devices, and what ALTNET is used for.
template: doc
tableOfContents: true
---

In an APRS frame:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

the element:

```text
APBOX0
```

is the **Destination Address**, the destination address field of the AX.25 frame.

The name can be misleading. In classic AX.25 the Destination Address really does indicate the destination of a frame, but APRS also uses this field to carry its own information.

In typical modern APRS packets it most often contains a **TOCALL**, an identifier of the software, device or firmware that generated the packet.

Destination Address and TOCALL are not equivalent concepts. A TOCALL is only one of the ways APRS uses the Destination Address field.

## Destination Address in APRS

The Destination Address uses the same AX.25 address format as the Source Address. The field can contain up to six characters and an SSID.

APRS, however, uses it in several different ways. Depending on the packet type, it may contain:

- a TOCALL identifying software or a device,
- a generic APRS address,
- data encoded by Mic-E,
- an ALTNET network name,
- historical information associated with additional APRS mechanisms.

For this reason, the string following the `>` character must not automatically be treated as the callsign of a recipient.

## TOCALL

The most common modern use of the Destination Address is to identify the software or device that generated a packet.

Example:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

Here:

```text
SQ9MDD-4
```

is the Source Address, while:

```text
APBOX0
```

is the Destination Address used as a TOCALL.

`APBOX0` is the APRSBox identifier registered in the current APRS device identifier database.

A TOCALL allows receiving APRS software to identify which application, firmware or device generated a packet. It does not identify the packet recipient.

## Current TOCALL registry

Current APRS identifier allocations are maintained in:

[github.com/aprsorg/aprs-deviceid](https://github.com/aprsorg/aprs-deviceid)

The primary source file is:

[tocalls.yaml](https://github.com/aprsorg/aprs-deviceid/blob/main/tocalls.yaml)

The repository contains the current TOCALL and Mic-E identifier database. The YAML file is the source from which additional machine-readable versions are generated.

The source code of the identifier lookup interface is maintained separately in:

[github.com/aprsorg/aprs-deviceid-web](https://github.com/aprsorg/aprs-deviceid-web)

When creating a new APRS program or device, a random `APxxxx` identifier should not simply be selected and treated as permanent. The allocation should be checked and registered in the current database.

## What do TOCALLs look like?

Many modern identifiers begin with:

```text
AP
```

followed by characters identifying a particular project, manufacturer or device family.

Examples from the current database can have forms such as:

```text
APBOX0
APDW??
APDR??
```

Not every entry is a single exact value. The registry also supports wildcard patterns, allowing one entry to describe a family of versions or devices.

TOCALL recognition should therefore use the current database rather than a manually copied static list.

## Generic Destination Addresses

Not every valid APRS Destination Address is the TOCALL of a particular program.

The APRS specification also defines generic values used for certain types of traffic, for example:

```text
APRS
BEACON
CQ
QST
TEST
WX
```

The historical specification defines a larger group of such addresses and allows some of them to contain additional characters.

They are part of the way APRS uses the Destination Address, but they do not identify a specific application in the same way as modern TOCALLs.

## Destination Address is not the recipient of an APRS message

This distinction is especially important for messages.

Example:

```text
SQ9MDD-7>APBOX0,WIDE1-1::SP9XYZ   :Test
```

contains:

```text
Source Address:       SQ9MDD-7
Destination Address: APBOX0
```

while the recipient of the APRS message is:

```text
SP9XYZ
```

The recipient is carried inside the Information field, the APRS payload.

`APBOX0` still acts as the TOCALL and identifies the software that generated the packet.

## Mic-E

Mic-E is one of the most important exceptions to the simple assumption:

```text
Destination Address = TOCALL
```

In Mic-E packets, part of the information is encoded directly in the Destination Address.

The six characters of this field participate in encoding position and Mic-E status information, so a string that looks like an unusual destination address may actually contain data.

TOCALL recognition must therefore take the packet type into account. Not every Destination Address can be blindly compared against `tocalls.yaml`.

## ALTNET

**ALTNET**, or Alternate Network, uses the Destination Address as an identifier for a separate APRS network or traffic group.

For example, an experimental network could use:

```text
TEST
```

as its Destination Address.

In that case `TEST` is not a software TOCALL and not a recipient callsign. It identifies a logical network.

ALTNET makes it possible to use the same APRS infrastructure while separating selected traffic from the normal network view. Software can then display or process only packets belonging to the selected ALTNET.

ALTNET is another reason why Destination Address must not be treated as synonymous with TOCALL.

## Historical use of Destination SSID for routing

The original APRS specification also defined the use of the **Destination Address SSID** to select a generic digipeater path.

For SSIDs from `-1` to `-7`, historical paths were defined as follows:

| Destination SSID | Historical path |
|---|---|
| `-0` | use the normal VIA path |
| `-1` | `WIDE-1` |
| `-2` | `WIDE-2` |
| `-3` | `WIDE-3` |
| `-4` | `WIDE-4` |
| `-5` | `WIDE-5` |
| `-6` | `WIDE-6` |
| `-7` | `WIDE-7` |

Values from `-8` to `-15` were intended for directional variants, including north, south, east and west.

This is the origin of references stating that the Destination SSID could specify the desired number of retransmissions.

However, for example:

```text
APRS-2
```

must not be treated as equivalent to the modern:

```text
WIDE2-2
```

They are different mechanisms.

Destination SSID path encoding predates the WIDEn-N mechanism and was intended, among other things, for very simple trackers where a small set of switches could directly select the Destination SSID.

In newer APRS documentation this mechanism is marked as **obsolete**. Modern implementations should carry the path in the AX.25 Digipeater Addresses field designed for that purpose.

## What about the C bits?

The seventh octet of an AX.25 address contains, in addition to the SSID, bits associated with the AX.25 Command/Response mechanism.

They are not part of a TOCALL and do not define the APRS meaning of the Destination Address.

Real APRS implementations use different combinations of these bits. APRS software should therefore not try to determine the meaning of a TOCALL, ALTNET or any other Destination Address use from the C-bit combination.

## How should Destination Address be interpreted?

For a typical modern packet:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

we can read:

```text
SQ9MDD-4   Source Address
APBOX0     Destination Address used as a TOCALL
WIDE1-1    path element
```

This interpretation cannot be applied mechanically to every APRS frame.

Destination Address may serve several functions:

```text
TOCALL
generic APRS address
Mic-E
ALTNET
historical APRS mechanisms
```

Correct interpretation therefore depends on the packet type and protocol context.
