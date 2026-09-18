---
title: APRS Frame Types
description: A reference to APRS information-field data type identifiers (DTIs).
---

An APRS transmission normally uses an AX.25 UI frame. Its information field begins with a **data type identifier** (DTI): one character that tells a decoder how to interpret the data that follows.

```text
SOURCE>DESTINATION,PATH:DTI data
```

This page describes the types of APRS data carried by that field. It does not replace the rules for AX.25 addressing, paths, position formats, or individual weather and telemetry fields.

## Important rules

- The DTI is normally the first character of the information field. The historic `!` position format is an exception: it may occur anywhere in the first 40 characters to support certain older TNC digipeaters.
- A DTI identifies the **format**, not necessarily a separate AX.25 frame type. For example, ordinary messages, acknowledgements, bulletins, and announcements all use `:`.
- A position can be uncompressed, compressed, Mic-E encoded, or a raw GPS/NMEA sentence. The DTI selects the family; the remaining characters select the detailed form.
- Do not transmit identifiers marked obsolete, reserved, or “do not use”. A receiver may still encounter them in old traffic.

## Main operational types

| DTI | Type | What it carries |
|---|---|---|
| `!` | Position without timestamp, no messaging | A station position; also used by the legacy Ultimeter 2000 weather-station format. |
| `=` | Position without timestamp, with messaging | A station position from a station that supports APRS messaging. |
| `/` | Position with timestamp, no messaging | A timestamped position. |
| `@` | Position with timestamp, with messaging | A timestamped position from a messaging-capable station. |
| `'` or `` ` `` | Mic-E position | Compact Mic-E position, with related information encoded partly in the destination address. Older Mic-E variants also exist; the TM-D700 uses `'` for current Mic-E data. |
| `$` | Raw GPS/NMEA data | A GPS sentence carried directly in APRS, or the legacy Ultimeter 2000 form. |
| `;` | Object | A named map object created and controlled by a station; its live or killed state is part of the object data. |
| `)` | Item | A shorter named map item. It may be live or killed, but unlike an object it has no timestamp or owner field. |
| `_` | Positionless weather | Weather observations without an APRS position. |
| `#` or `*` | Peet Bros U-II weather | Weather data in the Peet Bros Ultimeter-II formats. |
| `T` | Telemetry | Non-Mic-E telemetry, typically a sequence number, analogue values and digital bits. |
| `:` | Message family | An addressee plus message text; also the format for acknowledgements, rejections, bulletins, announcements and directed queries. |
| `>` | Status | A station’s one-line status or mission text, optionally preceded by a UTC timestamp. |
| `<` | Station capabilities | Capability tokens, normally sent as the response to an IGate query. |
| `?` | General query | A one-time request addressed to stations in general, such as `?APRS?`, `?WX?`, or `?IGATE?`. |
| `}` | Third-party traffic | An encapsulated APRS packet forwarded across another network. It includes the original source-path header and original payload. |
| `{` | User-defined data | An allocated or experimental extension: `{` followed by a one-character user ID, a one-character packet type, then application data. |
| `,` | Invalid or test data | Data known not to conform to a standard APRS format, for example an invalid GPS fix reported by a tracker. |
| `%` | Direction finding | The Agrelo DFJr/MicroFinder direction-finding format. |

## Position and location reports

The four ordinary position identifiers distinguish two choices: whether a timestamp is present and whether APRS messaging is supported. The following prefixes show the distinction; the coordinate syntax itself is defined separately.

| Prefix | Meaning |
|---|---|
| `!4903.50N/07201.75W...` | Position, no timestamp, no messaging. |
| `=4903.50N/07201.75W...` | Position, no timestamp, messaging enabled. |
| `/092345z4903.50N/07201.75W...` | Timestamped position, no messaging. |
| `@092345z4903.50N/07201.75W...` | Timestamped position, messaging enabled. |

Each can carry a symbol and may be followed by position extensions or a comment. Compressed positions, course/speed, altitude, radio-range, weather data and other extensions are therefore not separate DTIs.

Mic-E is also a position report, but it is encoded compactly across the destination address and information field. A decoder must treat it as Mic-E, not as ordinary printable latitude/longitude text. A raw GPS/NMEA report beginning with `$` is another position-related format that needs its own sentence parser.

## Map objects and items

`; ` introduces an **object**. It contains an object name, a live (`*`) or killed (`_`) marker, a timestamp, position and symbol. An object can represent a repeater, event checkpoint, incident, weather station, service, or another entity placed on the map by its owner.

`) ` introduces an **item**. It contains an item name and a live (`!`) or killed (`_`) marker followed by position data. Use it for concise map entities when object ownership and a timestamp are not needed.

An object or item is not the same as the reporting station. The source callsign identifies the station transmitting the report; the name in the payload identifies the displayed entity.

## Weather and telemetry

Weather can be sent in several ways:

- `_` starts a positionless weather report.
- A position, object, or item may be followed by weather data, so it remains a position/object/item DTI rather than becoming `_`.
- `#` and `*` identify the legacy Peet Bros U-II weather formats.

`T` starts standard non-Mic-E telemetry. The basic telemetry report begins `T#` and contains a sequence number, up to five analogue channels and eight digital bits. Metadata, parameters, units and bit-sense information use the telemetry message format and therefore begin with `:` rather than `T`.

## Messages, bulletins, queries, and responses

The message family uses a fixed-width addressee:

```text
:ADDRESSEE:message text{ID
```

Within this family, the text can be a person-to-person message, `ack`/`rej`, a bulletin (an addressee such as `BLN...`), an announcement, or a directed query. A message identifier is used when acknowledgement handling is required; APRS queries are one-time requests and should not carry one or be acknowledged.

`? ` is the DTI for a general broadcast query. Directed queries use `:` because they are addressed to one station. Responses are not a single separate DTI: their form depends on the requested information and can be a position, object, weather report, status, message, trace, stations-heard report, or capability report.

## Encapsulation and extensions

`} ` means the payload contains an APRS packet that has crossed a third-party network. A parser should unwrap the embedded source, destination, path and inner DTI before decoding the original data; it is not simply a comment prefix.

`{ ` is reserved for a documented user-defined format. After the DTI, the two-character header assigns the data to an author/application and packet subtype. `{{` is the explicitly experimental form. Unsupported user-defined formats should be ignored safely.

## Historic, reserved, and forbidden identifiers

These identifiers are part of the supplied APRS 1.0.1 reference but are not normal choices for new transmitters.

| Identifier | Status in the reference |
|---|---|
| `0x1C`, `0x1D` | Current and old Rev-0 beta Mic-E data; historical non-printable identifiers. |
| `[` | Obsolete Maidenhead grid-locator beacon. |
| `&` | Reserved for a map feature. |
| `+` | Reserved for shelter data with time. |
| `.` | Reserved for space weather. |
| `"`, `(`, `-`, `\`, `]`, `^` | Unused. |
| `A`–`S`, `U`–`Z`, `a`–`z`, `0`–`9` | Do not use as DTIs. |
| `|`, `~` | Do not use: TNC stream-switch characters. |

## Source basis

This overview follows the supplied *APRS Protocol Reference, APRS Protocol Version 1.0.1* (chapter 5, “APRS Data Type Identifiers”), with the supplied weather, message, parser and user-defined-format notes used to clarify related formats and legacy cases.
