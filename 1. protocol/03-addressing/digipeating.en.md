---
title: Digipeating in APRS
description: How APRS packet retransmission by digipeaters works, what the H-bit and the * marker mean, how WIDEn-N paths are processed, and why duplicate suppression is essential for correct network operation.
template: doc
tableOfContents: true
---

A **digipeater** is a digital relay station. It receives a complete AX.25 frame, analyses its path and, when the required conditions are met, transmits it again.

It therefore does not work like an analogue repeater that continuously retransmits a signal. A digipeater operates according to the **store and forward** principle:

```text
receive frame
    ↓
check path and rules
    ↓
check for duplicate
    ↓
modify path field
    ↓
retransmit
```

Most often the packet is retransmitted on the same radio channel, but a multi-port digipeater can also forward traffic between different channels.

The basic retransmission decision concerns the AX.25 address field, not the APRS content in the Information field. A digipeater does not need to understand whether a packet contains a position, message, weather report or telemetry in order to process its path correctly.

## Important note about the specification

The original APRS Protocol Reference describes path formats and APRS mechanisms, but it does not contain a complete, unambiguous algorithm for a modern digipeater.

As a result, implementations developed over the years differ in some details.

This article uses the following as its reference points:

- the modern, traceable **New-N** model,
- APRS 1.1 recommendations,
- the reference algorithm described by John Langner WB2OSZ in **APRS Digipeater Algorithm**.

Where older equipment or other implementations may behave differently, this is noted explicitly.

## AX.25 path field

An example APRS packet may look like this:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

After the Source Address and Destination Address come the digipeater addresses:

```text
WIDE1-1,WIDE2-1
```

AX.25 allows a maximum of eight digipeater addresses in one frame.

Each such address contains, among other things, an **H-bit**, meaning **Has Been Repeated**. This bit indicates whether that path element has already been used.

A digipeater makes its decision based on the **first unused path element**.

Elements that have already been used are not processed again.

## H-bit and the `*` marker

In textual monitor representation, the state of the H-bit is shown with:

```text
*
```

Example:

```text
SQ9MDD-7>APBOX0,SR5ABC*:...
```

means that the `SR5ABC` address has already been used for retransmission.

`*` is not part of the callsign or alias. It is a textual representation of the state of the AX.25 address field.

### Only the last used address carries `*`

In classic monitor format, the `*` character is placed next to the **last used digipeater address**.

If the path looks like:

```text
SR5AAA,SR5BBB*
```

it means that both:

```text
SR5AAA
SR5BBB
```

have been used, and `SR5BBB` is the last used element.

A representation such as:

```text
SR5AAA*,SR5BBB*
```

may be shown by some software as a direct visualisation of individual H-bits, but it is not the standard convention of classic monitor format. In standard representation, earlier used addresses are implied by the position of the last asterisk.

## Retransmission by explicit callsign

The simplest case occurs when the source station places specific digipeater callsigns in the path:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB:...
```

`SR5AAA` sees its own callsign as the first unused path element.

After retransmission we get:

```text
SQ9MDD-7>APBOX0,SR5AAA*,SR5BBB:...
```

Then `SR5BBB` may use the next element.

After its retransmission, standard monitor representation will look like:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

In the actual AX.25 field, the H-bit is set for both used addresses.

This mode of operation is routing through explicitly specified digipeaters.

## Aliases

APRS does not require users to know the callsigns of every digipeater along the route. Aliases are commonly used instead.

A digipeater may respond to:

- its own callsign,
- a configured simple alias,
- a **WIDEn-N** alias,
- local or regional aliases built according to similar rules.

If an alias is to be traceable, a modern digipeater should leave its own callsign in the path so that the actual route of the packet can later be determined.

For example, a traceable alias:

```text
TEMP
```

may be replaced after retransmission with:

```text
SR5ABC*
```

Non-traceable alias implementations also exist, where the alias is only marked as used. Modern APRS practice, however, prefers paths from which the real route of the packet can be reconstructed.

## WIDEn-N

The most important modern mechanism for general APRS digipeating is **WIDEn-N**.

Example:

```text
WIDE2-2
```

The first number:

```text
2
```

defines the alias class `WIDE2`.

The second number:

```text
2
```

is the counter of remaining uses of that path element.

In simplified form:

```text
WIDE2-2
      ^
      2 uses remain
