---
title: APRS Home Station
description: How to build a simple APRS home station, from receiving packets to running an iGate or digipeater.
template: doc
tableOfContents: true
---

A home APRS station is one of the easiest ways to start using APRS in practice.

It can simply receive local traffic, forward received packets to APRS-IS as an iGate, and, if the local network really needs it, also operate as a digipeater.

## What do you need?

A basic home station consists of four elements:

1. **A 2 m radio**
2. **An antenna**
3. **A modem or TNC**
4. **A computer running APRS software**

The APRS frequency depends on the region. In much of Europe, including Poland, APRS on 2 m uses **144.800 MHz FM**.

A typical setup may look like this:

```text
Antenna
  |
Radio 144.800 MHz
  |
TNC / modem
  |
Computer
  |
Internet
```

The computer does not have to be a traditional PC. A Raspberry Pi, a small x86 computer or another low-power system that can run continuously is usually sufficient.

## Radio

You do not need a radio with built-in APRS support to receive APRS.

A normal FM radio capable of operating on the local APRS frequency is enough.

Ideally, the radio should provide access to:

- receiver audio,
- transmitter audio,
- PTT control.

Many radios provide these signals through a DATA, PACKET or accessory connector.

It is also possible to use the speaker output and microphone input, although this usually requires more care when adjusting audio levels.

## Modem or TNC

APRS on 2 m commonly uses 1200 baud AFSK.

A modem is required to decode these packets.

This may be a traditional hardware TNC, but software modems are very common today.

One popular solution is **Dire Wolf**.

Dire Wolf uses a computer sound card as an AFSK modem and can provide a KISS TCP interface to other APRS applications.

```text
Radio
  |
Sound card
  |
Dire Wolf
  |
KISS TCP
  |
APRS software
```

## Antenna

For a home station, the antenna is often more important than transmitter power.

A simple 2 m antenna is enough for receiving local traffic.

If the station is intended to operate as an iGate or digipeater, it is usually worth installing the antenna as high as practical with a good view of the surrounding area.

However, maximum possible range is not always an advantage.

The goal of APRS is to build a useful radio network, not to receive the largest possible number of stations from very long distances.

## Computer

APRS software can run on:

- Raspberry Pi,
- a PC,
- a small x86 computer,
- a home server,
- another Linux or Windows computer.

For a station operating continuously, a small Linux computer is often a good solution.

Example configuration:

```text
Raspberry Pi
   |
Dire Wolf
   |
KISS TCP
   |
APRSBox
```

## First step: receive only

The best place to start is a receive-only setup.

Set the radio to the APRS frequency used in your region.

In Poland and much of Europe:

```text
144.800 MHz
FM
no CTCSS
no DCS
```

Connect the radio audio to the computer and start the modem.

Once the audio level is adjusted correctly, APRS packets should begin to appear, for example:

```text
SQ9ABC-9>APRS,WIDE1-1,WIDE2-1:...
```

At this stage you do not need to transmit anything.

You can simply observe the network and check:

- which stations can be heard,
- which digipeaters are active nearby,
- how busy the local channel is,
- whether the audio level is correct.

## Adding an iGate

The next step can be an iGate.

An iGate receives packets from RF and forwards them to APRS-IS.

```text
RF 144.800 MHz
      |
    Radio
      |
    Modem
      |
  APRSBox
      |
   APRS-IS
```

Stations received by your installation can then become visible in the global APRS network.

Typical configuration requires:

- your callsign,
- an APRS-IS connection,
- a valid APRS-IS passcode,
- a configured KISS port or TNC connection.

## What about transmitting?

There is no need to enable transmitting immediately.

A sensible order is:

```text
Receive
  |
Observe the local network
  |
RX iGate
  |
Own beacon
  |
Optional TX iGate
  |
Optional digipeater
```

This lets you understand the local APRS network first and only then decide which additional functions are actually useful.

## Your own beacon

If the station should be visible on APRS, it can periodically transmit its own position.

A fixed home station does not need to beacon very often because its position does not change.

The beacon can also contain additional information, for example:

```text
PHG...
QRV 145.500 MHz
```

It may also include information about a local repeater, website or other useful service.

## iGate to RF

Some stations can also forward selected packets from APRS-IS to RF.

This is most commonly used for messages addressed to local stations.

This function requires more care.

APRS-IS should not be treated as a source of traffic that can be retransmitted to RF without limits.

The APRS radio channel has limited capacity, so traffic sent from the Internet to RF should be filtered and limited to packets that are actually useful.

## Digipeater

A home station can also retransmit packets from other stations.

However, this does not mean that every iGate should also become a digipeater.

Before enabling digipeating, check:

- whether digipeaters already exist nearby,
- what areas they already cover,
- whether real coverage gaps exist,
- how busy the channel is.

An additional digipeater in an area that already has good coverage can make the network worse rather than better.

## Example complete station

One possible setup looks like this:

```text
2 m antenna
    |
Radio
    |
Audio interface + PTT
    |
Dire Wolf
    |
KISS TCP
    |
APRSBox
    |
APRS-IS
```

APRSBox can then provide functions such as:

- packet reception,
- local traffic display,
- iGate,
- station beacons,
- APRS messaging,
- digipeating,
- network statistics.

This is not the only possible configuration.

Instead of Dire Wolf, you can use a hardware TNC, VP-Digi or another device providing a KISS interface.

## Where should you start?

A simple path is:

```text
1. Antenna
2. Radio on the local APRS frequency
3. Dire Wolf
4. Receive packets
5. APRSBox or other APRS software
6. RX iGate
```

Only after everything works correctly is it worth enabling transmission.

This approach lets you understand local APRS traffic before adding more packets to the channel.

A home APRS station does not need to become a fully featured network node on the first day. It can start as a simple receiver and gradually grow together with the operator's experience and the needs of the local network.
