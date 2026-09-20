---
title: APRS packet anatomy
description: A detailed explanation of the text representation of an APRS packet, AX.25 fields, path information, and APRS data.
template: doc
tableOfContents: true
---

An APRS packet is very often presented in a readable text form:

```text
SOURCE>DEST,PATH:DATA
```

Example:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

At first glance, this looks like a single format.

In reality, this notation combines information from several AX.25 frame elements and the actual APRS data in a single line.

In simplified form:

```text
SQ9MDD-7 > APRS , WIDE1-1 : !5012.34N/01956.78E>
^^^^^^^^   ^^^^   ^^^^^^^   ^^^^^^^^^^^^^^^^^^^^^
 SOURCE    DEST     PATH           DATA
  AX.25    AX.25    AX.25          APRS
```

Understanding this division is the foundation for further protocol analysis.

## General packet form

The most common text representation of an APRS packet has the form:

```text
SOURCE>DEST,PATH:INFORMATION
```

Not all elements must always be present.

A packet without a path can look like this:

```text
SOURCE>DEST:INFORMATION
```

Example:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

A packet with a path can look like this:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:INFORMATION
```

Example:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:>QRV 145.550 MHz
```

Logically, it can therefore be divided into:

```text
SOURCE
DEST
PATH
INFORMATION
```

The first three elements are related to AX.25 addressing.

The `INFORMATION` field contains the actual APRS information.

## Step-by-step example

Consider the packet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

We can break it down into the following parts:

```text
SQ9MDD-7
```

packet source,

```text
>
```

separator used in the text representation,

```text
APRS
```

destination address,

```text
,
```

separator before the path,

```text
WIDE1-1
```

digipeater path,

```text
:
```

separator between the header and the information field,

```text
!
```

Data Type Identifier,

```text
5012.34N/01956.78E>
```

actual APRS data.

A single line therefore shows both AX.25 layer information and APRS protocol data.

## SOURCE

The first element is the source address:

```text
SOURCE
```

Example:

```text
SQ9MDD-7
```

In typical APRS use, this is the station callsign together with an optional SSID.

It can therefore appear, for example, as:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

SOURCE identifies the station that created the AX.25 frame.

It is an AX.25 addressing element, not part of the APRS information field.

## Callsign and SSID

An AX.25 address can contain a callsign and an SSID.

Example:

```text
SQ9MDD-7
```

can be read logically as:

```text
callsign: SQ9MDD
SSID:     7
```

SSID allows the same callsign to be used by several logical stations.

For example, an operator may use separate SSIDs for:

- a base station,
- a mobile station,
- a handheld radio,
- a digipeater,
- an IGate,
- a tracker.

There are historical and practical conventions regarding the meaning of individual SSIDs, but they should not be treated as an absolute protocol rule defining device function.

Details of callsigns and SSIDs are described separately.

## The `>` separator

After the source address, the following character appears in the text representation:

```text
>
```

For example:

```text
SQ9MDD-7>APRS
```

It separates SOURCE from DEST.

It is an element of the packet's text representation.

The `>` character should not be treated as a separate byte located at that point in the raw AX.25 frame transmitted over radio.

## DEST

After the `>` character comes the destination address.

Example:

```text
APRS
```

Together:

```text
SQ9MDD-7>APRS
```

In AX.25, this is the destination address.

In classic packet radio, it can identify the destination station.

In APRS, its meaning is often different.

## Destination in APRS

In many APRS packets, the destination field does not identify the actual recipient of the information.

It can be used, among other things, to identify:

- the device type,
- the manufacturer,
- the software,
- the application family,
- the method used to generate the packet.

For this purpose, APRS uses, among other mechanisms, the **TOCALL** system.

Example values can look like:

```text
APRS
APDWxx
APRSxx
```

Their exact meaning depends on the TOCALL assignment.

Therefore, the packet:

```text
SQ9MDD-7>APRS:...
```

does not mean:

```text
SQ9MDD-7 sends data to station APRS
```

