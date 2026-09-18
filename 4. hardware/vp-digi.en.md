---
title: VP-Digi
description: A modern, low-power APRS digipeater controller and KISS modem with AX.25, FX.25 and multiple modem modes.
template: doc
tableOfContents: true
---

VP-Digi is a standalone AX.25 digipeater controller and KISS TNC modem intended, among other uses, for APRS.

The project was designed as a small, inexpensive and energy-efficient STM32-based device. It combines the functions of a hardware TNC, Packet Radio modem and advanced APRS digipeater.

Official project repository:

https://github.com/sq8vps/vp-digi

Documentation:

https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md

VP-Digi is an open-source project and continues to be developed. New versions bring fixes, modem improvements, FX.25 development and refinements to AX.25 and KISS handling.

## Why VP-Digi?

VP-Digi is an interesting alternative to traditional TNCs and larger computers used for APRS stations.

A typical installation can look like this:

```text
Radio
  |
audio + PTT
  |
VP-Digi
  |
KISS / USB / UART
  |
APRSBox / computer / other application
```

It can also operate completely independently as a digipeater:

```text
Radio
  |
audio + PTT
  |
VP-Digi
```

In this role it does not require a Raspberry Pi, PC or operating system.

## Modern architecture

VP-Digi is built around an **STM32F103** microcontroller.

This architecture allows it to handle digitally:

- modulation and demodulation,
- channel busy detection,
- AX.25 encoding and decoding,
- FX.25,
- duplicate filtering,
- digipeater functions,
- KISS TNC,
- beacon generation.

It is therefore not just a tracker or packet generator, but a complete radio-layer controller for Packet Radio and APRS.

## AX.25 and APRS compatibility

One of the most important VP-Digi features is proper **AX.25** support, the protocol underlying classic Packet Radio and APRS.

The device can operate as:

- AX.25 modem,
- KISS TNC,
- APRS digipeater,
- beacon generator,
- Packet Radio modem,
- radio interface for external software.

Thanks to KISS, it can work with many applications without relying on a proprietary vendor protocol.

Example:

```text
VP-Digi
   |
KISS
   |
APRSBox
```

or:

```text
VP-Digi
   |
KISS
   |
Packet Radio software
```

## Supported modems

VP-Digi supports several modem types.

### 1200 Bd Bell 202

The classic VHF APRS modem:

```text
AFSK 1200 Bd
1200 / 2200 Hz
```

This is the standard mode used by classic APRS on 2 m.

### 300 Bd Bell 103

A mode mainly intended for HF:

```text
AFSK 300 Bd
1600 / 1800 Hz
```

### 9600 Bd G3RUH

VP-Digi also supports:

```text
GFSK 9600 Bd
G3RUH
```

This mode requires a suitable radio path and usually flat audio access.

Not every radio can handle 9600 Bd through a normal microphone input and speaker output.

### 1200 Bd V.23

An alternative modem is also available:

```text
AFSK 1200 Bd
1300 / 2100 Hz
```

compatible with V.23.

## FX.25

VP-Digi also supports **FX.25**.

FX.25 extends AX.25 with Reed-Solomon forward error correction. It can improve the probability of successful packet reception in difficult RF conditions while remaining compatible with classic AX.25.

VP-Digi can:

- receive AX.25,
- receive FX.25,
- transmit AX.25,
- transmit FX.25.

FX.25 can be configured independently for receive and transmit.

## Channel busy detection

VP-Digi does not base DCD only on successfully decoded data.

Channel activity is detected from the presence of a valid modulating signal.

This allows the device to recognize a busy channel earlier and helps reduce collisions.

In practice, the connected radio should normally operate with its squelch open.

## KISS TNC

One of the key VP-Digi features is **KISS** mode.

In this setup VP-Digi handles the radio layer:

```text
AX.25
modulation
demodulation
PTT
DCD
```

while an external application handles higher-level logic.

This can be:

- APRSBox,
- a Packet Radio client,
- Winlink software,
- custom software,
- diagnostic tools.

This means VP-Digi is not limited to digipeater operation.

## USB and two UART ports

The device provides:

- USB,
- UART1,
- UART2.

Each port can operate independently as:

- KISS,
- frame monitor,
- configuration terminal.

This allows flexible installations, for example using one port for KISS and another for monitoring and diagnostics.

## APRS digipeater

VP-Digi includes an advanced digipeater engine.

It can configure, among other things:

- 4 New-N aliases such as `WIDEn-N`,
- 4 simple aliases,
- maximum hop count,
- trace mode,
- direct-only mode,
- viscous delay,
- packet filtering,
- duplicate buffer.

This gives much more control than a simple packet repeater.

## Viscous delay

VP-Digi supports **viscous delay**.

Instead of repeating a packet immediately, the digipeater can briefly wait and check whether another station has already repeated it.

If so, its own retransmission can be suppressed.

This helps reduce:

- unnecessary duplicates,
- channel occupancy,
- retransmissions.

It is especially useful where several digipeaters have overlapping coverage.

## Direct-only

Individual aliases can also use **direct-only** mode.

In this mode the digipeater repeats only packets heard directly from the source station.

This helps prevent unnecessary re-digipeating of packets that have already passed through other digipeaters.

## Station filtering

VP-Digi can filter packets by callsign.

Two modes are available:

- blacklist,
- whitelist.

This allows selected stations to be blocked or only selected callsigns to be accepted.

## Duplicate protection

The digipeater includes a buffer for detecting repeated packets.

This prevents the same frame from being retransmitted multiple times when received over different paths.

The buffer time is configurable.

## Beacons

VP-Digi supports up to **8 independent beacons**.

For each beacon you can configure:

- content,
- interval,
- delay,
- APRS path,
- enable or disable state.

Example:

```text
!5002.63N/02157.91E#VP-Digi
```

## Frame monitor

VP-Digi can also operate as an AX.25 traffic monitor.

Received packets can be viewed on a serial port, which is useful for:

- station commissioning,
- configuration checks,
- audio path diagnostics,
- Packet Radio and APRS traffic analysis.

## Low power consumption

One of the major VP-Digi advantages is its very low current consumption.

According to the project documentation, the device draws approximately:

```text
40-50 mA
```

This is especially useful in stations:

- powered from batteries,
- powered from solar systems,
- installed where mains power is unavailable,
- operating as autonomous digipeaters,
- running continuously.

In such installations the radio itself usually consumes much more energy than the controller.

## Open and actively developed

VP-Digi is an open-source project.

The repository provides:

- source code,
- schematic,
- documentation,
- ready-made firmware,
- change history.

The project continues to be developed, with new releases adding fixes and features.

For infrastructure intended to operate for a long time, that is an important advantage.

## Who is VP-Digi for?

VP-Digi is a good fit when you need:

- an autonomous APRS digipeater,
- an energy-efficient 24/7 node,
- a hardware KISS modem,
- a Packet Radio modem,
- a radio interface for APRSBox or other software,
- a platform for AX.25 and FX.25 experiments,
- a small field or solar-powered station.

## VP-Digi vs a software modem

VP-Digi and software solutions such as Dire Wolf represent two different approaches.

### VP-Digi

```text
microcontroller
low power consumption
no operating system
standalone digipeater
KISS
```

### Software modem

```text
computer
sound card
more processing power
greater DSP flexibility
easier integration with network services
```

For an autonomous installation VP-Digi can be simpler and more energy-efficient.

In a larger station it can work as a KISS modem for external software.

## Summary

VP-Digi is a good example of modern APRS hardware.

It combines:

- AX.25 compatibility,
- FX.25 support,
- several modem types,
- an advanced digipeater,
- KISS,
- USB and UART,
- very low power consumption,
- open-source firmware,
- active development.

It can therefore operate both as a standalone digipeater and as a modem within a larger APRS system.

It is particularly interesting where **protocol compliance, reliability, low power consumption and operation without a full computer** matter.

## Documentation

Main sources:

- project repository: https://github.com/sq8vps/vp-digi
- Polish documentation: https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md
- SQ8L project description: https://sq8l.pzk.pl/index.php/vp-digi-tani-i-funkcjonalny-sterownik-digipeatera-aprs-wraz-z-modemem-kiss/
- changelog: https://github.com/sq8vps/vp-digi/blob/main/CHANGELOG.md
