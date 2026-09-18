---
title: dsTNC and dsDIGI
description: A hardware KISS TNC modem and APRS digipeater based on a dsPIC DSP processor, with a highly effective AFSK 1200 demodulator.
template: doc
tableOfContents: true
---

dsTNC is a hardware **AFSK 1200 modem and KISS TNC controller** designed by Tomek SP9UOB.

The same hardware can also run the **dsDIGI** firmware, turning it into a standalone APRS digipeater.

dsTNC project page:

https://sp9uob.verox.pl/dstnc.html

dsDIGI description:

https://sp9uob.verox.pl/dsdigi.html

The project is an interesting example of digital signal processing used in a hardware APRS modem.

Instead of relying on a classic analog modem IC, AFSK decoding is handled by a **dsPIC30F4013** processor.

## Two uses for the same hardware

The dsTNC hardware can operate in two main roles.

### KISS TNC

```text
Radio
  |
audio + PTT
  |
dsTNC
  |
KISS
  |
computer / APRS application
```

In this mode the device handles the AFSK modem and AX.25 layer, while external software processes the packets further.

### Standalone digipeater

With dsDIGI firmware installed, the unit can work without a computer:

```text
Radio
  |
audio + PTT
  |
dsDIGI
  |
APRS digipeater
```

The same board can therefore work either as a hardware modem or as a standalone APRS node.

## DSP instead of a classic analog modem

One of the most interesting aspects of dsTNC is its demodulation method.

The design uses **digital signal processing, DSP**.

Audio from the receiver passes through a low-pass filter and into the processor's ADC.

Further processing is performed in software.

Simplified receive path:

```text
Radio
  |
audio
  |
filter
  |
ADC
  |
DSP
  |
AX.25
```

This approach makes it possible to use more advanced demodulation algorithms without a traditional modem IC.

## Highly effective AFSK 1200 demodulator

This is one of the strongest parts of the design.

The author used an algorithm inspired by a solution described by Thomas Sailer HB9JNX.

During tests using the well-known **WA8LMF APRS Test CD**, dsTNC correctly decoded on track two:

```text
900 frames
```

In the same test, a classic modem based on the **TCM3105** decoded:

```text
747 frames
```

The result demonstrates the effectiveness of the DSP demodulator, especially with noisy and distorted signals.

## AFSK 1200

The primary operating mode of dsTNC is classic APRS:

```text
AFSK 1200 Bd
1200 Hz / 2200 Hz
Bell 202
```

This is the standard modulation used by classic APRS on the 2 m band.

Received audio is sampled by the processor ADC, while the transmitted signal is generated digitally using PWM and then filtered before being fed to the transmitter.

## KISS TNC

The dsTNC firmware implements **KISS TNC** operation.

The serial port can run at:

```text
57600 bit/s
```

This allows the device to work with, among other things:

- APRS applications,
- Xastir,
- UI-View,
- the Linux AX.25 stack,
- other software supporting KISS.

KISS provides a simple and widely used interface between a modem/TNC and an application.

This keeps dsTNC independent of any single program.

## dsDIGI

An alternative firmware for the same hardware is **dsDIGI**.

With this firmware the device can operate as a standalone APRS digipeater.

Basic configuration includes:

- station callsign,
- digipeater alias,
- position,
- altitude,
- APRS symbol,
- beacon,
- beacon interval,
- beacon path,
- TXDELAY,
- TXTAIL,
- serial port speed.

Configuration is performed through a simple text terminal.

## WIDE1-1

dsDIGI was designed mainly to operate as a local digipeater using:

```text
WIDE1-1
```

The alias can be changed, and the implementation supports one `WIDEn-N` style alias.

This provides a simple and clear configuration for a local digipeater.

## Station beacon

dsDIGI can automatically generate its own position beacon.

Configurable parameters include:

- callsign,
- latitude,
- longitude,
- altitude,
- symbol,
- comment,
- path,
- interval.

The position is automatically added to the beacon content.

This allows the digipeater to identify itself correctly on the APRS network.

## Telemetry

An interesting dsDIGI feature is APRS telemetry generation.

The device can report:

- supply voltage,
- temperature when a **DS18B20** sensor is connected,
- number of received packets,
- number of transmitted packets.

This allows the basic state of the installation to be observed remotely.

For a digipeater installed at a difficult-to-access site, this can be very useful.

## Diagnostic information

dsDIGI is not limited to packet retransmission.

The terminal can display information about received frames, including:

- source,
- destination,
- path,
- packet content,
- decoded position,
- distance from the digipeater,
- bearing to the received station.

For example:

```text
Dist = 56 km
bearing = 78 degrees
```

This provides a convenient way to observe local traffic without additional APRS software.

## Digipeater status

dsDIGI also generates its own status information.

It can report, among other things:

- uptime,
- number of received frames,
- number of repeated packets.

This provides a simple indication of device activity.

## Transmit path adjustment

The firmware can generate test tones:

```text
1200 Hz
2200 Hz
```

They can be used to set the transmit audio level and deviation.

This is a practical feature when commissioning a hardware modem.

## Simple hardware

One advantage of dsTNC is its relatively small component count.

Most modem functions are implemented in software by the dsPIC processor.

According to the author, the circuit does not require special alignment.

After programming the processor, the main commissioning step is correct adjustment of the audio signal level.

## DSP-based hardware design

dsTNC and dsDIGI are an interesting example of APRS hardware built around DSP.

The project combines:

- digital AFSK demodulation,
- KISS,
- standalone digipeater operation,
- APRS telemetry,
- analysis of received station positions,
- simple terminal diagnostics.

The dsDIGI v1.25 firmware available on the project page is dated:

```text
24 November 2013
```

The project remains technically interesting and demonstrates a practical use of dsPIC as an APRS modem and controller.

## dsTNC and dsDIGI roles

The difference is easiest to remember like this.

### dsTNC

```text
radio
  |
AFSK modem
  |
KISS
  |
external application
```

Primarily a hardware modem and TNC.

### dsDIGI

```text
radio
  |
AFSK modem
  |
digipeater logic
```

A standalone digipeater using the same hardware.

The installed firmware determines the device role.

## Who is this project for?

dsTNC and dsDIGI may be interesting for people who:

- want to run a hardware KISS TNC,
- need a simple standalone digipeater,
- experiment with AX.25 and AFSK 1200,
- are interested in DSP demodulation,
- want to study a hardware implementation of an APRS modem,
- already own a dsTNC or dsDIGI device.

A particularly interesting part of the project is the **effective DSP demodulator used in dsTNC**.

## Documentation

Main sources:

- dsTNC: https://sp9uob.verox.pl/dstnc.html
- dsDIGI: https://sp9uob.verox.pl/dsdigi.html

The project site also provides:

- schematic,
- bill of materials,
- assembly drawing,
- PCB layout,
- firmware,
- change history.
