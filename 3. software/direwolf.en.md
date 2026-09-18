---
title: Dire Wolf
description: Software AX.25 modem and TNC for APRS with AFSK, KISS, FX.25, digipeating and IGate support.
template: doc
tableOfContents: true
---

**Dire Wolf** is a software packet modem, TNC, and AX.25 encoder/decoder using a computer sound interface and digital signal processing.

It can receive and transmit AX.25 frames without requiring a traditional hardware TNC. It may work as a standalone application or as the radio layer for other APRS software.

Official project repository:

https://github.com/wb2osz/direwolf

## What Dire Wolf is used for

In its simplest form, Dire Wolf turns a computer with an audio interface into a packet radio modem.

```text
Radio <-> audio/PTT <-> Dire Wolf <-> KISS/AGW <-> APRS application
```

The program receives audio from the radio, demodulates the signal and decodes AX.25 frames. In the opposite direction it generates transmit audio and can control the radio PTT.

Dire Wolf can be used as:

- a software TNC,
- an AX.25 encoder and decoder,
- an APRS decoder,
- a KISS modem for other applications,
- an APRS digipeater,
- an IGate,
- a tracker and beacon generator,
- an APRStt gateway.

## High decoding performance

One of Dire Wolf's most important advantages is its very good AX.25 decoding performance.

It is not merely a software replacement for an old hardware TNC. Dire Wolf uses digital signal processing and several techniques intended to improve reception under real radio conditions.

Depending on configuration, it can use:

- different demodulator profiles,
- digital filtering,
- automatic mark/space amplitude balancing,
- multiple parallel demodulators,
- multiple decision points,
- parallel attempts to decode the same signal using different methods.

This is useful with weak signals, imperfect audio paths, different radio characteristics and HF operation.

Dire Wolf has also been compared with traditional TNCs using test material such as the WA8LMF TNC Test CD. In practice it can correctly decode some frames which simpler or older TNC solutions fail to decode.

This does not remove the importance of a good radio path. Correct audio levels, bandwidth, signal-to-noise ratio and receiver quality still matter.

## 1200 bit/s AFSK

The most common APRS mode on VHF and UHF is:

```text
MODEM 1200
```

Typical AFSK tones are 1200 Hz and 2200 Hz.

This is the standard mode for classic APRS on the 2 m band and normally works through ordinary radio audio connections.

## 300 bit/s AFSK on HF

Dire Wolf also supports 300 bit/s AFSK, used mainly for packet radio and APRS on HF SSB.

```text
MODEM 300
```

The default tones are 1600 Hz and 1800 Hz.

On HF, the ability to run several demodulators in parallel at slightly shifted frequencies is especially useful. It helps tolerate small tuning differences between stations and variations introduced by the SSB signal path.

Example of a more advanced configuration:

```text
MODEM 300 1600:1800 7@30 /4
```

The exact parameters should be adjusted for the radio path and the Dire Wolf version in use.

## 2400 and 4800 bit/s PSK

Dire Wolf also supports 2400 and 4800 bit/s PSK modes.

They are much less common than classic 1200 AFSK but can be used for more specialized packet radio applications.

## 9600 bit/s

Dire Wolf supports G3RUH-style 9600 bit/s operation.

It should not be treated as a simple faster equivalent of 1200 AFSK. Ordinary speaker output and microphone input usually do not provide a suitable signal path because of filtering, pre-emphasis and de-emphasis.

In practice a sufficiently wide and relatively direct radio data path is required, such as a 9600 packet, discriminator or direct-audio connection.

For a typical APRS station connected through normal radio audio, 1200 AFSK remains the most practical solution.

## FX.25

Dire Wolf also supports **FX.25**, an AX.25 extension using forward error correction.

FX.25 adds Reed-Solomon error-correction information to the transmission. A receiver supporting FX.25 can therefore recover a valid frame in some cases even when transmission errors occurred.

An important property of FX.25 is compatibility with ordinary AX.25.

FX.25 reception in Dire Wolf is enabled automatically. FX.25 transmission must be enabled explicitly.

Example:

```text
FX25TX 1
```

Depending on the version, a specific number of parity bytes such as 16, 32 or 64 can also be selected.

FX.25 increases the amount of data transmitted over the radio channel, so it is not always appropriate for a busy APRS frequency. It is nevertheless an interesting option for packet radio and operation under more difficult radio conditions.

## Very important: FIX_BITS

Dire Wolf includes a `FIX_BITS` mechanism which attempts to recover an AX.25 frame received with an invalid FCS.

The mechanism can alter bits in the received frame and test whether the resulting frame has a valid FCS.

This can be interesting for packet radio experiments, but it requires particular caution in APRS infrastructure.

