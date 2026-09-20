---
title: How does APRS work?
description: An overview of how information travels through APRS, from radio transmission through digipeaters and IGates to APRS-IS and applications.
template: doc
tableOfContents: true
---

APRS is a distributed system.

There is no single central point through which every piece of information must pass. A packet can be received directly by another station, repeated by a digipeater, forwarded to APRS-IS by an IGate, or reach several parts of the infrastructure at the same time.

For this reason, APRS is best understood not as a single linear route, but as **a radio network in which one transmission can be used by many receivers**.

The simplest packet path can look like this:

```text
Station A
   |
   | RF
   v
Station B
```

But in a larger network, the same packet can reach several receivers at the same time:

```text
                  -> Station B
                 /
Station A -------+-> Digipeater
                 \
                  -> IGate
```

Each of these elements has a different role.

## The simplest case: station to station

APRS does not require the Internet, a server, or a digipeater to work.

If two stations are within direct radio range of each other, one can transmit an APRS packet and the other can receive it directly.

```text
Station A
   |
   | RF
   v
Station B
```

Station A can, for example, transmit:

- its position,
- status,
- monitored frequency,
- a message,
- telemetry data,
- weather information.

If Station B can receive and interpret that type of information, it can use it immediately.

At this stage, the packet does not need to go anywhere else.

This is already a correctly functioning APRS exchange.

## What is actually transmitted?

A typical APRS packet on the radio layer is carried in an **AX.25 UI** frame, where UI means *Unnumbered Information*.

In simplified form, it contains, among other things:

- the source station callsign,
- the destination field,
- the path,
- the APRS information field.

In text representation, such a packet can look like this:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

At this stage, however, the most important point is not exactly how the packet is built, but what happens to it after transmission.

The detailed structure of AX.25 frames and APRS data is described in later articles.

## One transmission can have many receivers

A radio transmission is not directed to a single physical device in the same way as a classic point-to-point connection.

If several stations are within range of the transmitter, they can all receive the same packet.

For example:

```text
                -> Station B
               /
Station A -----+-> Digipeater
               \
                -> IGate
```

The same single transmission from Station A can therefore be received by:

- another user station,
- a digipeater,
- an IGate,
- several such devices at the same time.

This does not mean the packet must later pass through every one of these elements.

Each receiver can perform its own task independently of the others.

## The role of a digipeater

A **digipeater** is a station that can receive an APRS packet and retransmit it over radio.

Its primary purpose is to extend the radio range of the information.

Example:

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

Station B may be outside the direct range of Station A but can still receive its packet thanks to retransmission.

A digipeater does not, however, automatically repeat every frame it receives.

The decision to retransmit depends, among other things, on:

- the packet path,
- digipeater configuration,
- duplicate suppression mechanisms,
- local network policy.

Modern APRS networks most often use path mechanisms based, among other things, on `WIDE1-1` and `WIDE2-n`.

The detailed rules for paths and digipeaters are described separately.

## A digipeater is not an IGate

A digipeater and an IGate are two different functions.

A digipeater works primarily on the radio side:

```text
RF -> RF
```

It receives a radio packet and, under defined conditions, retransmits it over radio.

An IGate, on the other hand, connects the radio network to APRS-IS:

```text
RF -> Internet
```

One device can perform both roles at the same time, but it does not have to.

A station can therefore be:

- a digipeater only,
- an IGate only,
- both a digipeater and an IGate.

Understanding this distinction is important for understanding the entire APRS infrastructure.

## The role of an IGate

An **IGate**, or Internet Gateway, receives packets from the local radio channel and can forward them to the APRS-IS network.

Example:

```text
Station A
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

If the IGate receives a packet from Station A, it can send it to APRS-IS.

From that point onward, the information can become available to:

- APRS clients,
- mapping services,
- databases,
- monitoring applications,
- other systems using APRS-IS.

An IGate is not required for APRS to work locally.

Its primary role is to connect the local radio network to Internet infrastructure.

## The role of APRS-IS

**APRS-IS, the APRS Internet System**, is a global Internet infrastructure used to distribute APRS data.

Among other things, APRS-IS receives packets forwarded by IGates.

A simplified diagram can look like this:

```text
RF network
   |
   v
