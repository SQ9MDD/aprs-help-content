---
title: APRX
description: Lightweight APRS daemon for POSIX systems combining iGate, digipeater, multiple radio interfaces, telemetry and APRS-IS routing.
template: doc
tableOfContents: true
---

APRX is specialised APRS infrastructure software designed primarily to operate as an **iGate, digipeater, or a combination of both**.

It runs as a daemon on POSIX-compatible systems, especially Linux, BSD and other Unix-like platforms.

Official repository:

https://github.com/PhirePhly/aprx/

Project page:

https://thelifeofkenneth.com/aprx/

APRX is written in C and was designed from the beginning as lightweight software intended for continuous infrastructure operation.

It is not an audio modem.

To communicate with a radio it uses an external TNC, KISS modem, an AX.25 interface provided by the operating system, or another supported source of decoded frames.

A typical installation can look like this:

```text
Radio
  |
TNC / KISS modem
  |
APRX
  |
APRS-IS
```

or as a digipeater:

```text
Radio
  |
TNC / modem
  |
APRX
  |
Radio
```

In more advanced installations APRX can handle multiple receivers, transmitters and channels at the same time.

## Authors and project history

The author of the first and second generations of APRX was **Matti Aarnio OH2MQK**.

The code developed by Matti dates from the period:

```text
2007-2014
```

In the APRX source headers the project is described as:

```text
2nd generation APRS iGate and digi
```

From 2014, project maintenance and further development were taken over by **Kenneth W. Finnegan W6KWF**.

The current project repository:

https://github.com/PhirePhly/aprx/

contains code from both stages of development.

APRX is a mature project. The pace of development today is much slower than in newer projects, but the software is still present in Linux distributions and remains in use in operational APRS installations.

APRX 2.9.1 is available, among other places, in Debian.

## APRX philosophy

One of the fundamental design goals of APRX was to create infrastructure software with low system requirements.

The source code describes the project as a solution intended to have as few dependencies as possible beyond the standard UNIX system library environment.

This makes APRX well suited for:

- small computers,
- routers,
- embedded Linux devices,
- Raspberry Pi,
- older PCs,
- servers running continuously.

The program does not require a graphical environment.

A typical installation simply looks like:

```text
system
  |
aprx daemon
  |
/etc/aprx.conf
```

## APRX is not a modem

This distinction is important.

APRX does not decode AFSK from a sound card.

It needs a source of already decoded AX.25 frames.

For example:

```text
Radio
  |
VP-Digi
  |
KISS
  |
APRX
```

or:

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
```

or a classic hardware TNC.

APRX is responsible for:

- frame routing,
- digipeating,
- iGate operation,
- APRS-IS,
- filtering,
- beacons,
- telemetry,
- multi-interface logic.

The modem is responsible for the radio layer.

## RX iGate

One of the most common APRX use cases is a **receive-only iGate**.

In such a setup:

```text
RF
 |
Radio
 |
TNC
 |
APRX
 |
APRS-IS
```

frames received over radio are forwarded to APRS-IS.

A minimal configuration mainly requires:

- your own callsign,
- APRS-IS passcode,
- APRS-IS server,
- radio interface.

Example APRS-IS block:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

APRX can therefore provide a simple iGate without enabling RF transmission.

## TX iGate

APRX also supports transmission from APRS-IS to RF.

This makes it possible to build a full bidirectional iGate.

Diagram:

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

A TX iGate should not retransmit all APRS-IS traffic onto the radio channel.

APRX provides mechanisms to limit and filter such traffic.

The `APRSIS` source can be defined in the digipeater section as a separate source with:

```text
relay-type third-party
```

It can also use:

- filtering,
- rate limiting,
- viscous delay.

This provides much more precise control over traffic sent from the Internet to RF.

## Digipeater

APRX includes an advanced built-in digipeater engine.

It supports, among other things, the classic:

```text
WIDEn-N
```

mechanism and ordinary AX.25 aliases.

One digipeater section has one transmitter, but it can accept packets from multiple sources.

Example architecture:

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

This makes it possible to build systems with:

- several receivers,
- receiver diversity,
- separate directional antennas,
- different channels,
- a shared transmitter.

This multi-source architecture was one of the features that distinguished APRX from simple digipeater controllers.

## New-N

APRX supports the modern APRS New-N mechanism.

It can handle paths such as:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

The configuration can define the maximum number of requested and completed hops.

Custom alias keys can also be defined.

This allows APRX to operate both as a local fill-in digipeater and as part of a larger infrastructure network.

## Viscous digipeating

One of the more characteristic APRX features is **viscous digipeating**.

The idea is that the digipeater does not need to retransmit a packet immediately.

It first waits for a short period.

If during that time it hears that another station has already repeated the packet, it can cancel its own transmission.

Diagram:

```text
packet received
     |
     v