Here, the destination field is primarily part of the AX.25 header and is also used by APRS for additional identification.

## Destination is not the APRS message recipient

This is one of the most important things to remember.

Consider:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Hello
```

The AX.25 destination is:

```text
APRS
```

but the message recipient is:

```text
SP9XYZ
```

The recipient is located inside the APRS data.

We therefore have two different concepts:

```text
AX.25 destination
```

and:

```text
APRS message addressee
```

They should not be confused with each other.

## PATH

A path may follow the destination:

```text
SOURCE>DEST,PATH:DATA
```

Example:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

The path can contain one or more elements:

```text
WIDE1-1
WIDE2-1
```

In the text representation, they are separated by commas.

## PATH is an AX.25 element

The path is not inside the APRS payload.

It represents the list of AX.25 digipeater addresses.

Logically:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:DATA
```

can be understood as:

```text
AX.25:
    SOURCE
    DEST
    DIGI 1
    DIGI 2

APRS:
    DATA
```

This matters when analysing packets.

`WIDE1-1` is not a command stored inside the APRS data.

It is part of AX.25 frame addressing.

## Multiple path elements

The path can contain several addresses.

Example:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

or after passing through infrastructure:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:...
```

The character:

```text
*
```

visible in the monitor representation can indicate that the given path element has already been used.

Details of the H-bit mechanism, `WIDE` aliases, the New-N Paradigm, and path processing are described in a separate article.

## The `:` separator

The most important boundary in the text representation of a packet is:

```text
:
```

Example:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Everything before the first `:` separator forms the text representation of header-related information.

What follows it is the information field.

We can therefore write:

```text
SOURCE>DEST,PATH : INFORMATION
^^^^^^^^^^^^^^^^   ^^^^^^^^^^^
     header           data
```

For APRS analysis, the actual APRS content begins on the right-hand side of this separator.

## INFORMATION

The field after the `:` separator contains the data carried in the AX.25 Information field.

Example:

```text
!5012.34N/01956.78E>
```

This is where the actual APRS data protocol is located.

The contents can represent, among other things:

- position,
- message,
- status,
- object,
- item,
- weather,
- telemetry,
- query,
- station capabilities,
- special data.

Interpretation of the field depends primarily on how it begins.

## Data Type Identifier

The first character of the APRS Information field is very often the **Data Type Identifier**, or DTI.

Example:

```text
!5012.34N/01956.78E>
^
|
DTI
```

The DTI defines how the remaining data should be interpreted.

Example DTIs include:

| DTI | General meaning |
|---|---|
| `!` | position without timestamp |
| `=` | position without timestamp, with messaging support |
| `/` | position with timestamp |
| `@` | position with timestamp, with messaging support |
| `:` | message |
| `;` | object |
| `)` | item |
| `>` | status |
| `?` | query |
| `_` | weather data without position |
| `T` | telemetry |
| `}` | third-party traffic |

This is not a complete table of all possible values.

A complete DTI overview is provided in the article devoted to APRS frame types.

## DTI is not always enough for full interpretation

Recognising the first character is only the beginning of packet parsing.

Example:

```text
!5012.34N/01956.78E>
```

DTI:

```text
!
```

indicates a position without a timestamp.

Only the following characters define:

- latitude,
- symbol table,
- longitude,
- symbol,
- optional extensions,
- comment.

An APRS parser must therefore first recognise the data type and then apply the rules appropriate to that type.

## Position example

The packet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

can be broken down as follows:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

```text
!
```

DTI for a position without timestamp,

```text
5012.34N
```

latitude,

```text
/
```

symbol table identifier,

```text
01956.78E
```

longitude,

```text
>
```

symbol code.

In this example, the whole packet can therefore be viewed as:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E >
SOURCE      DEST    PATH      DTI LATITUDE   LONGITUDE  SYMBOL
```

## The symbol is part of the position format

In an APRS position packet, the symbol is not transmitted as a name:

```text
car
house
repeater
```

