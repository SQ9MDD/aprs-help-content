---
title: APRS protocol layers
description: Explanation of the relationship between APRS, AX.25, the radio layer, the modem, and APRS-IS.
template: doc
tableOfContents: true
---

APRS is not a single protocol that covers everything from the radio signal all the way to the user application.

In practice, the system consists of several layers that work together.

On the radio path, they can be shown in simplified form like this:

```text
application
   |
   v
APRS data
   |
   v
AX.25
   |
   v
modem / modulation
   |
   v
radio
   |
   v
RF
```

Each of these layers is responsible for something different.

APRS primarily defines the **meaning of the information**.

AX.25 provides the frame structure used to transport that information over radio.

The modem converts digital data into a signal that can be sent through the radio path.

The radio carries that signal over the RF channel.

On the Internet side, the same APRS information can be transported differently:

```text
APRS data
   |
   v
APRS-IS
   |
   v
TCP/IP
   |
   v
applications
```

Understanding this division is very important because many elements visible in a typical APRS packet actually belong to different layers of the system.

## APRS is not everything at once

In everyday language, the word "APRS" is often used to describe the whole system:

- radio,
- modulation,
- AX.25 frames,
- positions and messages,
- digipeaters,
- IGates,
- APRS-IS,
- Internet applications.

This is convenient, but technically imprecise.

For example, the transmission:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

contains elements that belong to different layers.

In a highly simplified form:

```text
SQ9MDD-7      AX.25 source
APRS          AX.25 destination
WIDE1-1       AX.25 path
!             APRS data type identifier
5012.34N...   APRS data
```

In addition, a real radio frame contains information that is not visible in this text representation.

## A simplified layer model

For APRS on a radio channel, the following model can be used:

```text
+-----------------------------+
| user application            |
+-----------------------------+
| APRS data                   |
+-----------------------------+
| AX.25 UI                    |
+-----------------------------+
| modem / modulation          |
+-----------------------------+
| radio                       |
+-----------------------------+
| RF channel                  |
+-----------------------------+
```

This is not a formal OSI model for the APRS protocol.

It is a practical way to show where the individual parts of the system are located and what they are responsible for.

## The radio layer

At the lowest level is the physical radio transmission.

This is where factors such as the following matter:

- operating frequency,
- channel bandwidth,
- transmitter power,
- antenna,
- propagation,
- signal level,
- interference,
- sharing the channel among many stations.

In European VHF APRS networks, the frequency:

```text
144.800 MHz
```

is very commonly used.

The frequency itself, however, is not part of the APRS protocol.

APRS can also be transported on other bands and using other transmission methods.

The frequency therefore belongs to the radio layer, not to the APRS data structure.

## Modem and modulation

Digital data must be converted into a signal that can be transmitted over radio.

In classic VHF APRS, the following is very commonly used:

```text
1200 baud AFSK
```

with modulation based on the Bell 202 standard.

A typical implementation uses two audio tones:

```text
1200 Hz
2200 Hz
```

Digital data is converted into the appropriate sequence of audio signals, which then modulate the radio transmitter.

On the receiving side, the process works in the opposite direction.

```text
RF
 |
 v
radio
 |
 v
audio
 |
 v
modem
 |
 v
digital data
```

The modem can be:

- a hardware device,
- part of a radio,
- a TNC,
- software running on a computer.

Dire Wolf is an example of a software modem.

It is important to remember that:

**1200 baud AFSK is not APRS.**

It is only one way to transport the data used by APRS.

## AX.25

Above the modem layer is **AX.25**.

AX.25 is a data link layer protocol used in packet radio.

AX.25 defines the structure of the radio frame, including, among other things:

- destination address,
- source address,
- optional digipeater addresses,
- control field,
- PID field,
- information field,
- frame error checking.

In simplified form:

```text
+-------------+
| Destination |
+-------------+
| Source      |
+-------------+
| Digipeaters |
+-------------+
| Control     |
+-------------+
| PID         |
+-------------+
| Information |
+-------------+
| FCS         |
+-------------+
```

