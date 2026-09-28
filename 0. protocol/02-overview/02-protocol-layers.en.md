---
title: APRS protocol layers
description: Functional division of APRS transmission over radio and APRS-IS, and the roles of the application, AX.25, the modem, and the radio channel.
template: doc
tableOfContents: true
---

APRS defines how information exchanged between stations is represented and interpreted, but it does not define the entire transmission path. In a typical radio network, it uses AX.25 frames, a modem, and a transceiver. On the Internet, APRS information is carried in text form through APRS-IS using TCP/IP.

The diagrams below show a **practical division of functions**, not a formal mapping to the OSI model. Individual functions may be handled by separate devices or integrated into a single transceiver or computer application.

## Radio transmission

![Functional division of APRS transmission over radio](./_img/diagram01.png)

In conventional VHF transmission, information prepared by the application is placed in an AX.25 frame. The modem converts digital data into a signal suitable for the radio path, and the transceiver transmits it on the selected frequency.

### User application

The application creates information to be transmitted or interprets data received from other stations. It can handle position reports, messages, objects, telemetry, and weather information. It may be a standalone program or a function built into a transceiver or tracker.

### APRS data

APRS defines information formats and the rules for interpreting them. Among other things, it specifies how to encode a position report, a message, or telemetry data, and how to identify the type of information.

In a typical APRS frame, the main data resides in the *Information* field of the AX.25 frame. This does not mean that other fields have no significance for APRS. The protocol also uses specific elements of AX.25 addressing, and the Mic-E format encodes some information in the destination address field.

APRS and AX.25 therefore perform different but cooperating functions: AX.25 defines the structure of the radio frame, while APRS defines how the information it carries is represented and interpreted, including the use of selected fields of that frame.

### AX.25

AX.25 is a data link layer protocol used in packet radio. It defines a frame containing, among other things, source and destination addresses, an optional list of digipeater addresses, a control field, a protocol identifier (PID), an *Information* field, and a frame check sequence (FCS).

Typical APRS traffic uses **UI** (*Unnumbered Information*) frames, which do not require an AX.25 connection to be established beforehand. This means a single transmission can be received by multiple stations within range. Transmitting a UI frame does not, however, guarantee an acknowledgment of its reception. Any APRS message acknowledgments are a separate mechanism.

### Modem and modulation

The modem converts digital data into a signal suitable for the transmit/receive path and performs the reverse operation on reception. Conventional VHF APRS commonly uses **1200 AFSK**, based on Bell 202, with a data rate of 1200 bit/s and audio tones of 1200 and 2200 Hz.

The modem may be a standalone device, part of a TNC, circuitry built into a transceiver, or software using a sound card. AFSK is one way of transmitting frames, not an APRS data format.

### Radio and RF channel

The transceiver sends and receives the radio signal. The RF channel is a shared medium used by stations operating on a given frequency. Transmission performance depends, among other things, on antennas, transmit power, propagation, interference, and channel load.

European VHF APRS networks commonly use **144.800 MHz**, but neither this frequency nor 1200 AFSK defines APRS itself. APRS information can also be transmitted using other methods and on other bands.

## How the layers work together: packet example

In logs and applications, an APRS packet is often displayed in a readable text form:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

In this representation:

| Element | Meaning |
| --- | --- |
| `SQ9MDD-7` | AX.25 source address. |
| `APRS` | AX.25 destination address, used here according to APRS conventions rather than as the address of a specific recipient. |
| `WIDE1-1` | An element of the digipeater path carried in the AX.25 address fields. |
| `:` | Separator between the header and the information field in the text representation. |
| `!5012.34N/01956.78E>` | Contents of the *Information* field: an uncompressed APRS position report. The `!` is the data type identifier, and the final `>` indicates the station symbol. |

This example shows why the entire visible representation should not be equated with the APRS data field alone. The header uses AX.25 fields to which APRS may assign additional meaning, while the *Information* field contains data encoded in APRS format.

**The text representation is not a literal copy of the frame transmitted over radio.** The actual AX.25 frame also contains binary-encoded fields not shown above, including the control field, PID, and FCS. The `:` separator belongs to the text representation, not to the structure of the radio frame.

The frame structure is covered in more detail in [“APRS packet anatomy”](../03-packet-anatomy/).

## Transmission over the Internet

![Functional division of APRS transmission over the Internet](./_img/diagram02.png)

On the Internet side, the application still creates or reads APRS information, but transmitting it does not require a radio modem or an AX.25 frame in the form sent over RF. The client communicates with **APRS-IS** servers over a **TCP/IP** connection.

APRS-IS uses a text representation of packets that includes the header and information field. APRS-IS servers receive packets and distribute them to the appropriate connected clients according to the network's operating rules, including any applied filters.

**APRS-IS is not an Internet tunnel carrying raw AX.25 frames.** Instead, it enables APRS information to be distributed using a different transmission mechanism. APRS-IS servers are infrastructure components, not a separate OSI model layer.

## IGate: connecting the two environments

An IGate connects the radio network to APRS-IS. After receiving a packet over RF, it may forward it to the Internet network in the appropriate text representation. Information specific to APRS-IS, such as a *q-construct*, may be added during forwarding. This does not mean that such information was present in the original frame transmitted by the radio station.

Traffic in the opposite direction follows separate rules. An IGate should not treat an arbitrary packet received from APRS-IS as a frame ready for direct RF transmission. The detailed forwarding rules, including the use of the *third-party traffic* format, belong to the description of IGate operation.

This distinction makes it easier to understand why a packet seen in APRS-IS may contain additional elements that were not present in its radio transmission.

## Summary

| Element | Main function |
| --- | --- |
| Application | Creating, receiving, and presenting information. |
| APRS | Information format and interpretation, including the use of selected address fields. |
| AX.25 | Radio frame structure, addressing, path, and error checking. |
| Modem | Converting digital data into the signal used by a given transmission method and vice versa. |
| Radio and RF channel | Physically carrying the signal between stations. |
| APRS-IS | Internet exchange and distribution of packets in text representation. |
| TCP/IP | Transport between APRS-IS clients and servers. |
| IGate | Controlled forwarding of packets between the radio network and APRS-IS. |

The key distinction is between the **meaning of information** and **how it is carried**. APRS defines what the data means while using certain AX.25 mechanisms. Over radio, information is carried in AX.25 frames; in APRS-IS, it is distributed in text representation over TCP/IP.

## Sources

- [*APRS Protocol Reference*, version 1.0.1](https://www.aprs.org/doc/APRS101.PDF), chapters 3–5: use of AX.25 and APRS data formats.
- [*AX.25 Link Access Protocol for Amateur Packet Radio*, version 2.2](https://tarpn.net/t/faq/files/AX25.2.2-Sep%2017-1-10Sep17.pdf): frame structure and UI frames.
- [*Connecting to APRS-IS*](https://www.aprs-is.net/connecting.aspx) and [*Server Design*](https://www.aprs-is.net/ServerDesign.aspx): client connections, text representation, and packet distribution.
