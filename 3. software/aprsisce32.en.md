---
title: APRSISCE/32
description: Advanced APRS client for Windows and Windows Mobile supporting maps, RF, APRS-IS, messaging, objects, MultiLine, NWS-WARN, direction finding, iGate and many other APRS protocol features.
template: doc
tableOfContents: true
---

APRSISCE/32 is one of the most advanced classic APRS clients.

The program combines in a single application:

- APRS mapping,
- APRS-IS connectivity,
- radio and TNC support,
- messaging,
- objects and items,
- telemetry,
- weather,
- iGate,
- beaconing,
- station tracking,
- Direction Finding,
- MultiLine objects and areas,
- NWS-WARN support,
- advanced APRS-IS filtering.

The project was created by **Lynn Deffenbaugh KJ4ERJ**.

Project documentation:

https://aprsisce.wikidot.com/

Downloads:

https://aprsisce.wikidot.com/downloads

User and support group:

https://groups.io/g/APRSISCE

APRSISCE/32 is especially interesting because it implements much more of the APRS protocol than just station positions and simple text messages.

## APRSISCE and APRSIS32

The project name covers two main program variants.

### APRSISCE

APRSISCE was intended for devices running:

- Windows Mobile 5,
- Windows Mobile 6,
- Windows Mobile 6.1,
- Windows Mobile 6.5,
- Windows CE.

This made it possible to use a full APRS client on handheld computers, PDAs and industrial mobile terminals.

### APRSIS32

APRSIS32 is the Windows desktop version.

Despite the name, it also works on 64-bit editions of Windows.

The current project download page provides a working build for 32-bit and 64-bit Windows, including Windows 10 and Windows 11.

The Windows Mobile and Windows CE variants are now mainly of historical interest.

## Full APRS terminal

The simplest operating mode looks like this:

```text
APRS-IS
   |
   |
APRSIS32
   |
  map
```

The full capabilities become visible after connecting a radio:

```text
Radio
  |
TNC / modem
  |
APRSIS32
  |
APRS-IS
```

In such a setup one application can simultaneously:

- receive stations from RF,
- display them on a map,
- transmit its own beacons,
- handle messages,
- gate packets to APRS-IS,
- send selected APRS-IS traffic to RF,
- create and transmit objects.

## Maps

One of the central APRSISCE/32 functions is mapping.

The program uses tiled maps, primarily OpenStreetMap.

Other map sources can also be configured.

Tiles are stored locally, so previously downloaded areas can remain available when Internet connectivity is limited.

This is useful during:

- field operation,
- amateur radio events,
- exercises,
- search activities,
- mobile operation.

## Stations on the map

APRSISCE/32 does more than display a symbol and callsign.

Depending on packet contents it can show:

- callsign,
- APRS symbol,
- comment,
- altitude,
- speed,
- course,
- weather,
- movement track,
- operating frequency,
- messaging information,
- position precision.

The program also supports **position ambiguity**.

If a station intentionally transmits a reduced-precision position, APRSISCE/32 can display the area in which the station may actually be located.

## Station tracking

A selected station can be followed on the map.

The program can automatically pan the map as the station moves.

There is also:

```text
MultiTrack
```

which allows a separate tracking window for a selected station.

## Movement history

APRSISCE/32 records successive station positions and can build a track from them.

Tracks can also be saved as:

```text
GPX
```

## Scroller

A very characteristic APRSIS32 feature is the **Scroller** on the left side of the main window.

The Scroller provides a quick overview of what is currently happening in the network.

Markers can distinguish, among other things:

```text
*  packet received directly by RF
#  packet related to APRS-IS -> RF transmission
@  packet repeated by the local digipeater
```

Additional colours and symbols help identify traffic type and origin.

This makes APRSIS32 a convenient diagnostic tool for observing APRS channel activity.

## Radio support

APRSISCE/32 can work with many devices and modems.

Project documentation lists, among others:

- classic TNCs,
- KISS TNCs,
- Kenwood TM-D700,
- Kenwood TM-D710,
- Kenwood TH-D7,
- Kenwood TH-D72,
- Kenwood TH-D74,
- Argent OT-USB,
- TinyTrak4,
- AGWPE,
- UZ7HO SoundModem,
- Dire Wolf.

Both serial ports and network connections are supported.

## KISS

The program can work directly with KISS devices.

Available modes include:

```text
KISS
Simply KISS
```

`Simply KISS` is intended for devices that are immediately ready to operate in KISS mode after startup.

The regular KISS mode can additionally send commands required to switch a TNC into the proper mode.

## AGW

APRSIS32 can also use the AGW interface.

This allows it to work with software modems such as:

- UZ7HO SoundModem,
- Dire Wolf.

Example:

