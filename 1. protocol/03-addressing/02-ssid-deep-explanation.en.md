---
title: SSID in AX.25 and APRS
description: Technical explanation of the AX.25 SSID field, APRS conventions, and the differences between RF and APRS-IS addressing.
template: doc
tableOfContents: true
---

SSID, **Secondary Station Identifier**, is part of an AX.25 address. APRS uses this field according to its protocol function and also applies conventions that indicate typical station roles.

The following must be distinguished:

- SSID as an AX.25 address field,
- SSID recommendations used in APRS,
- historical uses of SSID,
- identifiers existing only on APRS-IS or in other systems.

## SSID in AX.25

An AX.25 address consists of six callsign octets and a seventh octet containing, among other fields, the four-bit SSID.

For a source or destination address:

```text
bit:   7 6 5 4 3 2 1 0
       C R R S S S S E
```

For a digipeater address:

```text
bit:   7 6 5 4 3 2 1 0
       H R R S S S S E
```

Meaning:

- `SSSS` - SSID,
- `R` - reserved bits,
- `C` - Command/Response,
- `H` - Has Been Repeated,
- `E` - Extension Bit.

The SSID occupies four bits, so its value range is `0..15`.

This limitation applies to every actual AX.25 address, regardless of whether it is used as a source, destination, or digipeater address.

## SSID in APRS

APRS does not change the AX.25 SSID format. However, conventions were adopted for source stations to suggest the intended role of a station.

These are **recommendations**, not APRS syntax and not a validation rule.

Classic APRS recommendations:

| SSID | Typical use |
|---|---|
| `-0` | operator's primary station, usually fixed and message capable |
| `-1` | generic additional station |
| `-2` | generic additional station |
| `-3` | generic additional station |
| `-4` | generic additional station |
| `-5` | other networks or systems |
| `-6` | special activity, satellites, other bands, etc. |
| `-7` | handheld radio or other human-portable station |
| `-8` | boat, RV, or second primary mobile |
| `-9` | primary mobile station, usually communication capable |
| `-10` | Internet, iGate, EchoLink, Winlink, and similar uses |
| `-11` | balloon, aircraft, spacecraft |
| `-12` | APRStt, DTMF, RFID, devices, one-way trackers |
| `-13` | weather station |
| `-14` | trucker or other full-time driver |
| `-15` | generic additional station |

At the AX.25 level, `CALL-9` is simply a station with SSID `9`. Interpreting it as a primary mobile station comes from APRS convention.

Software should not use this table as a basis for rejecting packets or forcing a station type.

## `-9` and `-12`

In the classic APRS recommendations, `-9` is intended primarily for an operator's main mobile station, usually capable of APRS or voice communication.

`-12` is appropriate, among other uses, for one-way trackers.

A device installed in a vehicle therefore does not automatically need to use `-9`. If it only transmits position and has no return communication capability, `-12` may better describe its role.

## Historical use of SSID for symbol selection

In early APRS, SSID was also used to select a symbol for trackers transmitting raw NMEA-0183 data.

This mechanism was later replaced by other symbol encoding methods, including the `GPSxyz` destination-address scheme and APRS position formats that carry the symbol directly.

A modern source SSID should therefore not be used to determine the station symbol.

## Source, destination, and digipeater SSID

The frame:

```text
SQ9MDD-9>APRS-2,WIDE1-1:
```

contains three independent SSID values:

```text
SQ9MDD-9     source SSID = 9
APRS-2       destination SSID = 2
WIDE1-1      digipeater SSID = 1
```

All are encoded in the same way at the AX.25 level, but their APRS meaning depends on where they occur.

Recommendations such as `-7` handheld, `-9` mobile, or `-13` weather apply primarily to the **source SSID**. Destination SSIDs and SSIDs in the path can be used by other APRS mechanisms and should not be interpreted using the station-role table.

## APRS-IS and AX.25 limitations

APRS-IS uses a textual packet representation and permits a suffix after the hyphen consisting of one or two alphanumeric characters, subject to the remaining APRS-IS format restrictions.

For example, the following identifier is possible:

```text
SQ9MDD-D
```

Such an identifier can be used by a station that exists on APRS-IS, for example a gateway or a system integrating APRS with another network.

It cannot, however, be encoded as a native AX.25 source address on RF because the AX.25 SSID field contains only the four-bit value `0..15`.

An APRS-IS -> RF gateway therefore cannot assume that every valid APRS-IS identifier can be copied directly into an AX.25 address field.

## Third-party traffic

APRS defines a **third-party traffic** mechanism in which the original header and data can be carried inside the information field of another valid AX.25 frame.

This can preserve information about a station originating in another network, but it does not extend the AX.25 address space. An identifier such as `SQ9MDD-D` can occur inside third-party data, but it does not become a native AX.25 source address.

## Identifiers outside the `0..15` range

APRS documentation contains historical examples of suffixes such as:

```text
-63
-tt
-ID
-A ... -Z
```

They were used for objects or stations generated by Internet-based and other systems. They are not additional values of the four-bit AX.25 SSID.

For a native RF address, the range remains `0..15`.

## APRS objects

An APRS object name is carried in the information field, not in the AX.25 address field, and can contain up to nine characters.

Names such as:

```text
TEST-63
TEST-ID
WX-A
```

can therefore contain endings that resemble SSIDs, but those endings are part of the object name and are not subject to the four-bit AX.25 SSID limitation.

## Consequences for gateways and software

An APRS implementation should distinguish validation of a native AX.25 address from validation of an identifier used on APRS-IS.

For an address transmitted directly over RF, the SSID must be within `0..15`.

Software must not automatically assume that every identifier present on APRS-IS can be:

- converted into an AX.25 address,
- retransmitted over RF,
- used as a source address,
- passed through KISS to a TNC as a native frame address.

This is especially important for APRS-IS -> RF gateways, DMR/APRS and D-STAR/APRS integrations, hotspots, telemetry systems, and applications generating Internet-only APRS stations.

## SSID interpretation in software

SSID can be used as auxiliary information about a station's intended role, but it should not override data carried in the packet.

If `CALL-9` transmits a specific APRS symbol, software should use the symbol from the packet. If `CALL-13` does not transmit weather data, the SSID alone does not make the packet a weather packet.

SSID conventions are primarily intended for station organization and human recognition.

## Summary

Key rules:

```text
AX.25 SSID
    4 bits
    range 0..15

APRS source SSID
    uses the AX.25 SSID
    may additionally follow 0..15 recommendations

APRS-IS
    may contain identifiers with no
    direct AX.25 representation

APRS object
    name is carried in the information field
    a suffix after a hyphen does not have to be an SSID

third-party traffic
    may carry a textual representation
    of a station from outside RF, but does not extend AX.25
```

When interpreting a value written after a hyphen, first determine whether it is part of an actual AX.25 address, an APRS-IS identifier, or part of a name carried in the information field.

## Sources

- [AX.25 Link Access Protocol for Amateur Packet Radio, Version 2.2](https://www.ax25.net/AX25.2.2-Jul%2098-2.pdf)
- [APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [Bob Bruninga WB4APR, APRS SSID Recommendations](https://github.com/SQ9MDD/APRSBox/blob/main/APRS-SPEC/SSIDs.txt)
- [APRS-IS - Connecting to APRS-IS](https://www.aprs-is.net/connecting.aspx)
- [APRS-IS - Server Design](https://www.aprs-is.net/ServerDesign.aspx)
