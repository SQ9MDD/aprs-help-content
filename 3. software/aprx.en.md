---
title: APRX
description: Lightweight APRS daemon for POSIX systems combining iGate, digipeater, multiple radio interfaces, telemetry and APRS-IS routing.
template: doc
tableOfContents: true
---

APRX is specialised APRS infrastructure software designed primarily to operate as an **iGate, digipeater, or a combination of both**.

It runs as a daemon on POSIX systems, especially Linux, BSD and other Unix-like systems.

Official repository:

https://github.com/PhirePhly/aprx/

Project page:

https://thelifeofkenneth.com/aprx/

APRX is written in C and was designed from the beginning as lightweight software for continuous infrastructure operation.

It is not an audio modem. It uses an external TNC, KISS modem, operating-system AX.25 interface or another supported source of decoded AX.25 frames.

```text
Radio
  |
TNC / KISS modem
  |
APRX
  |
APRS-IS
```

## Authors and project history

The first and second generations of APRX were written by **Matti Aarnio OH2MQK**.

```text
2007-2014
```

The source headers describe it as:

```text
2nd generation APRS iGate and digi
```

From 2014, maintenance and further development were taken over by **Kenneth W. Finnegan W6KWF**.

Current repository:

https://github.com/PhirePhly/aprx/

APRX is a mature project. Development is slower today, but it remains packaged in Linux distributions and is still used in operational APRS installations.

## APRX philosophy

APRX was designed as infrastructure software with low system requirements and few dependencies.

It is well suited to:

- small computers,
- routers,
- embedded Linux devices,
- Raspberry Pi,
- older PCs,
- 24/7 servers.

No graphical environment is required.

```text
system
  |
aprx daemon
  |
/etc/aprx.conf
```

## APRX is not a modem

APRX does not decode AFSK from a sound card.

It needs already decoded AX.25 frames, for example from:

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

APRX handles:

- frame routing,
- digipeating,
- iGate,
- APRS-IS,
- filtering,
- beacons,
- telemetry,
- multi-interface logic.

## RX iGate

A common use is a **receive-only iGate**:

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

Example APRS-IS block:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

## TX iGate

APRX can also forward selected traffic from APRS-IS to RF.

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

`APRSIS` can be used as a digipeater source with:

```text
relay-type third-party
```

and combined with filtering, rate limiting and viscous delay.

## Digipeater

APRX includes an advanced built-in digipeater.

It supports:

```text
WIDEn-N
```

and ordinary AX.25 aliases.

One digipeater section can have one transmitter and multiple packet sources.

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

## New-N

APRX supports New-N paths such as:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

Custom alias keys and hop limits can also be configured.

## Viscous digipeating

With **viscous digipeating**, APRX can briefly delay retransmission.

If it hears that another digipeater has already repeated the packet, it can suppress its own transmission.

```text
packet received
     |
     v
short delay
     |
     +---- heard again ---> DROP
     |
     +---- no repeat ----> TX
```

## Multiple receivers

APRX can receive frames simultaneously from several interfaces.

```text
Radio RX North ---> TNC ---\
                            \
Radio RX South ---> TNC ----> APRX ---> Radio TX
                            /
Radio RX Local ---> TNC ---/
```

Interfaces can also be grouped with:

```text
igate-group
```

## Multiple channels

APRX can handle multiple radio channels and digipeater sections.

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
other RF    ---> local channel
```

## KISS interfaces

APRX supports classic serial TNCs using:

```text
KISS
```

and several extensions:

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

## TNC2 monitor mode

APRX can also receive textual:

```text
TNC2
```

monitor format.

## Linux AX.25

On Linux, APRX can use kernel AX.25 interfaces directly:

```text
ax25-device
```

## DPRS

APRX includes **D-PRS** support:

```text
D-PRS -> APRS
```

## APRS-IS

APRX includes its own APRS-IS client.

Example:

```text
server rotate.aprs2.net
```

APRS-IS filters can also be used:

```text
filter "m/100"
```

## Traffic filtering

Traffic can be filtered by:

- source,
- destination,
- path,
- payload,
- geographic area.

Example:

```text
filter -b/CALL
```

## Rate limiting

Global limit:

```text
ratelimit
```

Per-source limit:

```text
srcratelimit
```

## Beacons

APRX has its own beacon generator for:

- positions,
- items,
- objects,
- raw APRS frames,
- file-based data.

Example:

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

Beacon timing is intentionally spread and slightly randomised to reduce collisions.

## Telemetry

APRX can collect infrastructure telemetry such as:

```text
RX
DROP
TX
```

along with packet and byte counters.

## Erlang monitor

APRX includes an **erlang monitor** for channel occupancy.

Typical statistic intervals:

```text
1 minute
10 minutes
20 minutes
```

## Logging

Separate logs can be maintained for:

- RF frames,
- APRS-IS,
- DPRS,
- erlang statistics.

```text
aprx-rf.log
aprx.log
erlang.log
```

## Configuration

The main configuration file is usually:

```text
/etc/aprx.conf
```

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

Common sections include:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

## APRX with Dire Wolf

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

Dire Wolf handles AFSK modulation and demodulation.

APRX handles infrastructure logic, filtering, routing, iGate and digipeating.

## APRX with a hardware TNC

```text
Radio
  |
VP-Digi / TNC-X / other TNC
  |
KISS
  |
APRX
```

## Low system requirements

APRX is written in C and has no graphical interface.

It is suitable for:

- Raspberry Pi,
- small x86 systems,
- older hardware,
- embedded Linux,
- virtual machines.

## Stability and maturity

APRX has a long history and has been proven in many real APRS installations.

The 2.9.1 line remains available in Debian.

Today APRX should mainly be regarded as **mature infrastructure software**.

## What APRX does not do

APRX is not:

- an audio modem,
- a graphical APRS client,
- an APRS map,
- a station tracking application,
- a daily operator messaging application.

Its main role is infrastructure.

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

APRX is a good fit for:

- RX iGate,
- TX iGate,
- digipeater,
- combined digi + iGate,
- multi-receiver nodes,
- multiple radio interfaces,
- routing between ports,
- traffic filtering,
- infrastructure telemetry,
- lightweight 24/7 operation.

## License

APRX is open-source software under:

```text
BSD 3-Clause
```

## Summary

APRX is one of the classic APRS infrastructure programs.

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

It combines RX and TX iGate, New-N and viscous digipeating, multiple interfaces, KISS, Linux AX.25, filtering, rate limiting, beacons, telemetry, channel monitoring, DPRS and APRS-IS.

## Documentation

Main sources:

- repository: https://github.com/PhirePhly/aprx/
- project page: https://thelifeofkenneth.com/aprx/
- example configuration: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- complex configuration: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