IGate
   |
   v
APRS-IS
   |
   +-> APRS applications
   |
   +-> mapping services
   |
   +-> monitoring systems
   |
   +-> other services
```

APRS-IS allows applications to receive data originating from many different geographical areas.

This does not mean APRS-IS replaces the radio network.

It extends it.

## A typical packet path

Consider a simple example.

The station:

```text
SQ9MDD-7
```

transmits an APRS packet over radio.

The packet can be received by a local station:

```text
SQ9MDD-7
   |
   | RF
   v
Station B
```

At this point, the information is already useful.

At the same time, the same packet can be received by a digipeater:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
```

If the path and configuration allow it, the digipeater retransmits the packet:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Station C
```

The packet can also be received by an IGate:

```text
SQ9MDD-7
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

The data can then be retrieved by an application:

```text
SQ9MDD-7
   |
   v
IGate
   |
   v
APRS-IS
   |
   v
APRS application
```

This can still be the same original packet.

## A packet does not have to pass through every stage

It is very important not to treat this path as a mandatory chain:

```text
Station -> Digipeater -> IGate -> APRS-IS
```

That is only one possible route.

A packet can be received directly:

```text
Station A -> Station B
```

It can be received by an IGate without a digipeater:

```text
Station A -> IGate -> APRS-IS
```

It can remain entirely within the radio network:

```text
Station A -> Digipeater -> Station B
```

It can also reach several receivers at the same time:

```text
                     -> Station B
                    /
Station A -> Digipeater -> Station C
       \            \
        \            -> IGate 2
         \
          -> IGate 1
```

APRS therefore does not have one mandatory packet route.

## One frame can arrive by many paths

In a real network, the same frame can be received by several digipeaters and several IGates.

For example:

```text
                    -> IGate 1
                   /
Station A -> DIGI -+-> IGate 2
        \          \
         \          -> Station B
          \
           -> IGate 3
```

In addition, some IGates may also receive the original transmission directly.

As a result, the same information can reach the infrastructure through more than one path.

This is normal in APRS.

## Duplicates

Because one transmission can be received and forwarded by many parts of the infrastructure, APRS must deal with duplicates.

The same frame can, for example, be:

- received directly by an IGate,
- received again after retransmission by a digipeater,
- received by a second IGate,
- forwarded to APRS-IS from several locations.

For this reason, digipeaters, IGates, and servers can use mechanisms to recognize and reject duplicates.

Without such mechanisms, one transmission could multiply unnecessarily through the network.

Detailed duplicate handling rules depend on the specific infrastructure component and are described later in the documentation.

## RF to APRS-IS

The direction:

```text
RF -> APRS-IS
```

is one of the primary tasks of an IGate.

Packets received locally over radio can be forwarded to APRS-IS, where they become available to Internet applications.

This does not mean that every received frame must be forwarded.

An IGate can apply specific rules and filters depending on its configuration and the type of traffic.

## APRS-IS to RF

The opposite direction:

```text
APRS-IS -> RF
```

requires much more care.

The radio channel has limited capacity, so the entire APRS-IS traffic cannot simply be retransmitted onto RF.

Only selected information can be forwarded to radio according to IGate operating rules.

A typical example is a message addressed to a local station that has recently been heard by that IGate.

The path can then look like this:

```text
APRS-IS
   |
   v
IGate
   |
   | RF
   v
Local station
```

Traffic in the Internet -> RF direction requires control because every such packet consumes airtime on the shared radio channel.

Detailed APRS-IS -> RF gating rules are described separately.

## Third-party traffic

In some situations, a packet originating in another part of the system must be placed onto RF again.

APRS provides a special **third-party traffic** mechanism for this purpose.

This is not simply a matter of copying a text packet from APRS-IS and transmitting it unchanged over radio.

The original information is placed inside a special structure that preserves information about its origin.