APRS does not create its own complete radio frame structure from scratch.

Instead, it uses AX.25 as the mechanism for transporting data.

## AX.25 UI

Typical APRS traffic uses AX.25 **UI** frames, where UI means:

**Unnumbered Information**

This abbreviation should not be confused with *User Interface*.

UI frames allow information to be sent without first establishing a classic AX.25 connection.

This allows a station simply to transmit information such as:

```text
position
status
object
weather
telemetry
```

and every station within range can receive it.

This fits the broadcast nature of APRS very well.

## Where does APRS itself begin?

In a typical APRS frame, the actual APRS data is located in the **Information** field of the AX.25 frame.

It can be shown like this:

```text
AX.25
+------------------------------------------+
| addresses | control | PID | Information |
+------------------------------------------+
                           |
                           v
                      APRS data
```

The contents of this field define, among other things:

- position,
- status,
- message,
- object,
- weather,
- telemetry,
- query,
- other APRS information types.

The first character of the information field often acts as the **Data Type Identifier**, or DTI.

For example:

```text
!
=
/
@
:
;
>
?
```

can indicate different types of data.

The detailed meaning of individual DTIs is described in a separate part of the documentation.

## Example of dividing a packet into layers

Consider the packet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

We can divide it logically as follows:

```text
SQ9MDD-7
```

is the AX.25 source address.

```text
APRS
```

is the AX.25 destination address.

```text
WIDE1-1
```

is an element of the AX.25 path.

```text
:
```

separates the header from the information field in the text representation.

```text
!
```

is an APRS Data Type Identifier.

```text
5012.34N/01956.78E>
```

is the actual APRS content describing the position and symbol.

It is therefore clear that not all elements visible in the packet belong to the same layer.

## Destination address

The destination field is part of the AX.25 header.

In classic AX.25 use, the destination can indicate the destination station.

In APRS, the situation is more complex.

Many APRS packets use the destination address to identify the type of device or software.

Example:

```text
SQ9MDD-7>APRS:...
```

This does not mean that the packet is addressed to a station with the callsign `APRS`.

Likewise, the destination can contain values belonging to the TOCALL system.

For this reason, the destination address in APRS should not automatically be interpreted as the address of a specific recipient.

The detailed rules for destination addresses and TOCALL are described separately.

## The recipient of an APRS message

This distinction is especially important for messages.

For example, a packet may look like this:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

The AX.25 destination can still be:

```text
APRS
```

while the actual APRS message recipient:

```text
SP9XYZ
```

is located inside the APRS information field.

These are two different layers of addressing.

## Digipeater path

Elements such as:

```text
WIDE1-1
WIDE2-1
```

are not part of the actual APRS payload.

They belong to the list of digipeater addresses in the AX.25 frame.

This layer allows digipeaters to determine whether and how a frame can be retransmitted.

In text representation, we see this as:

```text
SOURCE>DEST,PATH:DATA
```

but logically:

```text
SOURCE
DEST
PATH
```

belong to the AX.25 header,

while:

```text
DATA
```

is the information field that contains APRS.

## Text representation is not the raw radio frame

APRS packets are very often shown as text:

```text
SOURCE>DEST,PATH:information
```

This is an extremely convenient representation for users, logs, and applications.

However, it is not an exact byte-for-byte record of what is present on the radio channel.

A real AX.25 frame contains elements that are not directly visible in the text representation, including:

- encoded AX.25 addresses,
- control bits,
- control field,
- PID,
- FCS,
- mechanisms related to frame transmission.

Therefore:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

should be treated as a readable representation of the packet, not as the literal contents of the radio transmission.

## APRS-IS

On the Internet side, APRS packets are not transmitted as raw AX.25 radio frames.

APRS-IS uses a text representation of packets similar to:

```text
SOURCE>DEST,PATH:information
```

A packet can therefore be received by an IGate over RF:

```text
AX.25
   |
   v
IGate
```

and then forwarded to APRS-IS as a text representation:

```text
SOURCE>DEST,PATH:information
```

Information specific to APRS-IS can be added to the packet.

