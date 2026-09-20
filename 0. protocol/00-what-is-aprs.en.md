---
title: What is APRS?
description: An introduction to APRS as a radio system for exchanging short, current information.
template: doc
tableOfContents: true
---

**APRS, Automatic Packet Reporting System**, is an amateur radio digital information exchange system designed for real-time use. It was created so that users operating within an area can automatically exchange short, current information useful for communications and field activities.

APRS is often associated mainly with displaying station locations on a map. That is only one of its functions.

The position of a station, vehicle, or object is one type of information APRS can carry. The system can also distribute status reports, messages, operating frequency information, objects, weather data, telemetry, bulletins, queries, and warnings.

The most important feature of APRS is therefore not position reporting itself, but **automatically making current information available to other users of the network**.

> APRS should primarily be understood as a radio system for exchanging short, current information that can help establish communication, improve situational awareness, or support action.

## More than position

One of the most common misconceptions about APRS is treating it as a GPS tracking system.

A tracker transmitting its position is a typical APRS device, but it uses only part of what the protocol can do.

The same network can carry, for example:

- positions of fixed and mobile stations,
- information about the currently monitored frequency,
- operator status,
- short text messages,
- group messages and bulletins,
- repeaters and other objects located in the field,
- meeting points, events, or field activities,
- telemetry data,
- weather station data,
- weather warnings and other area-related information.

A mobile station can therefore tell other stations not only **where it is**, but also which frequency the operator is monitoring or which repeater is currently being used.

In practice, this information can be much more useful than a dot on a map by itself.

## Information about what is happening now

APRS was designed for information that matters **now**.

Unlike traditional packet radio systems focused on establishing a connection between two stations and transferring larger amounts of data, typical APRS traffic is based on short broadcast transmissions.

A station transmits information that can be received at the same time by all stations within radio range.

This makes APRS well suited to building local situational awareness.

A user can learn, for example:

- who is nearby,
- where other stations are located,
- which stations are mobile,
- which frequencies operators are using,
- which repeaters are nearby,
- whether an amateur radio event or activity is taking place in the area,
- what weather conditions local stations are reporting,
- whether a warning has been issued,
- which objects have been published by other network users.

This does not mean that every APRS receiver must display all of these data types. The amount and form of information presented depend on the capabilities of the device or software being used.

## APRS is a radio system

APRS can operate entirely without Internet access.

The simplest information exchange can look like this:

```text
Station A
   |
   | RF
   v
Station B
```

Station B receives the transmission from Station A directly and can use the information it contains.

If direct radio coverage is insufficient, the network can use **digipeaters** that retransmit selected packets:

```text
Station A
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Station B
```

Internet access is therefore not required for APRS to work.

This is an important property of the system. Information can be exchanged locally over radio even when none of the stations has access to Internet infrastructure.

## APRS-IS extends the radio network

Modern APRS is often connected to the Internet-based **APRS-IS** network.

Stations called **IGates**, or Internet Gateways, provide this connection.

A typical packet path can look like this:

```text
station
  |
  | RF
  v
digipeater
  |
  | RF
  v
IGate
  |
  | Internet
  v
APRS-IS
  |
  v
APRS applications and services
```

An IGate can forward information received over radio to APRS-IS. This makes the data available to applications operating outside the local radio coverage area.

This is how packets received by local infrastructure can later appear in Internet APRS services.

However, the concepts should be kept in the correct order:

**APRS-IS is part of APRS infrastructure, but it is not APRS itself.**

The basic exchange of information can still take place directly over radio.

## APRS is not APRS.fi

Internet services such as APRS.fi are very useful ways of presenting data from the APRS network, but they are not the network itself.

The map visible in such a service is only a visualization of some of the information that has been forwarded to APRS-IS.

A packet can fulfil its purpose on the radio network and never reach the Internet.

For example, a mobile station may transmit its position together with information about the frequency being monitored. An operator a few kilometres away can receive the packet and use it to establish voice contact.

If no IGate is operating in the area, the packet will not appear in an Internet service.

That does not mean APRS failed.

Quite the opposite: the information reached a user for whom it was useful.

Therefore:

**appearing on an Internet map should not be treated as the only measure of whether APRS is working correctly.**

## The local value of information

APRS was designed primarily as a tactical information system.

In this context, the word *tactical* does not imply military use. It means information that is useful to a user in the current situation and local environment.

For an operator travelling by car, the information:

```text
SP9XYZ
145.550 MHz
```

may be more useful than a detailed history of that station's movement over the previous several hours.

Likewise, an object identifying a local repeater, meeting point, checkpoint, or field activity has the greatest value to users who are nearby.

For this reason, APRS should not be treated as a system intended to deliver every possible piece of information to every user.