This mechanism is important, among other things, for controlled forwarding of selected traffic from APRS-IS to RF.

The detailed third-party traffic syntax is described in the section covering special formats.

## What works without the Internet?

A lot.

Example:

```text
HT
 |
 | RF
 v
Digipeater
 |
 | RF
 v
Mobile radio
```

If the mobile radio operator receives the packet from the portable station, APRS has fulfilled its purpose.

APRS-IS is not required.

An Internet map is not required.

No server is required.

**No Internet does not mean no APRS.**

## What works without a digipeater?

If stations are within direct range of one another, a digipeater is not required.

```text
Station A
   |
   +-----> Station B
   |
   +-----> IGate
```

Station B can use the information locally, while the IGate can independently forward it to APRS-IS.

A digipeater is needed only when retransmission actually extends the useful range of the network.

## What works without an IGate?

A complete local radio network can still work.

```text
Station A
   |
   v
Digipeater
   |
   v
Station B
```

Stations can exchange positions, status reports, messages, and other information without any connection to APRS-IS.

The packets will not appear in Internet services, but the local APRS function remains fully useful.

## Direct reception matters

In practice, a packet received directly may be more valuable than information available through the Internet.

If a nearby station transmits:

```text
SP9XYZ
145.550 MHz
```

an operator can immediately use that information to establish communication.

There is no need to wait for the packet to:

- be received by an IGate,
- reach APRS-IS,
- be stored by an Internet service,
- be retrieved by an application.

The local radio path is a fundamental part of APRS.

## Common misconceptions

### Every packet must pass through a digipeater

No.

If the receiver is within direct range of the transmitter, it can receive the packet without any retransmission.

### A digipeater forwards packets to the Internet

Not necessarily.

The primary function of a digipeater is to retransmit packets over radio.

The IGate function is responsible for connection to APRS-IS.

### An IGate must also be a digipeater

No.

An IGate can simply receive RF traffic and forward it to APRS-IS.

### If a packet does not appear on APRS.fi, APRS did not work

No.

The packet may have been received correctly and used locally by other stations.

### APRS-IS sends all traffic back to radio

No.

Traffic from APRS-IS to RF must be limited and controlled.

### A packet has one fixed route

No.

The same transmission can be received by many stations and reach the infrastructure through different paths.

## The whole picture

A simplified model of APRS operation can be shown like this:

```text
                         +-> Local station
                         |
[APRS station] -- RF ----+-> [Digipeater] -- RF --> other stations
                         |
                         +-> [IGate]
                               |
                               | Internet
                               v
                            [APRS-IS]
                               |
                 +-------------+-------------+
                 |             |             |
                 v             v             v
             applications     maps        services
```

The most important point is that none of these paths is mandatory.

APRS can work as:

```text
station -> station
```

```text
station -> digipeater -> station
```

```text
station -> IGate -> APRS-IS
```

or through all of these paths at the same time.

## Key points to remember

**APRS is a distributed system.**

One transmission can be received by many stations at the same time.

**A digipeater extends radio coverage.**

Its primary role is retransmitting RF packets.

**An IGate connects the local radio network to APRS-IS.**

It does not have to be a digipeater at the same time.

**APRS-IS extends the reach of information beyond the local radio network.**

It does not replace basic RF communication.

**A packet does not have to reach the Internet to fulfil its purpose.**

Local reception of the information may be its most important goal.

**One frame can arrive through many paths.**

Duplicates and their suppression are therefore a natural part of network operation.

**Traffic from the Internet to RF must be controlled.**

The radio channel has limited capacity and cannot be treated as a copy of APRS-IS.

## Next

After understanding how information travels through the network, the next topics to study are:

- the relationship between APRS and AX.25,
- AX.25 frame structure,
- APRS packet structure,
- source addresses and SSIDs,
- destination addresses and TOCALL,
- digipeater paths,
- differences between RF and APRS-IS,
- third-party traffic mechanisms,
- q-constructs,
- detailed IGate operating rules.

Only when these elements are combined does the full picture emerge of how APRS information moves between stations and the different parts of the infrastructure.