An example is **q-constructs**.

## q-constructs are not part of the radio path

In APRS-IS, you may see a packet such as:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:...
```

The element:

```text
qAR
```

was not transmitted over radio by the station as another element of the normal digipeater path.

It is information added in the APRS-IS environment.

Likewise:

```text
SQ9MDD-4
```

in this part can identify the IGate responsible for forwarding the packet to APRS-IS.

Therefore, the entire path visible in APRS-IS should not be interpreted as an exact copy of the path present on RF.

## RF and APRS-IS are different transport environments

The same APRS information can be carried by different paths.

On RF:

```text
APRS data
   |
   v
AX.25
   |
   v
modem
   |
   v
radio
```

On the Internet:

```text
APRS data
   |
   v
APRS-IS
   |
   v
TCP/IP
```

In both cases, the meaning of the APRS information can remain the same.

What changes is the method used to transport it.

This is an important distinction.

**APRS is an information layer, while RF and APRS-IS are different environments through which that information can be transported.**

## IGate as the boundary between environments

An IGate sits between the radio network and APRS-IS.

In the RF -> Internet direction:

```text
RF
 |
 v
AX.25
 |
 v
IGate
 |
 v
APRS-IS
```

The IGate receives the radio frame, interprets the required elements, and forwards the appropriate representation of the packet to APRS-IS.

Schematically:

```text
[RF / AX.25]
      |
      v
    IGate
      |
      v
 [APRS-IS]
```

However, this is not simply forwarding bits from one interface to another.

The IGate operates at the boundary between two different transport environments.

## APRS-IS -> RF direction

Moving in the opposite direction is more complex.

You cannot simply take any text line from APRS-IS and transmit it unchanged over radio.

APRS provides mechanisms that define how selected information is moved from APRS-IS to RF.

In particular, the format:

**third-party traffic**

can be used.

It allows information about the origin of the original packet to be preserved.

Detailed rules for this mechanism are described in the sections covering special formats and IGate operation.

## Third-party traffic

The third-party traffic mechanism creates an additional encapsulation layer.

In highly simplified form:

```text
AX.25
 |
 v
APRS third-party packet
 |
 v
original packet
```

This means that a packet originating in another environment is not simply copied as a normal RF packet.

It is placed inside a special APRS format.

This allows the receiver to recognize that the information was forwarded by another element of the infrastructure.

## What belongs to which layer?

The table below shows several common APRS elements and where they belong in the system.

| Element | Layer |
|---|---|
| `144.800 MHz` | radio / RF |
| `1200 baud` | modem / transmission |
| AFSK | modulation |
| Bell 202 | modulation |
| AX.25 | data link layer |
| Source callsign | AX.25 |
| Destination address | AX.25 |
| `WIDE1-1` | AX.25 path |
| `WIDE2-1` | AX.25 path |
| UI | AX.25 frame type |
| PID | AX.25 |
| FCS | AX.25 |
| `!` | APRS DTI |
| `=` | APRS DTI |
| `:` | APRS message DTI |
| position | APRS |
| object | APRS |
| message | APRS |
| telemetry | APRS |
| weather | APRS |
| TOCALL | APRS use of destination |
| APRS-IS | APRS Internet transport |
| `qAR` | APRS-IS |
| TCP/IP | Internet transport |
| APRS.fi | application / service |

This separation helps explain why some packet elements appear only on RF, some only in APRS-IS, while others retain their meaning in both environments.

## The same data, different transports

Assume that the actual APRS information describes a station position.

On RF, it can be transported like this:

```text
APRS position
     |
     v
   AX.25
     |
     v
   AFSK
     |
     v
    RF
```

After passing through an IGate:

```text
APRS position
     |
     v
  APRS-IS
     |
     v
   TCP/IP
```

The meaning of the position does not have to change.

Only the mechanism used to carry the information changes.

## The application is above all of this

At the very end is the user application.

It can be:

- a radio with an APRS decoder,
- an APRS terminal,
- a computer application,
- a map,
- an Internet service,
- a monitoring system,
- a database.

The application interprets APRS information and presents it to the user.

For example:

```text
RF
 |
 v