The goal is to deliver **the right information, to the right users, at the right time**.

## One shared channel

Classic APRS on VHF most commonly operates at **1200 baud**.

It is a radio channel shared by all stations within range.

Every transmission uses some of the available airtime. A packet may also be repeated by one or more digipeaters.

For this reason, APRS requires sensible use of the available channel capacity.

More transmissions do not always mean more information.

Excessive beaconing, unnecessarily long digipeater paths, or overly long packets can reduce the ability of other users to access the channel.

In a well-designed network, transmissions should occur **as often as necessary, but no more often than necessary**.

Detailed rules concerning paths, digipeaters, collisions, and channel usage are described later in the documentation.

## Broadcast instead of a classic connection

Most APRS transmissions use AX.25 UI frames, where UI means *Unnumbered Information*.

A classic connection between sender and receiver is therefore not established beforehand.

A station transmits a packet, and all stations within range can receive it.

This approach fits the nature of APRS very well.

Information such as:

```text
I am here
```

```text
I am monitoring this frequency
```

```text
there is a repeater at this location
```

```text
a weather warning has been issued
```

can be useful to many users at the same time.

This does not mean APRS cannot support communication directed to a specific station.

The protocol includes short text messages and mechanisms for acknowledging their reception. These features are built on top of the broadcast nature of the basic network.

## What can be an APRS station?

An APRS node does not have to be only a vehicle GPS tracker.

The network can include, among other things:

- handheld radios,
- mobile radios,
- base stations,
- trackers,
- computers with software modems,
- digipeaters,
- IGates,
- weather stations,
- telemetry devices,
- balloons,
- portable stations,
- specialized APRS devices.

Some devices only transmit data. Others can also receive, analyse, and present it to the operator.

The fullest use of APRS occurs when an operator not only transmits their own position, but also uses information generated by other network users.

## A short history

APRS was developed by **Bob Bruninga, WB4APR**.

The roots of the system go back to the 1980s. One of its early core uses was displaying the location and status of participants in field activities.

Originally, the APRS acronym was expanded as:

**Automatic Position Reporting System**

As the system evolved, it became increasingly clear that position was only one of many types of information being exchanged.

The name therefore came to be expanded as:

**Automatic Packet Reporting System**

This change reflects very well how APRS should be understood.

It is not only a position reporting system.

It is a system for reporting **information**.

## APRS as a tool supporting communication

One of the most interesting uses of APRS is supporting traditional voice communication.

A station can transmit information about the currently monitored frequency together with its position.

An operator nearby can then see not only the station callsign, but also information that makes it possible to establish contact directly.

In the same way, APRS can provide information about local repeaters, events, activities, or other stations in the area.

In this sense, APRS is not a system that exists separately from traditional amateur radio communication.

It can act as an information layer that helps communication begin.

## How should APRS be understood?

The simplest way to think about APRS is as a shared radio channel carrying short messages about the current situation.

Each station can add a small piece of information to the overall picture:

```text
I am here.
```

```text
I am monitoring here.
```

```text
There is a repeater here.
```

```text
An event is taking place here.
```

```text
These are the current weather conditions.
```

```text
This device has this status.
```

```text
A warning is in effect for this area.
```

Together, these pieces of information create a current picture of what is happening on the local network.

That is the essence of APRS.

## Key points to remember

**APRS is not only a position tracking system.**

Position is only one of many data types.

**APRS can operate without the Internet.**

A local radio network can be the basic communication medium.

**APRS-IS extends the system, but it is not its essence.**

Internet services use information originating from the APRS network.

**APRS.fi is not APRS.**

It is one way of presenting data from APRS-IS.

**APRS is used to distribute short, current information.**

Its value depends mainly on whether the information is useful to other users of the network.

**APRS can support traditional radio communication.**

Information about position, status, and monitored frequency can directly help establish contact.

## Next

To understand the system in more detail, the next topics worth learning are:

- the path of an APRS packet between stations,
- the relationship between APRS and AX.25,
- APRS packet structure,
- the basic APRS data types,
- the role of digipeaters,
- the role of IGates and APRS-IS,
- the limitations of the APRS radio channel.

These topics are covered by the following articles in the **APRS protocol overview** section.

## Sources and further reading

This article was prepared primarily using:

- APRS materials and documentation created by Bob Bruninga, WB4APR,
- *APRS Protocol Reference*,
- the APRS Specification documentation project maintained by John Langner, WB2OSZ,
- *What is APRS?*,
- *Understanding APRS Packets*,
- materials related to the modern APRS 1.2 specification.

Reference materials used by the APRSBox project are also available in the `APRS-SPEC` directory of the repository:

`https://github.com/SQ9MDD/APRSBox/tree/main/APRS-SPEC`
