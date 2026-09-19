---
title: "APRS Queries"
---

An APRS query is a request mechanism that allows one station to ask another station, server, or APRS service for specific information or for a defined response. Instead of waiting for the required information to appear in normal APRS traffic, an operator can actively request it when it is needed.

Queries are especially useful because APRS is primarily a broadcast-oriented system. Stations periodically transmit positions, status reports, weather data, objects, telemetry, and other information, but this does not mean that every piece of information is available at exactly the moment a user wants it. A query makes it possible to retrieve information on demand without increasing the beacon rate of the entire network.

Typical examples include requesting the current position of a station, its status, software version, a list of stations heard directly, or information about IGATE operation. Other queries are not addressed to an individual station, but to services operating within the APRS ecosystem, such as object servers, directories, group services, callsign lookup systems, weather services, or gateways to other communication systems.

There are two basic types of APRS queries:

general queries, addressed to all stations or to a particular class of stations,
directed queries, sent as an APRS message to a specific callsign or service name.

A query usually begins with the ? character followed by a query type identifier. The response depends on what the receiving station or service supports. Not every APRS implementation is required to support every query, so different programs and devices may provide different sets of commands.

An important characteristic of queries is that they are intended as one-time requests for information. They are not used to establish a connected session as in traditional packet radio. A station sends a query, and if the receiving side recognizes and supports it, it returns an appropriate position report, status, message, or other APRS packet.

Queries should also not be confused with ordinary APRS messages that require acknowledgement. Standard APRS queries do not contain a message identifier intended for ACK handling, and responses should not create additional acknowledgement traffic. This prevents a simple information request from producing an unnecessary chain of packets.

In practice, queries are used when information is useful on demand but does not need to be broadcast continuously. Instead of increasing the transmission rate of status information or station-heard lists, such data can be requested only when it is actually needed. This fits the APRS design philosophy well, especially on radio channels where unnecessary traffic should be minimized and available capacity should be used only when it provides real value.

The modern APRS ecosystem has extended the query concept far beyond the original station-to-station requests. This is why this document includes not only classic commands such as ?APRSP, ?APRSS, and ?APRSD, but also queries to services such as ANSRVR, QRU, AVRS, WLNK-1, WHO-IS, WXBOT, and WHERE-IS. They all follow the same general idea: an APRS station sends a short request, and the remote side returns specific information or performs a defined operation.

## General notes

- UI-View is **case sensitive**.
- For calls involving a callsign, use the **SSID if one exists**.
- This file was prepared **exclusively from the supplied PDF**, without using any other sources.

---
## 1. Standard APRS queries
| Input | To | Meaning / response | Example |
|---|---|---|---|
| `?APRS?` | `callsign` | Request for a list of all queries that the station/system can answer | `?APRS?` |
| `?WX?` | `CQ` | Query to all weather stations | `?WX?` |
| `!PROGRAMNAME.EXE` | `callsign` | UI-View can launch a program from the RCOMMAND directory, e.g. `!Orbitron.exe` or `!Orbitron` | `!help` |
| `?ABOUT` | `callsign` | Station software version, operating system, CPU load, equivalent to `?APRSV` and `?VER` | `?APRST APRSIS32 Win v6.1 b7601 p2 9.1/6.4%` |
| `?APRSD` | `callsign` | Stations heard directly, without digipeater hops | `?APRSD` |
| `?APRSH` | `callsign` | Stations heard together with packet path type: `I`, `G`, `D`, `d`, `R` | `?APRSH` |
| `?APRSH DF8LS-9` | `callsign` | Whether the station has heard the specified callsign, response includes statistics from the last 18 hours | `?APRSH DF8LS-9` |
| `?APRSL` | `callsign` | Stations heard locally within the last 30 minutes, up to 2 hops | `?APRSL` |
| `?APRSM` | `callsign` | Message query, asks whether the station has messages queued for me | `?APRSM` |
| `?APRSO` | `callsign` | Object query, sends active objects created by the station, excluding QRU-server objects | `?APRSO` |
| `?APRSP` | `callsign` | Position query, usually causes the receiving station to send a beacon | `?APRSP` |
| `?APRSS` | `callsign` | Status query, e.g. APRSIS32 port status | `?APRSS` |
| `?APRST` | `callsign` | Trace of station packets to the APRS-IS Internet interface or over radio, equivalent to `?PING?` | `?APRST` |
| `?APRSV` | `callsign` | Software version, system, CPU, equivalent to `?ABOUT` and `?VER` | `?APRSV` |
| `?CPU` | `callsign` | Software version, system, kernel and user CPU load | `?CPU` |
| `?DX` | `callsign` | Short-form DX report, the most distant station received in the last hour | `?DX` |
| `?IGATE` | `callsign` | Activity on IGATE ports, in APRSIS32 also information about bi-directional operation | `?IGATE` |
| `?PING?` | `callsign` | Same as `?APRST` | `?PING?` |
| `?VER` | `callsign` | Same as `?ABOUT` and `?APRSV` | `?VER` |