short wait
     |
     +---- packet heard again ---> DROP
     |
     +---- no repeat -----------> TX
```

This helps reduce duplicate traffic in areas where coverage from several digipeaters overlaps.

APRX supports viscous delay both within the same interface and between different interfaces.

## Multiple receivers

APRX was also designed with larger nodes in mind.

It can receive frames simultaneously from multiple interfaces.

Example:

```text
Radio RX North ---> TNC ---\
                            \
Radio RX South ---> TNC ----> APRX ---> Radio TX
                            /
Radio RX Local ---> TNC ---/
```

This allows nodes with several receivers and a single transmitter.

Interfaces can also be grouped using:

```text
igate-group
```

which helps correctly handle iGate logic in installations with multiple receivers and transmitters operating on the same channel.

## Multiple channels

APRX is not limited to one radio channel.

Multiple interfaces and multiple digipeater sections can be configured.

A single APRX instance can therefore operate a more complex node.

For example:

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
other RF    ---> local channel
```

The exact configuration depends on the modems and interfaces used.

## KISS interfaces

APRX can work with classic serial TNCs.

Standard:

```text
KISS
```

is supported, along with several related extensions.

The configuration includes, among others:

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

This gives APRX compatibility with different generations and types of hardware TNCs and modems.

## TNC2 monitor mode

APRX can also receive data in:

```text
TNC2
```

monitor format, the textual format known from classic TNCs.

This makes it possible to integrate sources that do not provide KISS but can output received frames in monitor format.

## Linux AX.25

On Linux systems APRX can also use AX.25 interfaces provided directly by the operating-system kernel.

Configuration can use:

```text
ax25-device
```

APRX can then receive frames from the kernel AX.25 stack.

This is not required.

The project was designed so that it can also operate without AX.25 support in the kernel.

## DPRS

APRX also includes **D-PRS** support.

It can receive DPRS data and convert it into APRS.

The project includes gateway functionality:

```text
D-PRS -> APRS
```

This was developed mainly to integrate position data originating from digital radio systems.

## APRS-IS

APRX includes its own APRS-IS client.

It can connect to Tier2 servers and handle multiple server definitions.

For example:

```text
server rotate.aprs2.net
```

APRS-IS filters can also be used.

Example:

```text
filter "m/100"
```

meaning traffic within 100 km of the station position.

Other filters compatible with the javAPRSSrvr/APRS-IS filtering mechanism can also be used.

## Traffic filtering

APRX provides extensive traffic-filtering mechanisms.

Traffic can be filtered by, among other things:

- source,
- destination,
- path,
- payload,
- geographic area.

Regular-expression filters and geographic filters are available.

For example, a selected callsign can be blocked or retransmission can be limited to a chosen area.

Example:

```text
filter -b/CALL
```

Filtering can be applied separately to different digipeater sources.

## Rate limiting

APRX includes mechanisms for limiting the number of retransmitted frames.

A global digipeater limit can be configured with:

```text
ratelimit
```

and a limit for individual source callsigns with:

```text
srcratelimit
```

This helps protect the channel from a situation in which one station generates an excessive amount of traffic.

## Beacons

APRX has its own beacon generator.

It can generate, among other things:

- positions,
- items,
- objects,
- raw APRS frames,
- data read from a file.

Example:

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

Beacons can be sent through a selected interface and with a selected path.

APRX intentionally spreads beacon transmission times instead of sending all beacons at exactly the same instant.

Intervals are also slightly randomised to statistically reduce the probability of collisions between stations.

## Telemetry

APRX has its own infrastructure telemetry system.

The program collects traffic data for individual interfaces.

Among other values it monitors:

```text
RX
DROP
TX
```

as well as packet and byte counters.

The data can be aggregated over time and published as APRS telemetry.