Instead, it is defined by characters located at specific positions in the position format.

In a classic uncompressed position, the important elements are:

- the symbol table character,
- the symbol code.

Therefore:

```text
/
```

inside a position packet is not a text separator like `>` or `:`.

It is part of the APRS data and is significant when interpreting the symbol.

## Message example

Consider:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

The breakdown is as follows:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

and the information field:

```text
:SP9XYZ   :Test
```

begins with:

```text
:
```

which is the message DTI.

Next comes the addressee field:

```text
SP9XYZ
```

and the content:

```text
Test
```

It is worth noting the two different colons:

```text
...WIDE1-1::SP9XYZ   :Test
           ^^
```

The first:

```text
:
```

separates the header from Information.

The second:

```text
:
```

is the APRS message DTI.

This repetition is not accidental.

## Status example

The packet:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

contains:

```text
>
```

as the first character of the information field.

Here, `>` is the status DTI.

This shows why the same character can have different meanings depending on where it appears.

In the header:

```text
SOURCE>DEST
```

`>` is a separator in the text representation.

In Information:

```text
>QRV 145.550 MHz
```

`>` is an APRS DTI.

A parser must always take context into account.

## Object example

An object packet begins in Information with:

```text
;
```

Schematically:

```text
SOURCE>DEST,PATH:;OBJECTNAM*...
                 ^
                 |
                DTI
```

After the DTI come object-specific fields, including its name, state, time, and position.

The detailed object syntax is described in a separate part of the documentation.

## Comment

Many APRS formats allow an additional comment after the basic data.

For example, a position packet can contain:

```text
!5012.34N/01956.78E>QRV 145.550 MHz
```

The first part defines the position and symbol.

The following part:

```text
QRV 145.550 MHz
```

is the comment.

The comment can also contain specific APRS data extensions.

Therefore, not everything following the position should automatically be treated as arbitrary plain text.

## Data extensions

Additional information can appear after the basic APRS position.

Depending on the format and context, these can describe, among other things:

- course and speed,
- altitude,
- PHG,
- range,
- frequency,
- tone,
- offset,
- DAO,
- other extensions.

An example can therefore be much more elaborate than a simple position:

```text
SOURCE>DEST,PATH:POSITION + EXTENSION + COMMENT
```

The exact rules depend on the specific data type and are described in the relevant documentation sections.

## Information field length matters

APRS operates in a bandwidth-limited environment, especially on the classic 1200 baud VHF channel.

Every additional character increases transmission length.

A well-designed packet should therefore convey the required information without unnecessary content expansion.

This applies especially to:

- comments,
- position beacons,
- objects,
- statuses,
- messages.

A long packet occupies the channel longer than a short packet.

## The text shown in a monitor is TNC2 format

The form:

```text
SOURCE>DEST,PATH:INFORMATION
```

is commonly referred to as TNC2 monitor format or TNC2 representation.

It is used, among other things, by:

- terminals,
- APRS software,
- logs,
- APRS-IS servers,
- diagnostic tools.

It allows an AX.25 frame to be presented in a form that is easy for a human to read.

This does not mean that exactly this character string was transmitted over radio.

## What is actually inside an AX.25 frame?

On RF, addresses are not transmitted as literal text:

```text
SQ9MDD-7>APRS,WIDE1-1
```

An AX.25 frame has its own binary encoding for addresses and control fields.

It includes, among other things:

```text
Destination
Source
Digipeater addresses
Control
PID
Information
FCS
```

For typical APRS:

```text
Control = UI
PID     = no layer 3
```

The Information field, however, contains the APRS data.

The TNC2 representation hides many AX.25 layer details to make the packet easier to analyse.

## FCS is not visible in TNC2

A real radio frame includes an error-checking mechanism, FCS.

However, it is not visible in a typical line:

```text
SOURCE>DEST,PATH:DATA
```

The receiver, TNC, or modem checks frame validity earlier.

If the frame is accepted and passed to the application, the monitor representation usually does not contain the FCS.