### Meaning of abbreviations in `?APRSH`

- `I` - packets from the Internet, including APRS-IS itself
- `G` - packets from gated stations, callsign-SSID after the `qAS`, `qAR`, etc. construct
- `D` - digipeated station packets whose first copy arrived from the Internet
- `d` - digipeated packets heard locally
- `R` - packets heard over radio

---
## 2. UI-View-only functions

| Input | To | Meaning |
|---|---|---|
| `BCN` | `callsign` | Position and comment text as a beacon packet |
| `LGS` or `LG1` | `callsign` | Start logging all received stations |
| `LGX` or `LG0` | `callsign` | Stop logging received stations, command must originate from the station itself |
| `QAS` | `callsign` | All heard stations |
| `QWS` | `callsign` | Heard weather stations |

---

## 3. ANSRVR and CQSRVR
### ANSRVR
| Input | To | Meaning |
|---|---|---|
| `?` | `ANSRVR` | List of available interest groups, without a time or activity limit |
| `? GROUP NAME` | `ANSRVR` | Number of members in the given group |
| `D GROUP NAME` | `ANSRVR` | Group description and member list |
| `L` | `ANSRVR` | Groups I belong to |
| `J GROUP NAME` | `ANSRVR` | Join a group, membership valid for 12 hours |
| `U GROUP NAME` | `ANSRVR` | Leave a group |
| `CQ GROUP NAME Text...` | `ANSRVR` | Join the group and send a message to members at the same time |

### Notes on `CQ GROUP NAME Text...`

- If the group does not exist, the sender becomes its owner.
- When creating a group, its name may contain at most **46 characters**.
- The owner can delete the group only after it has no members.

### CQSRVR

| Input | To | Meaning |
|---|---|---|
| `INFO` | `CQSRVR` | List of short-term groups, membership and the group expire after 12 hours without activity |

---

## 4. QRU-Server
### General queries

| Input | To | Meaning |
|---|---|---|
| `INFO` | `QRU` | Which QRU object groups are available within my geographic radius, by default e.g. 50 km |
| `INFO 250` | `QRU` | Same, but for a 250 km radius |
| `OBJECT GROUP NAME` | `QRU` | Which objects from the given group are available within my radius |
| `OBJECT GROUP NAME 150` | `QRU` | Which objects from the given group are available within a 150 km radius |

### QRU response behavior
- For a query over the Internet, all objects are listed.
- In APRSIS32, map windows showing their locations may also open.
- For a query over radio, a final message such as `Sent 5 RP70 Objects Max 5@50km` is returned.
- The objects are then sent through the IGATE and may appear on the radio display, station list, or navigation system.
- The document states that objects do not appear on aprs.fi when the query was made over the Internet, but do appear when the query was made over radio.

### QRU object groups
| Code | Meaning |
|---|---|
| `AERO` | Aerodrome |
| `AIRP` | Airport |
| `AMBU` | Ambulance |
| `CLUB` | Amateur Radio Club |
| `CIVD` | Civil Defense |
| `ECHO` | EchoLink |
| `FIRE` | Fire Department |
| `FOOD` | Restaurant |
| `FUEL` | Gas Station |
| `HELI` | Rescue Helicopter |
| `HOSP` | Hospital |
| `INFO` | Info Kiosk Collection |
| `LIFEBOAT` | Lifeboat |
| `POLI` | Police |
| `POST` | Post Office |
| `RAIL` | Railway |
| `RD2M` | 2m D-Star Repeater |
| `RD70` | 70cm D-Star Repeater |
| `RP10` | 10m Repeater |
| `RP23` | 23cm Repeater |
| `RP2M` | 2m Repeater |
| `RP6M` | 6m Repeater |
| `RP70` | 70cm Repeater |
| `SHOP` | Shopping Center |
| `SRAIL` | Steam Railway |
| `STOR` | Amateur Radio Shop |
| `T2SRV` | T2 Server |
| `VETE` | Veterinarian |

---
## 5. AVRS, Automatic Voice Relay System

| Input | To | Meaning |
|---|---|---|
| `?` | `AVRS` | Where the nearest EchoLink, IRLP, or Allstar station is relative to my position |
| `? CALLSIGN-SSID` | `AVRS` | Where the nearest EchoLink, IRLP, or Allstar station is relative to the specified station's position |
| `CALLSIGN-SSID` | `AVRS` | Request for information needed to establish voice contact through the nearest EchoLink node |

### Note

For AVRS to work correctly, at least one position beacon must previously have reached APRS-IS.

---
## 6. APRSlink, WLNK-1

APRSlink is used for queries by Winlink users who have an address in the form `callsign@winlink.org`.