APRX can send this telemetry to APRS-IS and optionally to RF.

## Erlang monitor

An interesting APRX component is the built-in **erlang monitor**.

The name comes from the unit used to describe channel occupancy.

APRX measures actual traffic on its interfaces and collects statistics in intervals such as:

```text
1 minute
10 minutes
20 minutes
```

This allows the operator to observe the load on the radio channel.

The statistics can be written to a state file so that short program restarts do not necessarily erase the current measurements.

## Logging

APRX can maintain separate logs for, among other things:

- received RF frames,
- APRS-IS connections,
- DPRS,
- erlang statistics.

Example files:

```text
aprx-rf.log
aprx.log
erlang.log
```

Logs can be rotated normally by the operating system.

## Configuration

The main configuration file is usually:

```text
/etc/aprx.conf
```

Its syntax resembles Apache-style configuration.

Example:

```text
mycall SQ9ABC-1

<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>

<interface>
    serial-device /dev/ttyUSB0 9600 8n1 KISS
</interface>
```

Configuration sections include, among others:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

Multiple interfaces, beacons and telemetry sections can be defined.

## APRX with Dire Wolf

A very practical combination is to use APRX for infrastructure logic and an external modem for the radio layer.

Diagram:

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
  |
APRS-IS
```

Dire Wolf then handles AFSK modulation and demodulation.

APRX handles:

- digipeating,
- iGate,
- filters,
- multiple sources,
- telemetry,
- routing.

This separation of responsibilities fits the APRX architecture, which does not require its own audio modem.

## APRX with a hardware TNC

A hardware KISS device can be used in exactly the same way:

```text
Radio
  |
VP-Digi / TNC-X / other TNC
  |
KISS
  |
APRX
```

Thanks to standard KISS, APRX remains independent of a particular modem model.

## Low system requirements

APRX is written in C and has no graphical user interface.

Its hardware requirements are therefore small.

It works well on:

- Raspberry Pi,
- small x86 computers,
- older hardware,
- embedded Linux devices,
- virtual machines.

In a typical infrastructure installation it can run for months without operator interaction.

## Stability and maturity

APRX has a long history.

Its core architecture was created many years ago and has been proven in a large number of real APRS installations.

The 2.9.1 line remains available in Debian packages.

The repository still has issues and pull requests, but today the project should primarily be regarded as **mature infrastructure software**, not as a rapidly changing application.

## What APRX does not do

APRX is not:

- an audio modem,
- a graphical APRS client,
- an APRS map,
- a station-tracking application,
- an operator-facing daily messaging client.

Its place is mainly in infrastructure.

The simplest view is:

```text
radio
  |
modem / TNC
  |
APRX
  |
APRS infrastructure logic
  |
APRS-IS / RF
```

## Who is APRX for?

APRX is a good fit when you need:

- RX iGate,
- TX iGate,
- digipeater,
- combined digi + iGate,
- a node with multiple receivers,
- an installation with several radio interfaces,
- routing between ports,
- traffic filtering,
- infrastructure telemetry,
- a very lightweight daemon operating 24/7.

It is especially interesting where the operator wants full control over APRS infrastructure logic while treating the radio modem as a separate layer.

## License

APRX is open-source software.

The project is distributed under:

```text
BSD 3-Clause
```

This allows the code to be analysed, modified and used under the terms of that licence.

## Summary

APRX is one of the classic infrastructure programs of the APRS world.

Its main strength is not a single feature, but the ability to build an entire node around one process:

```text
multiple receivers
      |
      v
    APRX
   /    \
 digi   iGate
   \    /
    RF / APRS-IS
```

The program combines:

- RX iGate,
- TX iGate,
- New-N digipeating,
- viscous digipeating,
- multiple interfaces,
- KISS and Linux AX.25,
- filtering,
- rate limiting,
- beacons,
- telemetry,
- channel-load monitoring,
- DPRS,
- APRS-IS.

At the same time it remains a small daemon written in C that does not require a graphical environment or a large operating system.

This is why APRX has remained a popular choice for continuously operating APRS infrastructure stations for many years.

## Documentation

Main sources:

- repository: https://github.com/PhirePhly/aprx/
- project page: https://thelifeofkenneth.com/aprx/
- example configuration: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- complex configuration: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