```text
Radio
  |
sound card
  |
Dire Wolf
  |
AGW
  |
APRSIS32
```

or:

```text
Radio
  |
sound card
  |
UZ7HO SoundModem
  |
AGW
  |
APRSIS32
```

This arrangement allows APRSIS32 to serve as the full user interface while AFSK decoding is handled by a separate software modem.

## Kenwood TM-D710

Integration with Kenwood radios that include built-in APRS support is particularly extensive.

With the TM-D710, APRSISCE/32 can display more than what is visible on the radio itself.

The program can decode data types that the D710 does not itself present, including:

- Direction Finding reports,
- MultiLine objects,
- full station track history.

## APRS-IS

APRSISCE/32 provides full APRS-IS connectivity.

The program can receive simultaneously:

```text
RF + APRS-IS
```

and combine both sources into one operational picture.

## Advanced APRS-IS filters

The program supports APRS-IS filters compatible with javAPRSSrvr.

Filtering can be based on:

- area,
- radius,
- callsign,
- prefix,
- digipeater,
- iGate,
- objects,
- symbol,
- packet type,
- group messages.

Example:

```text
m/50
```

means traffic within 50 km of the station's own position.

## iGate

APRSIS32 can operate as an iGate.

Packets received locally over RF can be forwarded to APRS-IS.

```text
RF
 |
Radio
 |
APRSIS32
 |
APRS-IS
```

Selected APRS-IS traffic can also be transmitted to RF.

Individual port configuration can control, among other things:

- RF to IS,
- IS to RF,
- Messages,
- Bulletins/Objects,
- Beacon,
- Telemetry,
- transmit enable.

## Digipeater

APRSIS32 also includes digipeater functionality.

Path transformations can be defined, for example:

```text
WIDE1-1=WIDE1*
```

allowing a local or temporary digipeater to be created.

## APRS messaging

The program has an advanced APRS messaging interface.

Supported features include:

- text messages,
- message identifiers,
- ACK,
- retransmissions,
- conversations with multiple stations.

APRSISCE/32 also correctly handles message paths such as:

```text
RF
 |
iGate
 |
APRS-IS
 |
iGate
 |
RF
```

including the return path for ACKs.

## Message-able

The program can identify stations that are likely to support APRS messaging.

The function:

```text
View Message-able
```

can highlight such stations based on packet type, application identifier and previous messaging activity.

## Objects

APRSISCE/32 provides advanced APRS object support.

Objects can represent, for example:

- repeaters,
- frequencies,
- meeting points,
- aid points,
- hazards,
- technical stations,
- activity locations.

An object can include:

- position,
- symbol,
- comment,
- path,
- interval,
- frequency,
- CTCSS tone,
- QSY information.

Objects can be grouped and managed as sets.

## Frequency objects

APRSISCE/32 correctly interprets APRS Frequency Specification.

An example object comment can look like:

```text
145.650MHz T077 -060
```

From this the client can read:

- frequency,
- tone,
- offset direction,
- offset value.

## QRU, the APRS Info-kiosk

One particularly interesting APRSIS32 feature is **QRU**.

It allows a large number of objects to be stored locally and transmitted only when someone requests them.

```text
mobile station
     |
     | INFO
     v
QRU server
     |
     | group list
     v
mobile station
```

The user can then request, for example:

```text
FUEL
FOOD
HOSP
RP2M
```

and receive objects located within a selected radius of their current position.

This reduces unnecessary RF traffic.

## MultiLine

APRSISCE/32 supports the **MultiLine** extension.

It allows APRS to carry not only a point, but also:

- a line,
- a route,
- a boundary,
- a polygon,
- an area.

The program can both receive and create this data.

Compressed coordinates are also supported, allowing greater precision within the limited packet length.

## Area objects

MultiLine makes it possible to create objects that represent more than a single point.

For example:

- operational area,
- closed zone,
- search area,
- event grounds,
- hazard area,
- route.

```text
+-------------------+
|                   |
| OPERATIONAL AREA  |
|                   |
+-------------------+
```

APRSIS32 can draw such an area directly on the map and encode it in an APRS object comment.

## NWS-WARN

One of the most advanced APRSISCE/32 functions is support for **National Weather Service** warnings.

NWS-WARN uses APRS to distribute weather warnings and information about the areas they affect.

APRSISCE/32 can:

- receive NWS objects,
- identify the warning area,
- display warnings on the map,
- use local shapefiles,
- map warning identifiers to actual administrative or meteorological boundaries.

The program uses:

```text
SHP
SHX
DBF
```

files to draw accurate alert boundaries on the map.

## Why NWS-WARN matters

This feature is a good example of what APRS can do beyond position tracking.

```text
short APRS information
        |
        v
area identifier
        |
        v
local shapefile
        |
        v
warning area on map
```

