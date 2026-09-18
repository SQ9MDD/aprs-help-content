---
title: APRSBox
description: A modern APRS console for Linux and Raspberry Pi combining Packet Routing, DIGI, iGate, APRS-IS, messaging, mapping, warnings, statistics and propagation analysis.
template: doc
tableOfContents: true
---

**APRSBox** is open-source software for building a complete APRS station on Linux. The project is developed by **SQ9MDD** and released under the **GPL-3.0** license.

It is not merely an iGate or digipeater program. APRSBox combines, in one application, the functions of a home APRS station, digipeater, iGate, APRS-IS client, traffic monitor, messaging system, map, warning display, statistics and diagnostic tools.

The project is designed primarily for continuous operation on small computers such as Raspberry Pi, but it can also run on other Linux systems.

Official repository:

https://github.com/SQ9MDD/APRSBox

## Project character

APRSBox can be treated as a modern, browser-managed APRS console. A single installation can simultaneously provide functions that, in a traditional station, often require several independent programs.

APRSBox can operate as:

- a home APRS station,
- a digipeater,
- a fill-in digipeater,
- an RX iGate,
- a controlled TX iGate,
- a messaging iGate,
- an APRS-IS client,
- a weather station,
- a local APRS channel monitor,
- a warning terminal,
- a propagation analyzer,
- a node with multiple radio interfaces.

APRSBox does not replace the radio modem. It communicates with the radio through an external TNC or modem exposing KISS, for example Dire Wolf, VP-Digi or another compatible device.

## Architecture

The application is written in Python and uses FastAPI.

The system is split into two main processes:

- `app.main` - the web interface, configuration and administration,
- `app.core_main` - the APRS Core process responsible for runtime operation.

APRS Core handles frame reception and transmission, AX.25 and APRS decoding, Packet Routing, DIGI operation, APRS-IS, messaging, schedulers, objects, bulletins, weather, statistics and other real-time tasks.

Configuration and operational data are stored in SQLite. Separating the GUI from APRS Core reduces the impact of web-interface activity on the critical packet-processing path.

## Interfaces and data sources

APRSBox can use several interfaces at the same time.

Supported interfaces include:

- KISS TCP,
- KISS Serial,
- APRS-IS,
- OpenWebRX over MQTT.

A single installation can therefore receive data from several sources, use several transmitters and route traffic between them according to Packet Routing rules.

## KISS TCP and KISS Serial

KISS TCP allows APRSBox to work with modems and TNCs exposing KISS over an IP network. Typical examples are Dire Wolf, VP-Digi or a remote hardware TNC.

KISS Serial allows a TNC to be connected directly through a serial port. Configuration includes the device path and port speed, while the runtime provides mechanisms required for stable continuous operation.

A KISS interface can be used both as an RX source and a TX destination.

## OpenWebRX MQTT

APRSBox provides a receive interface using MQTT from OpenWebRX.

In addition to classic APRS frames, it can receive data from other decoders available in OpenWebRX, including:

- SONDE,
- ADS-B.

Radiosonde and ADS-B data can be represented in the system as APRS objects and displayed in the interface.

OpenWebRX MQTT is a receive source and is not a direct RF transmitter.

## APRS-IS

APRS-IS is a full part of the APRSBox architecture.

Configuration includes:

- server,
- port,
- login,
- passcode,
- APRS-IS filter,
- connection diagnostics.

APRS-IS can be both a source of received data and a Packet Routing destination.

## Packet Routing

One of the most important parts of APRSBox is **Packet Routing**.

The administrator creates flows following the model:

```text
source -> filters -> action / destination
```

This supports, among other things:

```text
RF -> RF
RF -> APRS-IS
APRS-IS -> RF
Local TX -> APRS-IS
```

These correspond to DIGI operation, RX iGate, controlled TX iGate and forwarding locally generated frames to APRS-IS.

A rule can also end by logging an event or deliberately dropping the frame.

## DIGI and Packet Routing filters

Flows can use multiple consecutive filters.

Available mechanisms include:

- duplicate detection,
- viscous delay,
- path analysis,
- strict mode,
- direct-only reception,
- DIGI protection,
- callsign matching,
- packet type,
- APRS symbol,
- distance,
- rate limiting.

