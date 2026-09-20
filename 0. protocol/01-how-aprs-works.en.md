---
title: How does APRS work?
description: How APRS packets travel through radio, digipeaters, IGates, and APRS-IS.
template: doc
tableOfContents: true
---

APRS is a broadcast network. A station transmits a packet over the air, and every receiver within range can receive and use it independently. There is no central node or mandatory route: a packet may remain local, be retransmitted by a digipeater, reach the Internet through an IGate—or do several of those things at once.

## Station to station: APRS works over radio

The simplest case needs neither the Internet nor infrastructure. A mobile station transmits a packet and a home station receives it directly over RF.

![Direct reception of an APRS packet over radio](./_img/diagram1.png)

The received packet may contain, for example, a position, status, message, weather data, or telemetry. If the receiver understands its format, the information is immediately useful. That is a complete, correctly functioning APRS exchange.

Over radio, APRS data is usually carried in an **AX.25 UI** (*Unnumbered Information*) frame. Its text representation may look like this:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

The source callsign, destination address, path, and information field describe the frame and its APRS content. Later articles cover packet structure; here, the important question is what happens after transmission.

## One transmission, many receivers

RF is a shared medium. The same transmission can be received simultaneously by a user station, a digipeater, and an IGate.

![One APRS transmission received by a station, digipeater, and IGate](./_img/diagram2.png)

Reception does not create a queue or a forwarding chain. Each receiver makes its own decision: it displays the data, retransmits the packet over radio, or forwards it to APRS-IS. This is why multiple paths for the same information are normal in APRS.

## Digipeater: extending RF coverage

A **digipeater** receives a radio packet and—when its path and configuration allow it—transmits it again. This lets the information reach beyond the direct range of the source station.

![Retransmission of an APRS packet by a digipeater](./_img/diagram3.png)

A digipeater should not repeat everything. Its decision depends, among other things, on the addresses in the path, the network's local operating policy, and duplicate protection. Paths such as `WIDE1-1` and `WIDE2-n` are commonly used; their semantics and configuration rules are covered separately.

The role of a digipeater can be summarized as:

```text
RF → RF
```

It does not automatically imply Internet access.

## IGate and APRS-IS: the RF–Internet boundary

An **IGate** (*Internet Gateway*) hears local RF traffic and forwards selected packets to **APRS-IS**, the global network of servers that distributes APRS data. This makes locally received packets available to applications, maps, and monitoring services.

![Data forwarded from the RF network through an IGate to APRS-IS](./_img/diagram4.png)

The primary direction of an IGate is:

```text
RF → APRS-IS
```

An IGate can operate without digipeating, and a digipeater can operate without an IGate. A single station can of course perform both roles, but they are independent functions:

| Element | Role | Primary direction |
| --- | --- | --- |
| Digipeater | Extends local radio-network coverage | `RF → RF` |
| IGate | Connects local RF to APRS-IS | `RF → APRS-IS` |
| APRS-IS | Distributes packets over the Internet | Internet |

APRS-IS extends the reach of information, but it does not replace the radio channel. A packet that does not appear on an Internet service may still have been received and used correctly locally.

## Why the same frame appears more than once

In a real network, multiple IGates may hear the original transmission and its retransmission. Each can forward the frame to APRS-IS.

![Multiple reception paths for the same APRS transmission](./_img/diagram5.png)

This is not a transmission error; it follows from the broadcast nature of RF. Digipeaters, IGates, and APRS-IS servers use duplicate recognition to avoid propagating the same frame further. Details depend on the implementation and configuration of each node.

## From the Internet back to radio

The `APRS-IS → RF` direction is deliberately limited. The radio channel has low capacity and is shared by every station, so an IGate cannot treat it as a complete copy of APRS-IS.

A typical controlled case is a message addressed to a local station that the IGate has heard on RF recently. When selected traffic is sent from APRS-IS to radio, the **third-party traffic** mechanism can be used to preserve information about the packet's origin. Gating rules, q-constructs, and the third-party traffic format deserve separate treatment.

## The complete picture

The diagram below shows these roles working together. RF communication spreads locally; IGates move data between local radio and APRS-IS; applications and services use the data available on the Internet.

![Data flow between RF stations, digipeaters, IGates, and APRS-IS](./_img/diagram6.png)

For one packet, three outcomes may therefore occur in parallel:

- local reception by other stations,
- extended coverage through digipeating,
- publication to APRS-IS through one or more IGates.

None is required for the others to occur. Local APRS can work without the Internet, and an IGate can forward a packet to APRS-IS without a digipeater.

## Key points

- APRS is not a single route: `station → digipeater → IGate → Internet`.
- One RF transmission can be useful to many receivers and reach them by different paths.
- A digipeater retransmits radio traffic; an IGate connects RF with APRS-IS.
- Duplicates are natural in a broadcast network and are filtered by its components.
- The Internet improves the availability of data, but is not a condition for local APRS to work.
- Traffic from the Internet to RF must be selective so it does not burden the shared channel.

## Next

The next step is to learn the structure of AX.25 frames and APRS packets, source addresses and SSIDs, the destination/TOCALL field, digipeater paths, and the differences between RF and APRS-IS traffic.