AX.25
 |
 v
APRS
 |
 v
application
 |
 v
map
```

or:

```text
APRS-IS
   |
   v
application
   |
   v
map
```

The map is therefore the final presentation layer for the information, not the APRS protocol itself.

## Common mistakes

### APRS and AX.25 are the same thing

No.

AX.25 provides, among other things, the frame structure and data link layer addressing.

APRS uses AX.25 to transport its own data.

### 1200 baud AFSK is APRS

No.

1200 baud AFSK is one method of data transmission.

APRS sits above it.

### WIDE1-1 is part of the APRS data

No.

`WIDE1-1` is part of the AX.25 address path.

### Destination address always identifies the APRS recipient

No.

In APRS, the destination can also identify a device or software implementation.

### qAR is part of the radio path

No.

`qAR` is an APRS-IS element.

### A text packet line is the exact radio frame

No.

```text
SOURCE>DEST,PATH:DATA
```

is a readable representation of the information contained in the frame, but it does not include all elements of the actual AX.25 transmission.

### APRS-IS is an Internet version of AX.25

Not directly.

APRS-IS transports APRS information in an Internet environment using its own method of packet representation and distribution.

## The complete picture

The layers of radio transmission can be summarized as follows:

```text
+-----------------------------+
| Application                 |
+-----------------------------+
| APRS                        |
| positions, messages,        |
| objects, weather, telemetry |
+-----------------------------+
| AX.25 UI                    |
| addresses, path, control,   |
| PID                         |
+-----------------------------+
| modem / modulation          |
| e.g. 1200 baud AFSK         |
+-----------------------------+
| radio                       |
+-----------------------------+
| RF                          |
+-----------------------------+
```

On the APRS-IS side:

```text
+-----------------------------+
| Application                 |
+-----------------------------+
| APRS                        |
+-----------------------------+
| APRS-IS                     |
+-----------------------------+
| TCP/IP                      |
+-----------------------------+
| Internet network            |
+-----------------------------+
```

An IGate can operate between these environments:

```text
             RF                         Internet

+--------------------------+       +----------------------+
| APRS                     |       | APRS                 |
+--------------------------+       +----------------------+
| AX.25                    |       | APRS-IS              |
+--------------------------+       +----------------------+
| modem                    |       | TCP/IP               |
+--------------------------+       +----------------------+
| radio                    |       | Internet             |
+--------------------------+       +----------------------+
             \                         /
              \                       /
               +-------- IGate ------+
```

## Key points to remember

**APRS does not define the entire radio transmission.**

It uses other layers, especially AX.25.

**AX.25 and APRS are not the same thing.**

AX.25 transports data, while APRS defines the meaning of the information contained in the data field.

**1200 baud AFSK is not APRS.**

It is one way of carrying AX.25 frames over radio.

**WIDE1-1 and similar elements belong to the AX.25 path.**

They are not part of the actual APRS payload.

**The destination address is an AX.25 element.**

APRS can additionally use it to identify a device or software implementation.

**APRS-IS is a different transport environment for APRS information.**

It does not transmit raw radio frames 1:1.

**q-constructs belong to APRS-IS.**

They should not be interpreted as normal elements of the radio path.

**The text representation of a packet combines information from several layers in a single line.**

Therefore:

```text
SOURCE>DEST,PATH:DATA
```

is a very convenient representation for people and applications, but it is not a literal image of the entire radio transmission.

## Next

After understanding the layers of the system, it is possible to move on to a more detailed analysis of a single packet.

The next topics should include:

- AX.25 frame structure,
- anatomy of the text representation of an APRS packet,
- source callsign and SSID,
- destination address,
- TOCALL,
- digipeater paths,
- APRS information field,
- Data Type Identifier,
- differences between an RF packet and its APRS-IS representation,
- q-constructs,
- third-party traffic.

With this foundation, the following protocol elements can be analysed with a clear understanding of **which layer of the system they actually belong to**.