The order of selected filters is controlled to reduce the risk of a configuration causing incorrect or excessive retransmission.

## Duplicate Filter and Viscous Delay

APRSBox includes duplicate detection used during DIGI operation.

It can work with **viscous delay**, a controlled retransmission delay. A fill-in digipeater can wait before transmitting and cancel retransmission if the same frame is heard from another station during that period.

This reduces unnecessary transmissions and channel occupancy.

## DIGI protection and frame freshness

APRSBox includes safeguards against retransmitting frames that a digipeater should not repeat.

Checks can include:

- invalid path elements,
- frames already repeated by the local station,
- third-party packets,
- local APRS messages and queries,
- situations where further retransmission should not occur.

The system also monitors the age of frames waiting in queues. A packet that has waited too long because of load or a transport problem can be dropped instead of being transmitted later as stale information.

## RX iGate and TX iGate

In the **RF -> APRS-IS** direction, APRSBox can operate as a classic RX iGate. This path is designed with low latency in mind.

In the **APRS-IS -> RF** direction, APRSBox provides a controlled TX iGate. APRS messaging and information about whether the destination station was heard locally are particularly important here.

APRS-IS -> RF is not unconditional forwarding of all Internet traffic. Rules and filters protect the local radio channel from unnecessary load.

## Local TX and Internal TX

Frames generated by APRSBox are treated as a logical **Local TX** source.

This includes:

- beacon,
- status,
- WX,
- objects,
- items,
- bulletins,
- messages.

Local TX can be routed to APRS-IS independently of physical RF transmission.

A logical **Internal TX** is also available. It does not perform a physical transmission, but allows a locally generated frame to enter further routing.

## Own station, beacon and Proportional Pathing

APRSBox can generate the position of its own station.

Configuration includes:

- callsign and SSID,
- position,
- APRS symbol,
- comment,
- path,
- beacon interval,
- destination TX interface.

A beacon can also be sent manually.

**Proportional Pathing** is available, allowing direct packets to be sent more often while wider DIGI paths are used less frequently, for example:

```text
DIRECT -> short path -> full path
```

This helps reduce unnecessary channel load.

## APRS Status

APRS Status can be transmitted periodically and independently of the position beacon.

This allows additional station information to be published without placing it in every position frame.

## APRS messages

APRSBox provides an APRS conversation interface.

Supported features include:

- numbered messages,
- unnumbered messages,
- ACK,
- REJ,
- retry of messages waiting for ACK,
- duplicate suppression,
- messages received over RF and APRS-IS,
- conversations with individual stations,
- message groups.

Alphanumeric APRS message identifiers are also supported.

A repeated numbered message does not have to create another entry in the conversation, but it can be acknowledged again.

## Group messages and APRS Queries

APRS groups can be defined for the application to listen to, for example:

- `ALL`,
- `QST`,
- `CQ`.

RF and APRS-IS groups can be configured independently.

APRSBox can also respond to selected standard APRS queries, including:

- `?APRS`,
- `?APRSD`,
- `?DX`.

This allows basic information about the station and local activity to be provided through APRS itself.

## Objects, items, bulletins and announcements

APRSBox can manage APRS objects and items.

Available operations include:

- setting position and symbol,
- adding a comment,
- manual transmission,
- periodic transmission,
- setting an expiry time,
- stopping transmission after expiry,
- sending object removal information.

A bulletin and announcement scheduler is also available. Transmissions can be spaced over time so that several active elements do not create one sudden burst on the radio channel.

## Weather

APRSBox can generate and process APRS weather data.

WX data are decoded and used in station views, on the map and in filters.

The parser supports classic weather fields and additional extensions used in APRS.

## Traffic Monitor

Traffic Monitor displays live traffic passing through APRSBox.

It can distinguish:

- RX,
- TX,
- traffic from different interfaces,
- RF -> APRS-IS,
- APRS-IS -> RF,
- locally generated frames.

Filters by direction, interface and frame contents make this view useful as an analyzer of local APRS traffic.

## Station list

Received frames are processed into the current state of each station.

The view can include:

- callsign,
- last activity,
- position,
- distance,
- symbol,
- frame source,
- WX data,
- speed and course,
- Mic-E information,
- identified device or software.

Stations can be filtered by type and source.

## Map

APRSBox includes an advanced Leaflet-based map.

The map can display:

- stations,
- objects and items,
- mobile station tracks,
- PHG coverage,
- Maidenhead grid,
- NWS-WARN areas,
- CAWF warning areas,
- PL-WARN,
- ES-WARN.

The view can be filtered by source interfaces.

## PHG, tracks and overlapping stations

If a station transmits PHG data, APRSBox can display its approximate radio coverage area.

Tracks can be shown for mobile stations.

Where several stations occupy the same or nearly the same position, APRSBox can cluster markers and spread overlapping symbols at an appropriate zoom level so that each station remains accessible.

## Maidenhead grid

The map can display a Maidenhead locator grid.

The level of detail changes with zoom, making the layer useful both for general orientation and more precise operator work.

## APRS Emergency

APRSBox detects frames related to **APRS Emergency** and presents them in a dedicated part of the interface.

The system can:

- group alerts by full source callsign,
- retain the history of related frames,
- show the number of subsequent events,
- display global notifications,
- play an audible signal,
- allow a specific alert to be muted temporarily or indefinitely.

Removing an alert from the list does not necessarily remove its source frames from Traffic Monitor, so the event can still be analyzed.

## NWS-WARN

APRSBox supports **NWS-WARN**, the area-warning mechanism used in the US APRS ecosystem and associated with National Weather Service warnings.

A warning can include the hazard type, validity period and information about the affected area.

APRSBox can use these data to visualize the warning directly on the map instead of presenting only message text.

NWS-WARN support provides compatibility with the existing APRS ecosystem and applications that have used this mechanism for many years.

## CAWF, Common APRS Warning Format

APRSBox also supports **CAWF, Common APRS Warning Format**.

CAWF is designed for distributing area-based warnings over APRS independently of a specific national source system.

The format can carry standardized information including:

- hazard type,
- severity level,
- affected area,
- validity period,
- warning identifier,
- data source.

Data from national warning systems can be normalized by a warnHUB server, sent over APRS and interpreted consistently by a CAWF-compatible client.

## PL-WARN

In Poland, CAWF is used by **PL-WARN**.

warnHUB retrieves official warnings, normalizes them into CAWF and forwards them to the APRS network.

APRSBox includes implemented administrative areas for Poland and can associate an area identifier with the correct map geometry.

This means a PL-WARN alert can be presented as a highlighted geographic area rather than only as message text.

## ES-WARN

The same mechanism is supported for Spain through **ES-WARN**.

APRSBox includes the Spanish areas required to visualize CAWF warnings.

PL-WARN and ES-WARN use the same CAWF model, allowing the application to present warnings from different countries in a consistent way.

## Warning areas on the map

NWS-WARN and CAWF warnings can be represented as map layers.

In one view the user can therefore observe:

- APRS stations,
- objects,
- mobile station tracks,
- local activity,
- areas covered by warnings.

For CAWF, APRSBox currently includes implemented areas for Poland and Spain, used by PL-WARN and ES-WARN respectively.

This allows APRSBox to act as a local warning-information terminal receiving data through APRS-IS or other configured sources.

## Station radar, notifications and webhooks

APRSBox includes a station radar function.

Selected callsigns, including masks, can be watched and the system can react when they appear within a defined range.

Selected events can also be forwarded outside the GUI using notification and integration mechanisms, including webhooks and Telegram.

## Band conditions

APRSBox provides automatic analysis of propagation conditions based on local APRS traffic.

The system builds a local baseline describing which stations and distances are normally heard from a given location. Unusual appearances of more distant stations or new geographic areas can indicate improved propagation.

Analysis can operate independently for individual interfaces.

## W0-W5 scale and propagation history

Conditions are presented on a simplified **W0-W5** scale.

The assessment can consider:

- number of heard stations,
- normal local range,
- distances,
- repeatability of long-distance reception,
- appearance of new areas,
- maturity of collected data.

The model is not based solely on one most-distant station.