This is another example of the difference between the real AX.25 frame and its text representation.

## Packet received directly

Example of a packet seen directly on RF:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

We can interpret it as:

```text
SOURCE: SQ9MDD-7
DEST:   APRS
PATH:   WIDE1-1
DATA:   !5012.34N/01956.78E>
```

This is one of the simplest analysis cases.

## Packet after passing through a digipeater

After retransmission, the representation may contain information about the path element that was used.

For example:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:!5012.34N/01956.78E>
```

The monitor can then show through which station the packet was repeated.

This does not mean that the APRS data:

```text
!5012.34N/01956.78E>
```

changed.

The information related to AX.25 transport changed.

## Packet in APRS-IS

After being forwarded by an IGate, the packet may look like this:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Here we can see the elements:

```text
qAR
SQ9MDD-4
```

which relate to APRS-IS.

The entire string between destination and `:` should therefore not be analysed as only a radio AX.25 path.

In APRS-IS, the packet representation can also contain information added by the Internet infrastructure.

## The boundary between RF and APRS-IS

Compare:

### RF packet

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

### Packet visible in APRS-IS

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

The actual APRS information:

```text
!5012.34N/01956.78E>
```

remains the same.

What changes is the information describing how the packet reached the given environment.

## Third-party packet

APRS also has a format that allows an entire packet to be placed inside another packet.

DTI:

```text
}
```

means third-party traffic.

Schematically:

```text
SOURCE>DEST,PATH:}ORIGINAL>DEST,PATH:DATA
                 ^
                 |
          third-party DTI
```

After the `}` character comes the representation of the original packet.

We therefore have a situation where another packet representation is contained inside the Information field of one frame.

A parser must recognise this case and handle the nested information appropriately.

## A packet can contain nested information

Third-party traffic demonstrates an important APRS property.

A packet cannot always be analysed using the simple assumption:

```text
find the first character
read the rest as data
```

Some data types create an additional internal structure.

A correct parser must therefore recognise the format implied by the DTI and only then interpret the remaining content.

## The meaning of character position

When analysing APRS, a character is significant not only because of its value but also because of where it appears.

For example, the character:

```text
:
```

can be:

- a separator between the header and Information in TNC2 representation,
- a message DTI,
- a separator inside the message format.

The character:

```text
>
```

can be:

- a separator between SOURCE and DEST,
- a status DTI,
- a symbol code in position data.

APRS should therefore not be analysed by searching for individual characters without considering structure.

## Parsing order

A practical packet parser can logically operate in the following order.

First, separate:

```text
HEADER:INFORMATION
```

Then recognise from the header:

```text
SOURCE
DEST
PATH
```

And then from the Information field:

```text
DTI
```

and select the appropriate data parser based on the DTI.

Diagram:

```text
packet
  |
  +-> header
  |     |
  |     +-> source
  |     +-> destination
  |     +-> path
  |
  +-> information
        |
        +-> DTI
              |
              +-> position parser
              +-> message parser
              +-> object parser
              +-> weather parser
              +-> ...
```

This is much safer than trying to interpret the entire packet as one string with a fixed structure.

## Not every position packet looks the same

Example:

```text
!5012.34N/01956.78E>
```

is a classic uncompressed position.

APRS also supports other position encodings, including:

- position with timestamp,
- compressed position,
- Mic-E,
- precision extensions.

A parser should therefore not assume that a position always contains readable coordinates in the form:

```text
DDMM.mmN
DDDMM.mmE
```

Detailed position formats are described in a separate section.

## Not every Information field begins with a simple DTI

Most common formats can be recognised easily from the first character.

However, there are historical formats, special formats, and constructions requiring further analysis.

A complete APRS implementation therefore requires support not only for the most common:

```text
!
=
:
;
>
```

but for the full set of formats defined by the specification and later extensions.

## What does the user see and what does the modem see?

The user can see:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

The APRS application sees logical fields:

```text
source
destination
path
information
```

The APRS parser sees inside Information:

```text
DTI
payload
```

The AX.25 layer operates on the actual frame fields.

The modem operates on the bitstream and signal.

The radio ultimately carries the RF signal.

Each layer therefore looks at the same packet from a different perspective.

## Common interpretation errors

### Everything before `:` is APRS

No.

A large part of that information describes the AX.25 header and path.

### `WIDE1-1` is part of the APRS payload

No.

It is an AX.25 path element.

### Destination is the message recipient

Not always.

The APRS message addressee is located inside Information.

### Every `:` character has the same meaning

No.

Its meaning depends on where it appears.

### The text form of the packet is the exact RF contents

No.

It is a monitor representation of the real AX.25 frame.

### Everything between destination and `:` comes from RF

Not always.

APRS-IS can contain elements added by the infrastructure, for example q-constructs.

### Every position has the form DDMM.mmN/DDDMM.mmE

No.

APRS has several position formats.

## How to read an APRS packet

For the packet:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>QRV 145.550
```

it is useful to read it in stages.

### 1. Who transmitted it?

```text
SQ9MDD-7
```

### 2. What is the destination?

```text
APRS
```

### 3. What is the path?

```text
WIDE1-1
```

### 4. Where does Information begin?

After:

```text
:
```

### 5. What is the DTI?

```text
!
```

meaning a position without a timestamp.

### 6. What are the actual data?

```text
5012.34N/01956.78E>
```

position and symbol.

### 7. Is there additional information?

```text
QRV 145.550
```

comment.

This approach allows even much more complex packets to be analysed step by step.

## The entire packet as layers

The same example can be shown as follows:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E > QRV 145.550
|          |      |          | |          |           | |
|          |      |          | |          |           | +-- comment
|          |      |          | |          |           +---- symbol
|          |      |          | |          +---------------- longitude
|          |      |          | +--------------------------- latitude
|          |      |          +----------------------------- DTI
|          |      +---------------------------------------- path
|          +----------------------------------------------- destination
+---------------------------------------------------------- source
```

At the layer level:

```text
AX.25
+---------------------------------------------------+
| SOURCE | DEST | PATH | INFORMATION               |
+---------------------------------------------------+
                       |
                       v
APRS
+---------------------------------------------------+
| DTI | packet-type data | extensions / comment    |
+---------------------------------------------------+
```

This is the most important model to remember.

## Key points to remember

**A text APRS packet combines AX.25 and APRS information.**

The form:

```text
SOURCE>DEST,PATH:DATA
```

is not one flat format.

**SOURCE, DEST, and PATH belong to the AX.25 addressing layer.**

They are not the actual APRS payload.

**The Information field begins after the `:` separator in TNC2 representation.**

This is where the APRS data is located.

**The first character of Information is often the DTI.**

It defines how the rest of the packet should be interpreted.

**Destination does not have to be the actual recipient.**

In APRS, it can identify a device or software.

**The APRS message addressee is located in Information.**

It is different from the AX.25 destination.

**The digipeater path is not part of the APRS data.**

It belongs to AX.25.

**TNC2 representation is not the raw radio frame.**

It hides some AX.25 fields and binary details.

**A packet visible in APRS-IS can contain information that was not present on RF.**

q-constructs are an example.

**The meaning of a character depends on its position in the structure.**

The packet should therefore be analysed by layer and according to the format implied by the DTI.

## Next

After understanding the anatomy of a single packet, its individual elements can be discussed in more detail.

The next topics include:

- source callsign,
- SSID,
- destination address,
- TOCALL,
- AX.25 paths,
- the WIDE mechanism,
- Data Type Identifier,
- Information field,
- timestamp,
- symbols,
- data extensions,
- position formats,
- Mic-E,
- messages,
- objects,
- telemetry,
- weather,
- third-party traffic,
- q-constructs.

From this point onward, individual APRS types can be analysed without mixing information that belongs to AX.25, APRS, and APRS-IS.