This is an efficient way of transmitting area information over the low-bandwidth APRS channel.

## Direction Finding

APRS also defines formats for information used in radio direction finding.

APRSISCE/32 can interpret and visualise **Direction Finding** packets.

It can display, among other things:

```text
DF circles
```

on the map.

This allows measurements from different locations to be plotted and used to estimate the probable location of a signal source.

## Triangulation

Measurements from several locations can be displayed together on one map.

This can be useful for:

- interference hunting,
- fox hunting,
- transmitter location,
- field exercises.

## Weather

APRSISCE/32 supports APRS weather stations.

It can also generate weather packets.

If external software updates:

```text
wxnow.txt
```

APRSISCE/32 can read it, generate a valid APRS Weather packet and send it to APRS-IS, RF or both.

## Telemetry

The program supports standard APRS telemetry.

It can receive and display telemetry from infrastructure, weather and experimental stations.

## APRS Queries

APRSISCE/32 supports standard APRS queries, including:

```text
?APRS?
?IGATE?
?WX?
```

These are not ordinary text messages but a separate APRS packet type.

## Item-In-Message

The program also supports:

```text
Item-In-Message
```

which allows a small APRS item to be carried inside a message.

## Satellites

APRSISCE/32 includes satellite tracking features.

It can use TLE data to calculate satellite positions.

For suitable objects it can also display the predicted coverage footprint as a MultiLine object.

## Duplicate packets

APRSISCE/32 includes duplicate detection mechanisms.

This is especially important when tracking mobile stations and the same position arrives through different paths at different times.

## Beaconing

The program can generate its own position beacons.

Position sources can include:

- static configuration,
- GPS,
- NMEA from a radio port,
- USB GPS receiver,
- Bluetooth GPS.

## GPS

APRSISCE/32 supports standard:

```text
NMEA
```

data from external GPS receivers.

## Windows, Linux and Wine

APRSIS32 is a Windows application.

The documentation also describes running it under Linux using:

```text
Wine
```

It can also be run inside a Windows virtual machine.

## Current project status

APRSISCE/32 has a long history and most advanced functions were developed many years ago.

The project is not completely dead, however.

The download page was updated in 2025 and provides a working APRSIS32 build for modern Windows versions.

## APRS protocol compatibility

One of the greatest strengths of APRSISCE/32 is the broad range of APRS elements it supports.

The program can interpret, among other things:

- positions,
- compressed positions,
- symbols and overlays,
- comments,
- messages,
- ACK,
- status,
- telemetry,
- weather,
- objects,
- items,
- frequencies,
- APRS Queries,
- NWS weather objects,
- MultiLine,
- area objects,
- Direction Finding data,
- Item-In-Message.

This makes APRSISCE/32 an excellent demonstration that APRS is much broader than a simple position-reporting system.

## Who is APRSISCE/32 for?

The program may be interesting for operators who want to:

- observe local APRS traffic,
- use mapping,
- connect their own radio,
- exchange APRS messages,
- run an iGate,
- create objects,
- create areas and routes,
- experiment with Direction Finding,
- analyse weather and warnings,
- work with multiple RF ports,
- explore advanced APRS protocol features.

## Summary

APRSISCE/32 is one of the most complete classic APRS clients.

It combines operator, mapping and infrastructure functions in one program.

It supports both common APRS features and less frequently implemented ones:

- area objects,
- MultiLine,
- Direction Finding,
- QRU,
- APRS Queries,
- NWS-WARN,
- shapefiles,
- telemetry,
- weather,
- satellites.

This makes APRSISCE/32 not only a useful client, but also an excellent tool for demonstrating how broad the APRS protocol really is.

## Documentation

Main sources:

- project site: https://aprsisce.wikidot.com/
- downloads: https://aprsisce.wikidot.com/downloads
- feature documentation: https://aprsisce.wikidot.com/en-contexthelp
- hardware and software: https://aprsisce.wikidot.com/doc:compatible-hardware-software
- APRS-IS filters: https://aprsisce.wikidot.com/aprs-is-filters
- NWS: https://aprsisce.wikidot.com/en-nws
- support group: https://groups.io/g/APRSISCE

Practical materials in Polish:

- APRSIS32 search: https://hamspirit.pl/SQ9MDD/?s=aprsis
- getting started and radio: https://hamspirit.pl/SQ9MDD/?p=1239
- temporary digipeater: https://hamspirit.pl/SQ9MDD/?p=1202
- objects: https://hamspirit.pl/SQ9MDD/?p=1140
- area objects: https://hamspirit.pl/SQ9MDD/?p=1170
- QRU Info-kiosk: https://hamspirit.pl/SQ9MDD/?p=1384
- Direction Finding and triangulation: https://hamspirit.pl/SQ9MDD/?p=1090