APRSBox also stores propagation assessment history and diagnostic data that help explain the current score.

## Statistics and TOP lists

APRSBox maintains traffic and activity statistics.

Available data include:

- APRS frame types,
- direct and total traffic,
- routing directions,
- radio activity,
- most active users,
- identified devices and applications.

The system can show top users by `CALLSIGN-SSID` and top devices identified using information such as TOCALL and Mic-E.

## Dashboard and diagnostics

The dashboard shows the current state of the entire installation.

It can include:

- RF activity,
- traffic,
- service status,
- interfaces,
- APRS-IS,
- configured routing directions,
- own station.

Packet Routing provides step-by-step diagnostics. The administrator can see which rule was triggered, which filters were executed, where a packet was rejected, whether it reached TX and what the execution, queue and worker timings were.

## Multiple interfaces, queues and pacing

APRSBox does not assume that a station has only one modem.

A single installation can have multiple KISS interfaces, while Packet Routing defines which receiver may forward traffic to which transmitter.

TX queues are separated per interface, so a slow or problematic TNC does not have to block the others.

APRSBox also controls spacing between locally generated frames. Beacon, status, WX, objects, bulletins and messages can be distributed over time instead of being transmitted as one sudden burst.

## APRS parser

APRSBox has its own APRS decoding layer used by the map, station views, messaging, weather and statistics.

Supported data include:

- classic APRS positions,
- compressed positions,
- Mic-E,
- position ambiguity,
- objects,
- items,
- messages,
- ACK and REJ,
- query,
- status,
- telemetry,
- weather,
- PHG.

## Symbols and web interface

The interface supports symbols from the primary and alternate APRS symbol tables together with their descriptions.

The GUI includes:

- light and dark themes,
- responsive navigation,
- contextual help,
- diagnostic views,
- dynamically updated traffic and station state.

The interface is translated into Polish, English, Spanish and German.

## Backup, update, SQLite and HTTPS

APRSBox configuration can be exported and restored.

Native installations provide a GUI update mechanism, and a database backup can be created before an update.

SQLite diagnostics include information such as database size, WAL, page counts and integrity checks.

Runtime data can be cleared without deleting the complete station configuration.

A native installation can also be configured to use HTTPS with a custom certificate and private key.

## Installation and Docker

The project provides installation scripts for:

- Debian,
- Raspberry Pi OS and other Debian-compatible systems,
- Alpine Linux.

Both `systemd` and `OpenRC` are supported.

APRSBox can also run in Docker. Data and logs can be stored in volumes. In a container, some operations that directly manage the host are disabled, and updates are performed by replacing the image while keeping the data.

## Raspberry Pi and low-power hardware

The project is developed with small computers and continuous operation in mind.

The code uses mechanisms such as:

- bounded queues,
- separate transmission workers,
- configuration caching,
- moving heavier operations outside the critical RX path,
- statistics aggregation,
- limiting repeated processing of the same data.

This allows APRSBox to run on hardware substantially less powerful than a typical modern PC.

## Key features

The most characteristic APRSBox functions include:

- Packet Routing,
- multiple TNC support,
- advanced DIGI filters,
- Duplicate Filter and Viscous Delay,
- RX iGate and controlled TX iGate,
- APRS messaging,
- Proportional Pathing,
- map with PHG, tracks and Maidenhead grid,
- NWS-WARN,
- CAWF,
- PL-WARN with Polish areas,
- ES-WARN with Spanish areas,
- APRS Emergency,
- station radar,
- webhooks and notifications,
- local propagation analysis,
- device and user statistics,
- extensive station diagnostics.

The project is actively developed, so its feature set can change faster than descriptions in external documentation.

## Basic information

**Name:** APRSBox  
**Author / main maintainer:** SQ9MDD  
**License:** GPL-3.0  
**System:** Linux  
**Interface:** Web  
**Database:** SQLite  
**Radio interface:** KISS TCP / KISS Serial  
**Additional RX source:** OpenWebRX MQTT  
**APRS-IS:** RX/TX  
**Installation:** Debian, Raspberry Pi OS, Alpine Linux, Docker  
**Repository:** https://github.com/SQ9MDD/APRSBox