| Input | To | Meaning |
|---|---|---|
| `H` | `WLNK-1` | Help |
| `I` | `WLNK-1` | APRSlink information |
| `?L` | `WLNK-1` | Help for a specific command |
| `L` | `WLNK-1` | List of available messages, usually the last 5 |
| `R<number>` | `WLNK-1` | Read a specific message |
| `Y<number>` | `WLNK-1` | Reply to a specific message |
| `K<number>` | `WLNK-1` | Delete a specific message |
| `F<number>` | `WLNK-1` | Forward a specific message |
| `SP <email/callsign/alias> <subject>` | `WLNK-1` | Start sending a longer email in several steps |
| `/EX` | `WLNK-1` | Finish and send the complete message |
| `P` | `WLNK-1` | Message playback |
| `SMS ...` | `WLNK-1` | Send a one-line message |
| `A ALIAS=mail@provider.net` | `WLNK-1` | Create or update an alias |
| `A ALIAS=` | `WLNK-1` | Delete an alias |
| `AL` | `WLNK-1` | List aliases |
| `G<number>` | `WLNK-1` | Query for the nearest RMS Packet gateways, default 1 |

### `SP` sequence

1. Start: `SP <email or callsign or alias> <subject>`
2. Send successive text fragments
3. Finish with a separate `/EX` message

The document notes that until the terminating command is sent, text may be sent to `WLNK-1` without additional identification.

---
## 7. WHO-IS, callsign queries on QRZ.com

| Input | To | Meaning |
|---|---|---|
| `callsign` | `WHO-IS` | Short query: class / name / country |
| `F callsign` | `WHO-IS` | Full query: name / class / street / city / country |

The document states that the query is also possible through `WHO-15`.

---
## 8. QRZ, object queries

| Input | To | Meaning |
|---|---|---|
| `object name` | `QRZ` | Short query for an object no older than 2 hours: object name and text |
| `object*` | `QRZ` | Version with a trailing `*` wildcard, response contains a selection of available objects |

---

## 9. METAR, weather queries

| Input | To | Meaning |
|---|---|---|
| `ICAO Code` | `WXBOT` | Short query for current airport weather, SA/METAR |

Example from the document: `EDDL`.

---
## 10. WHERE-IS, positions, distance, alerts

**Important:** the document states that `WHERE` and `WHERE-IS` are **case sensitive**.

| Input | To | Meaning |
|---|---|---|
| `callsign` | `WHERE` or `WHERE-IS` | Short query for distance, bearing, and time of the last report |
| `where callsign` | `WHERE` or `WHERE-IS` | Same as above |
| `dir callsign` | `WHERE` or `WHERE-IS` | Direction and time only |
| `direction callsign` | `WHERE` or `WHERE-IS` | Full form of `dir` |
| `dis callsign` | `WHERE` or `WHERE-IS` | Distance and time only |
| `distance callsign` | `WHERE` or `WHERE-IS` | Full form of `dis` |
| `dis callsign @km` | `WHERE` or `WHERE-IS` | Distance in specific units |
| `pos callsign` | `WHERE` or `WHERE-IS` | Coordinates and time only |
| `loc callsign` | `WHERE` or `WHERE-IS` | Same as `pos` |
| `position callsign` | `WHERE` or `WHERE-IS` | Full form of `pos` |
| `location callsign` | `WHERE` or `WHERE-IS` | Full form of `loc` |
| `last callsign` | `WHERE` or `WHERE-IS` | Date and time of the last report only |
| `alert callsign < 15 km` | `WHERE` or `WHERE-IS` | Alert when the station comes closer than the specified distance |
| `alert callsign > 15 km` | `WHERE` or `WHERE-IS` | Alert when the station moves farther than the specified distance |
| `cancel callsign` | `WHERE` or `WHERE-IS` | Deletes the stored alert |
| `help` | `WHERE` or `WHERE-IS` | Shows available commands |

### Units in `WHERE` / `WHERE-IS`

Available units:

- `@miles` or `@mi`
- `@nm`
- `@yards` or `@y`
- `@foot` or `@f`
- `@meters` or `@m`
- `@km`

### Alert limitations

- An alert applies to only **one station**
- Once triggered, the alert is deleted
- The document says that persistent storage of multiple alerts was not yet available

---

## 11. SMSGTE

| Input | To | Meaning |
|---|---|---|
| `@6135551234 free text...` | `SMSGTE` | System connecting APRS and mobile phones |

### Note

The document states that `SMSGTE` is available only in the **USA and Canada**.

---
## 12. Short practical summary

The main query groups from the document are:

- standard APRS queries to stations and systems
- UI-View-specific functions
- ANSRVR and CQSRVR groups
- QRU objects and directories
- AVRS for finding the nearest voice nodes
- APRSlink to Winlink via `WLNK-1`
- WHO-IS and QRZ for callsign and object information
- WXBOT for METAR
- WHERE / WHERE-IS for position, distance, and alerts
- SMSGTE as an APRS-to-SMS bridge