```

Every correctly operating WIDEn-N digipeater decrements the second number during retransmission.

### When N is greater than 1

The packet:

```text
SQ9MDD-7>APBOX0,WIDE2-2:...
```

is received by digipeater `SR5AAA`.

After retransmission:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

The digipeater:

1. inserted its callsign into the path,
2. marked it as used,
3. decremented the counter from `2` to `1`.

The remaining:

```text
WIDE2-1
```

is now the first unused path element and may be handled by another digipeater.

### When N equals 1

If another digipeater `SR5BBB` receives:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

the counter is exhausted.

In the reference algorithm, a pointless:

```text
WIDE2-0
```

is not left in the path.

The alias is replaced by the digipeater callsign.

In standard monitor representation, the result will look like:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

The packet has consumed the complete planned `WIDE2-2` section.

Some older implementations may leave an exhausted alias with its H-bit set, so different representations can still appear in real logs.

### When N equals 0

An element such as:

```text
WIDE2-0
```

is exhausted and should not cause another retransmission.

In a correctly formed path, such a state should not appear as the first unused address.

## WIDE1-1 and the fill-in digipeater

The alias:

```text
WIDE1-1
```

has a special role in a typical APRS network architecture.

It is used primarily to obtain the first local hop through a **fill-in digipeater**, a lower-coverage station placed where users do not have good direct access to the main wide-area digipeaters.

An example path:

```text
WIDE1-1,WIDE2-1
```

may be processed as follows.

Source packet:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

after the local fill-in `SR5FILL`:

```text
SQ9MDD-7>APBOX0,SR5FILL*,WIDE2-1:...
```

then after the main digipeater `SR5WIDE`:

```text
SQ9MDD-7>APBOX0,SR5FILL,SR5WIDE*:...
```

A fill-in should be configured according to its local role. A typical fill-in responds to `WIDE1-1`, but should not behave like a full wide-area digipeater handling arbitrary `WIDEn-N`.

Choosing a path from the user's perspective is a separate topic. Here the important point is how the individual path elements are processed.

## The first unused element is the key

The basic digipeater algorithm does not search arbitrarily through the path for something it knows how to handle.

The primary element examined is:

**the first unused digipeater address.**

For:

```text
SR5AAA*,WIDE2-1,SR5XYZ
```

the first unused element is:

```text
WIDE2-1
```

`SR5XYZ` is later in the path and, in normal operation, should not "jump over" `WIDE2-1`.

This rule preserves the path order specified by the sender and prevents arbitrary bypassing of path elements.

## Preemptive digipeating

Some modern implementations offer optional **preemptive digipeating**.

This mechanism allows a digipeater to find its own callsign or a selected alias later in the unused part of the path and react despite preceding elements.

For example:

```text
WIDE1-1,SR5ABC
```

would normally require `WIDE1-1` to be used first.

Preemptive digipeating may allow `SR5ABC` to take the packet earlier.

This is not, however, a basic rule of classic APRS digipeating. It changes the semantics of path ordering and should be used deliberately. Exact behaviour depends on the implementation.

## Duplicate suppression

The WIDEn-N counter and H-bits alone are not enough to protect the network.

A digipeater must also detect **duplicates**.

The same original transmission may be heard by several neighbouring digipeaters and then arrive back at one of them through different paths.

Without memory of recently retransmitted packets, the network would quickly begin generating repeated copies of the same information.

In the WB2OSZ reference algorithm, a digipeater remembers packets retransmitted recently, typically for about:

```text
30 s
```

If the same packet appears again within that window, it is not retransmitted again.

### The path cannot be part of duplicate comparison

This is very important.

The same frame can have different paths after passing through different digipeaters:

```text
SQ9MDD-7>APBOX0,SR5AAA*:...
```

and:

```text
SQ9MDD-7>APBOX0,SR5BBB*:...
```

yet these may still be two copies of the same original transmission.

The path field therefore cannot determine whether a packet is a duplicate.

The WB2OSZ reference algorithm compares:

- Source Address,
- Destination Address, ignoring its SSID,
- the Information field,

and ignores the digipeater addresses.

Implementations often store a hash of these values rather than the full frame.

## Why a digipeater should not modify packet data

During normal modern digipeating, the part that changes is the **digipeater path**.

A digipeater should not modify:

```text
Source Address
Destination Address
Information
```

Changing any of these fields would mean that the retransmitted copy is no longer the same frame from the point of view of duplicate-detection mechanisms.

This is especially important because different copies of the same transmission may travel over different paths while still needing to be recognised as the same packet.

The historical routing mechanism based on Destination SSID was an exception to this modern rule, but it is now obsolete and should not be the basis of new implementations.

## Loop protection

A correctly operating APRS network uses several independent safeguards:

1. The **H-bit** marks path elements that have already been used.
2. The **first unused address** enforces processing order.
3. The **WIDEn-N counter** limits the number of further retransmissions.
4. **Duplicate suppression** prevents retransmitting the same transmission received by another route.
5. **Tracing with the digipeater's own callsign** makes the actual route visible and helps identify faulty network behaviour.

No single mechanism replaces the others.

## Traced and non-traced digipeating

Historically, aliases were handled in two main ways.

### Non-traced

The alias remained in the path and only its state or counter changed.

Such a representation did not make it possible to determine unambiguously which physical digipeater performed the retransmission.

### Traced

The digipeater inserts its own callsign into the path.

Example:

```text
WIDE2-2
```

after the first hop:

```text
SR5AAA*,WIDE2-1
```

This allows the receiver to reconstruct the actual route taken by the packet.

The modern **New-N Paradigm** prefers traceable WIDEn-N specifically because it enables network analysis, diagnostics and management.

## RELAY, WIDE and TRACE - historical mechanisms

Older APRS documentation may contain paths such as:

```text
RELAY
WIDE
TRACE
TRACEn-N
```

They should not be confused with modern:

```text
WIDE1-1
WIDEn-N
```

The old `RELAY`, plain `WIDE` and `TRACE` aliases were retired as part of the **New-N Paradigm**, because older implementations generated large numbers of duplicates and did not provide sufficient control over network traffic.

APRS 1.1 and later recommendations direct new implementations toward traceable WIDEn-N and small values of N justified by local network conditions.

Articles and configurations using `RELAY,WIDE` should be treated as historical material.

## Limiting excessively large N values

A modern digipeater does not have to honour every:

```text
WIDEn-N
```

value received from a user without limits.

Implementations may apply local limits and traps for excessively large values so that a single misconfigured station cannot unnecessarily occupy a large part of the shared channel.

For example, a packet with a very aggressive path may be:

- dropped,
- limited to a single hop,
- rewritten according to local policy.

This is protection of a shared channel resource, not a change to the basic meaning of WIDEn-N.

## Maximum path length

AX.25 allows a maximum of eight digipeater addresses.

This matters for traceable WIDEn-N because successive digipeaters may insert their callsigns into the path.

If the address list is already full, an implementation cannot continue adding new elements indefinitely.

The reference algorithm accounts for this limit when rewriting the path.

New systems should not create artificially long routes. In practical APRS, the objective is effective access to the local network, not maximising the number of hops.

## Delayed and "viscous" digipeating

Some implementations use an additional delayed-retransmission mechanism.

The digipeater:

1. qualifies a packet for retransmission,
2. does not transmit it immediately,
3. listens for a short time to determine whether another digipeater retransmits the same frame,
4. cancels its own transmission if it hears the appropriate copy.

This can reduce the number of simultaneous retransmissions in the network.

It is not a basic element of APRS or AX.25 framing. It is an implementation strategy used by some programs and devices.

## What does a digipeater actually change?

For modern standard digipeating, the most important rule can be reduced to one sentence:

> **A digipeater forwards the same packet, changing only the information required to process the AX.25 path.**

Example:

```text
before:
SQ9MDD-7>APBOX0,WIDE2-2:!5213.45N/02100.12E>Test

after the first digi:
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:!5213.45N/02100.12E>Test
```

The following did not change:

```text
SQ9MDD-7                    Source Address
APBOX0                      Destination Address
!5213.45N/02100.12E>Test    Information
```

Only the path changed:

```text
WIDE2-2
```

to:

```text
SR5AAA*,WIDE2-1
```

This separation is essential for correct routing, path tracing and duplicate suppression.

## Sources

This article is based on:

- [APRS Digipeater Algorithm - WB2OSZ](https://github.com/wb2osz/aprsspec/blob/main/APRS-Digipeater-Algorithm.pdf)
- [APRS Digipeaters - WB2OSZ](https://raw.githubusercontent.com/wb2osz/direwolf-doc/main/APRS-Digipeaters.pdf)
- [Understanding APRS Packets](https://github.com/wb2osz/aprsspec/blob/main/Understanding-APRS-Packets.pdf)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [New-N Paradigm / Fixing Digipeaters](https://www.aprs.org/fix14439.html)
- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
