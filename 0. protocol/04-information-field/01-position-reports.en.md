---
title: "Position reports"
---


Position reports are one of the fundamental APRS data types. They convey a station’s location and may also carry information about its movement, characteristics or status. A position may come from a GNSS receiver, be entered manually or be programmed as a fixed location. Both mobile and fixed stations transmit position reports.


## Position report formats


APRS defines several ways to convey a position:

- **Uncompressed Position**: geographic coordinates represented in readable text.

- **Compressed Position**: coordinates encoded using Base91 to reduce report length.

- **Mic-E**: a format that encodes part of the position information in the AX.25 destination address and the remainder in the information field.


The specification also describes positions carried in NMEA sentences and reports using a Maidenhead locator. These have separate identifiers and interpretation rules.


## Position report identifiers


In uncompressed and compressed reports, the first character of the information field, the Data Type Identifier (DTI), identifies the report type:

| DTI | Purpose |
|---|---|
| `!` | Position without a timestamp; the station does not declare APRS messaging capability. |
| `=` | Position without a timestamp; the station declares APRS messaging capability. |
| `/` | Position with a timestamp; the station does not declare APRS messaging capability. |
| `@` | Position with a timestamp; the station declares APRS messaging capability. |


The DTI does not determine whether coordinates are uncompressed or compressed. That is established from the structure of the following data. Mic-E uses separate identifiers: `'` and `` ` ``.


## Report structure


Depending on its format, a position report may contain the following elements:

| Element | Purpose |
|---|---|
| DTI | Data type identifier. |
| Timestamp | Optional timestamp. |
| Position | Geographic coordinates. |
| Symbol | Station graphic symbol identification. |
| Position extension | Optional position extension. |
| Comment | Comment and additional information. |


The presence, order and encoding of individual fields depend on the report format.


### Uncompressed report example


```text
SQ9MDD>APRS:!5003.50N/01956.00E-
```

Here `!` identifies a report without a timestamp, `5003.50N` and `01956.00E` are the coordinates, and `/` and `-` specify the symbol table and symbol code.


## Additional information


Position reports can also include supplementary data. The specification provides, among other things:

- **Course/Speed**: course and speed.

- **PHG**: transmitter power, antenna height, gain and directivity.

- **RNG**: declared station range.

- **BRG/NRQ**: direction-finding information.

- **Altitude**: altitude, including the `/A=` extension.


Availability and encoding depend on the report format; not every extension is valid in every format.


## Position accuracy and ambiguity


Coordinate resolution is not the same as actual measurement accuracy. APRS defines **Position Ambiguity**, which deliberately specifies an area rather than an exact point. The **DAO** extension can provide additional coordinate precision.

These mechanisms serve different purposes and must be interpreted separately.


## Station positions versus objects and items


An ordinary position report describes the station identified by the frame’s source address. APRS also supports **Objects** and **Items**, allowing a named object or item to have a reported position independently of the transmitting station’s callsign.

Objects and items reuse APRS position encoding mechanisms but have their own formats and identification rules. They are documented separately.


## Detailed documentation


Separate articles cover uncompressed positions, timestamps, Position Ambiguity, position extensions, altitude and precision, symbols, position comments and compressed formats, including Mic-E.


## Reference


[APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF).