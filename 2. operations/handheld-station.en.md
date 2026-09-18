---
title: Handheld APRS station
description: APRS in a handheld radio, field use, available hardware and the difference between full APRS support and a simple tracker.
template: doc
tableOfContents: true
---

APRS in a handheld radio is one of the applications where the capabilities of the system become especially useful.

Instead of carrying a separate tracker, computer and radio, we can use one small device for normal voice communication and APRS data exchange at the same time.

Depending on the capabilities of the radio, we may be able to:

- transmit our own position,
- receive positions from other stations,
- see distance and bearing to another operator,
- send and receive APRS messages,
- receive objects and local information,
- advertise the frequency where we are available,
- observe nearby APRS activity.

This means APRS in a handheld radio is more than an addition to a map. It can provide an extra information channel that works independently of the mobile network.

## Where is handheld APRS most useful?

The advantages of APRS become particularly clear during field operation.

### Mountains and hiking

While hiking, we can periodically transmit our position so other amateur radio operators using APRS can see where we are.

If another APRS station is nearby, the radio may show its position, distance and bearing.

This can be very useful during:

- group hikes,
- SOTA activations,
- field meetings,
- contests,
- amateur radio expeditions,
- finding other operators nearby.

APRS does not replace navigation equipment or emergency systems, but it can be a very useful additional source of information.

### On the water

The same idea applies to sailing, kayaking and other activities on the water.

A station position can be transmitted over radio without relying on mobile coverage.

Operators in the same group can see each other's positions while using the same handheld radio for normal voice communication.

### General field operation

APRS is also convenient during many other amateur radio activities away from home.

For example, after arriving at a test site or meeting point we can transmit our position together with information such as:

```text
QRV 145.500 MHz
```

Another amateur radio operator who sees our APRS station then knows not only **where we are**, but also **where to call us**.

This is one of the important differences between APRS and a simple position tracking system.

APRS is intended to help operators find each other and establish communication.

## APRS without Internet access

Internet access is not required for direct APRS information exchange between stations.

```text
Radio A
   |
   |  RF
   |
Radio B
```

If both stations are within radio range, they can exchange data directly.

If a digipeater is available between them:

```text
Radio A
   |
   v
Digipeater
   |
   v
Radio B
```

the packet can be retransmitted and reach a much wider area.

Internet connectivity, APRS-IS and iGates extend the capabilities of the system, but they are not required for local APRS operation.

This is one of the reasons APRS is so useful in the field.

## What can a handheld APRS radio do?

There is an important difference between individual devices.

The statement:

> **this radio supports APRS**

does not tell us exactly which APRS features it provides.

Capabilities can vary greatly.

### Full APRS terminal

More advanced handheld radios can independently provide a large part of APRS functionality.

Depending on the model, they may offer:

- built-in GPS,
- automatic position beacons,
- SmartBeaconing,
- reception and decoding of packets from other stations,
- a received station list,
- distance and bearing display,
- APRS messaging,
- status information,
- APRS objects,
- frequency information,
- APRS path configuration,
- access to a built-in TNC.

In this case, the radio is a real standalone APRS terminal.

A phone or computer is not required to see other stations or reply to a message.

## The classic choices: Kenwood and Yaesu

For many years handheld APRS has been associated mainly with radios from **Kenwood** and **Yaesu**.

Kenwood has produced several generations of radios with extensive APRS integration. A modern example is the TH-D75 series.

Such radios can do more than transmit their own position. They can also receive data from other stations, display their location, handle messages and provide other APRS functions.

Yaesu has also offered handheld radios with extensive APRS support for many years, including models from the FT series.

With this type of equipment, APRS can work as a fully self-contained function of the handheld radio.

## More and more radios include APRS

The handheld radio market has changed considerably.

APRS is no longer a feature found only in a few expensive radios from Japanese manufacturers.

An increasing number of handheld radios, including equipment from Chinese manufacturers, offer GPS and functions described by their manufacturers as APRS.

This is a positive development.

A wider choice of equipment means:

- a lower entry cost,
- more hardware options,
- more APRS users,
- greater interest in data communication from handheld radios.

There is, however, one very important detail.

## "Supports APRS" does not always mean the same thing

When buying a radio, it is not enough to rely on a specification that says:

> **APRS supported**

In one radio this may mean a full APRS terminal.

In another it may only mean the ability to transmit the radio's own position at regular intervals.

That is a major difference.

### APRS tracker

The simplest implementation looks like this:

```text
GPS
 |
Radio
 |
APRS position
 |
RF
```

The radio obtains its position from GPS and periodically sends an APRS beacon.

Other stations and iGates can receive it.

For the operator of the radio, however, APRS functionality may end there.

The radio may not be able to:

- decode APRS packets from other stations,
- display a station list,
- show their positions,
- receive messages,
- send messages,
- handle APRS objects,
- support more advanced protocol functions.

Such a device is effectively an **APRS tracker built into a handheld radio**.

There is nothing wrong with that if this is exactly what we need.

The problem begins when we buy it expecting a full APRS terminal.

## Analog APRS and digital-system position reporting

It is also important to check **how the radio implements APRS**.

Some radios can transmit classic AFSK APRS over an analog radio channel.

Others provide position reporting as part of a digital voice system or require additional infrastructure.

From the user's point of view, the result may look similar: the position appears on a map.

Technically, however, these can be completely different systems.

If we want to use the traditional local APRS network on 2 m, we should make sure the radio actually supports APRS over an analog radio channel.

## What should you check before buying?

If APRS is one of the reasons for buying a radio, it is worth answering a few questions first.

### Does the radio transmit classic APRS over RF?

Not only through the Internet, an application or a digital radio network.

### Does it receive APRS?

This is one of the most important differences between an APRS terminal and a tracker.

### Does it display received stations?

Ideally, we should be able to see:

- callsign,
- distance,
- bearing,
- symbol,
- comment or status.

### Does it support APRS messaging?

Check separately whether the radio supports:

- receiving messages,
- sending messages,
- ACK acknowledgements.

### Can the APRS path be configured?

For example:

```text
WIDE1-1,WIDE2-1
```

Not every device gives the user full control over APRS parameters.

### Does it support SmartBeaconing?

For pedestrian or mobile operation, intelligent beacon timing can significantly reduce unnecessary radio traffic.

### Does APRS work without a phone?

If standalone field operation is important, this is an especially useful question.

Some radios require a smartphone application for part of their APRS functionality.

### Does the manufacturer actually document the APRS implementation?

It is worth reading the radio manual before buying it.

The presence of the word `APRS` in an online shop specification is not enough information.

## A phone as an extension of the radio

A smartphone and APRS do not have to be mutually exclusive.

Some radios can work with a phone over Bluetooth.

The phone can then provide a more convenient:

- interface,
- map display,
- way to enter messages,
- view of received stations.

The radio still performs the actual radio transmission.

This can be a useful compromise between a traditional standalone APRS handheld and a more complex field setup.

## Handheld APRS is more than a dot on a map

The simplest use of APRS in a handheld radio is:

```text
GPS -> beacon -> map
```

but the system can do much more.

A full APRS terminal may allow an operator standing somewhere in the mountains to see:

```text
SQ9ABC-7
3.2 km
bearing: NE
QRV 145.500 MHz
```

and then call that station by voice.

It may receive a short message from another operator.

It may show an object marking a meeting point.

It may reveal that another amateur radio operator is only a few kilometres away.

At that point APRS stops being just a position tracking system.

It becomes what it was designed to be: **a local real-time information exchange system for amateur radio operators**.

## Where should you start?

If you already have a radio with full APRS support:

1. configure your callsign and SSID,
2. enable GPS,
3. set the APRS frequency used in your region,
4. configure an appropriate path,
5. configure a reasonable beacon interval or SmartBeaconing,
6. start by observing received stations,
7. test APRS messaging,
8. then adjust the configuration to the way you actually use the radio.

In Poland and much of Europe, classic APRS on the 2 m band uses:

```text
144.800 MHz FM
```

If you are still choosing equipment, first decide what you expect from APRS.

If you only want other stations to see your position, a simple tracker may be completely sufficient.

If you want to **use APRS as a communication and information system**, look for a device that not only transmits its own position, but also receives and displays data from other stations and supports APRS messaging.