A frame with an invalid FCS was received with an error. Attempting to repair it can result in modified data being passed onward, while downstream applications may no longer be able to distinguish it from a correctly received frame.

A changed bit may affect:

- the station callsign,
- SSID,
- path,
- position,
- message,
- telemetry,
- message identifier,
- any part of the APRS payload.

For typical APRS infrastructure, especially when Dire Wolf is used as the modem for an IGate or digipeater, it is advisable to disable this mechanism explicitly:

```text
FIX_BITS 0
```

This preserves a simple rule: only frames actually received with a valid FCS are forwarded.

### FIX_BITS and FX.25

`FIX_BITS` and FX.25 are different mechanisms.

FX.25 uses additional FEC information intentionally transmitted by the sending station. The receiver therefore has mathematical redundancy which can be used to correct a defined amount of corruption.

`FIX_BITS` instead searches for bit changes in an already corrupted ordinary AX.25 frame which would produce a valid FCS.

It is therefore possible to use:

```text
FIX_BITS 0
```

while still receiving and correcting FX.25 frames.

## KISS and other applications

Dire Wolf can operate as a virtual TNC and provide received frames to other applications.

It supports, among other interfaces:

- KISS over TCP,
- KISS over a serial port or pseudo-terminal,
- the AGW network interface.

A typical architecture can look like this:

```text
Radio
  |
  | audio + PTT
  v
Dire Wolf
  |
  | KISS TCP
  v
APRSBox / YAAC / Xastir / other application
```

Dire Wolf can therefore handle only the radio and AX.25 layer while APRS logic remains in another application.

The default KISS TCP port is usually:

```text
8001
```

## Digipeater

Dire Wolf includes APRS digipeating support.

It can receive frames from the radio channel, inspect their path and retransmit packets according to configured rules.

Before operating a digipeater, the operator should understand APRS path handling, especially `WIDE1-1`, `WIDE2-1` and the need to avoid unnecessary retransmissions.

A badly configured digipeater can significantly increase channel occupancy.

## IGate

Dire Wolf can also operate as a gateway between the APRS radio network and APRS-IS.

The simplest direction is:

```text
RF -> Dire Wolf -> APRS-IS
```

Dire Wolf also contains functions related to forwarding selected information from APRS-IS to RF.

APRS-IS -> RF operation requires particular care. Internet traffic must not be retransmitted onto the radio channel without appropriate restrictions.

## PTT control

Transmission normally requires control of the radio transmitter.

Dire Wolf supports several PTT methods, including:

- serial RTS and DTR,
- GPIO,
- interfaces based on CM108/CM119 devices,
- Hamlib,
- selected dedicated radio interfaces.

Example using RTS:

```text
PTT /dev/ttyUSB0 RTS
```

Depending on the interface, inverted polarity may be required.

## Audio interfaces and SDR

Dire Wolf uses standard operating-system audio devices.

It can work with:

- built-in sound cards,
- USB audio adapters,
- radio interfaces with integrated audio codecs,
- virtual audio devices,
- SDR receivers.

Example SDR receive chain:

```text
RTL-SDR
   |
   v
rtl_fm / other SDR software
   |
   v
Dire Wolf
   |
   v
KISS / APRS
```

This makes it possible to build a simple APRS receiver or RX-only IGate.

## Continuous operation

Dire Wolf is well suited for continuously running installations.

It can operate on small computers such as Raspberry Pi as a modem, TNC, IGate, digipeater or radio layer for another application.

This is a separate advantage from the high performance of its demodulators.

## Basic configuration example

A very simplified configuration for a typical 1200 bit/s APRS station can look like this:

```text
ADEVICE plughw:0,0

CHANNEL 0
MYCALL SQ9ABC

MODEM 1200

FIX_BITS 0
```

A real installation also requires configuration of the correct audio device, signal levels, PTT, KISS interfaces and station-specific functions.

It is usually best to configure and verify receive performance before enabling transmission.

## Diagnostics

Dire Wolf prints useful information to the terminal while starting and receiving packets.

It can show:

- received AX.25 frames,
- decoded APRS content,
- audio levels,
- receive channel information,
- demodulator information,
- diagnostic messages.

This also makes Dire Wolf useful when adjusting the audio path and troubleshooting receive problems.

## Documentation

Main sources:

- project repository: https://github.com/wb2osz/direwolf
- project documentation: https://github.com/wb2osz/direwolf/tree/master/doc
- additional documentation: https://github.com/wb2osz/direwolf-doc
- sample configuration: https://github.com/wb2osz/direwolf/blob/master/conf/generic.conf

When configuring a specific version of Dire Wolf, use documentation matching that version.
