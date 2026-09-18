---
title: Arduino TNC and Arduino TNC Plus
description: An open KISS TNC modem for APRS, developed from the original KI4MCW project through M1GEO experiments to Arduino TNC Plus by SQ9MDD and SQ5RWU.
template: doc
tableOfContents: true
---

Arduino TNC is an open **KISS TNC for AFSK 1200** whose history includes several stages of development and contributions from multiple amateur radio operators.

The project began with Robert Marshall **KI4MCW**, who created a software AFSK modem that did not require a dedicated modem IC.

Audio was sampled by the ADC of an AVR microcontroller, while AFSK demodulation and AX.25 decoding were performed in software.

Original KI4MCW project:

https://sites.google.com/site/ki4mcw/Home/arduino-tnc

Experiments and development by George Smart M1GEO:

https://www.george-smart.co.uk/arduino/arduino_tnc/

Polish hardware version:

https://hamspirit.pl/SQ9MDD/?p=337

Arduino TNC Plus:

https://hamspirit.pl/SQ9MDD/?p=541

Code developed by SQ9MDD and SQ5RWU:

https://github.com/SQ9MDD/arduino_tnc

## Project idea

The basic idea was simple: use a popular AVR microcontroller as an AFSK modem without a specialised modem chip.

Functional diagram:

```text
Radio
  |
audio + PTT
  |
Arduino
  |
AFSK / AX.25
  |
KISS
  |
computer / APRS application
```

Arduino handles:

- audio sampling,
- AFSK demodulation,
- AX.25 decoding,
- transmit signal generation,
- PTT control,
- KISS communication.

The external application handles higher-level APRS or Packet Radio functions.

## Origins: KI4MCW

Robert Marshall KI4MCW began work on Arduino TNC around 2010.

The project was an experimental software APRS modem aimed mainly at AVR-based Arduino platforms.

One of its key goals was to perform as much work as possible directly in microcontroller code.

Instead of using a device such as TCM3105 or MX614, received audio was fed directly to the processor ADC.

```text
Radio
  |
audio
  |
ADC
  |
demodulation algorithm
  |
AX.25
```

Later versions added, among other features:

- AFSK 1200 reception,
- KISS,
- DCD,
- automatic ADC bias correction,
- packet transmission,
- UART support.

The code was heavily optimised for the limited resources of AVR microcontrollers.

## Software demodulation

The most interesting aspect of the original project was the absence of a dedicated analog modem.

Arduino sampled the audio signal and detected the tones:

```text
1200 Hz
2200 Hz
```

used by classic Bell 202 AFSK in APRS.

This approach made it possible to build a complete modem with very few components.

At the same time it required carefully optimised software because the ATmega328P has limited processing resources.

## M1GEO experiments

George Smart M1GEO tested the KI4MCW project in detail and experimented with his own input circuitry.

His work included:

- audio input filtering,
- ADC bias stabilisation,
- reducing USB-related noise,
- analysing sampling frequency,
- testing different KI4MCW code versions.

M1GEO used version 0.14 and achieved good KISS TNC operation.

He also experimented with sending APRS data over Bluetooth to a phone and designed his own board.

This demonstrates an important feature of Arduino TNC: the project was open and simple enough to be adapted to different uses.

## Polish Arduino TNC version

Based on the earlier work, a hardware version was documented by SQ9MDD.

The goal was to create a simple and inexpensive TNC interface that could be built from commonly available components.

Typical configuration:

```text
Radio
  |
Arduino TNC
  |
USB / KISS
  |
Raspberry Pi / PC
  |
APRS software
```

The design was tested both with WA8LMF test material and on the live APRS channel.

It worked well as a small modem for Raspberry Pi, an iGate or a home station.

## Software development by SQ5RWU

Łukasz **SQ5RWU** made an important contribution to later versions of the code.

The development history includes:

- CRC checking of received frames,
- stability fixes,
- receive-path corrections,
- transmit improvements,
- code cleanup,
- fixes required for stable operation.

Version:

```text
0.15.3
```

contains a substantial set of SQ5RWU fixes and improvements.

It was followed by:

```text
0.15.4
```

marked as the stable release.

At startup the device reports:

```text
Arduino TNC v.0.15.4
```

## KISS

Arduino TNC operates as a classic **KISS TNC**.

A typical setup is:

```text
Radio
  |
Arduino TNC
  |
KISS
  |
APRSBox / Xastir / Linux AX.25 / other software
```

KISS is a simple standard interface between a TNC and an application.

This keeps the device independent of any one program.

## AFSK 1200

The primary mode is:

```text
AFSK 1200 Bd
1200 / 2200 Hz
Bell 202
```

the classic APRS modulation used on VHF.

Arduino generates the transmit audio in software and controls PTT.

## Arduino TNC Plus

The next stage of the project was **Arduino TNC Plus**, or ATNC+.

It was developed as an extension of the earlier design.

The basic functionality was retained, but the radio interface was expanded.

The most important change was the addition of:

```text
TCM3105
```

used as a signal regeneration stage according to a solution by Adam SP5RZP.

Functional diagram:

```text
Radio
  |
audio path
  |
TCM3105
  |
Arduino
  |
KISS
  |
computer
```

The goal was to improve the signal presented to the digital section and increase decoding reliability under real RF conditions.

## Hardware changes in ATNC+

In addition to TCM3105, other improvements were introduced.

These included:

- DC-blocking capacitors at transformer inputs,
- an improved audio path,
- support for controlling handheld radios,
- an additional configuration jumper,
- a 2.2 kΩ resistor for selected PTT control methods.

The ATNC+ board is larger than the original version because of the additional modem circuitry.

## Stable software for ATNC+

Testing of Arduino TNC Plus also showed the need for further software improvements.

A large part of this work was done by SQ5RWU.

The result was a stable software version used with ATNC+.

The code is available here:

https://github.com/SQ9MDD/arduino_tnc

This repository should be treated as a later, corrected development line, not as the original KI4MCW version.

## Communication parameters

In the stable Arduino TNC version, the serial port operates at:

```text
19200 bit/s
```

for the KISS interface.

## Audio isolation

The design uses audio isolation transformers.

Their purpose is to electrically isolate the radio from the digital section.

This helps reduce:

- ground loops,
- computer noise,
- audio-level problems,
- USB-related interference.

## PTT control

Arduino controls the transmitter automatically.

The PTT path uses isolation and a transistor.

```text
Arduino
  |
PTT
  |
isolation
  |
Radio
```

This allows safe operation with different types of radios.

## Applications

Arduino TNC and ATNC+ can be used as a modem for:

- APRS iGate,
- APRS home station,
- Packet Radio,
- Raspberry Pi,
- Linux computers,
- KISS-compatible applications,
- custom experimental projects.

A typical installation can look like:

```text
Radio
  |
Arduino TNC Plus
  |
USB
  |
Raspberry Pi
  |
APRSBox
```

## Educational value

One of the greatest values of Arduino TNC is that it allows the complete packet path to be studied.

The project shows in practice:

- how audio reaches the ADC,
- how AFSK tones are detected,
- how AX.25 frames are decoded,
- how CRC works,
- how KISS works,
- how the TX signal is generated,
- how PTT is controlled.

It is a very useful project for people interested in both APRS and AVR microcontrollers.

## Project history

The development can be summarised as:

```text
KI4MCW
  |
original software Arduino TNC
  |
M1GEO
  |
testing and radio-path development
  |
SQ9MDD
  |
Polish hardware version
  |
SQ5RWU
  |
code fixes and stabilisation
  |
Arduino TNC Plus
```

It is therefore not a single design by one author, but the result of successive experiments and improvements by several amateur radio operators.

## Who is this project for?

Arduino TNC may be interesting for people who:

- want to build their own KISS TNC,
- experiment with Arduino and AVR,
- want to learn AFSK 1200 in practice,
- need a simple modem for Raspberry Pi,
- are interested in AX.25,
- want to understand how a software modem works.

Arduino TNC Plus is additionally interesting for those who want to use the expanded radio path with signal regeneration.

## Documentation

Main sources:

- original KI4MCW Arduino TNC: https://sites.google.com/site/ki4mcw/Home/arduino-tnc
- M1GEO experiments: https://www.george-smart.co.uk/arduino/arduino_tnc/
- SQ9MDD Arduino TNC: https://hamspirit.pl/SQ9MDD/?p=337
- Arduino TNC Plus: https://hamspirit.pl/SQ9MDD/?p=541
- later SQ9MDD/SQ5RWU code: https://github.com/SQ9MDD/arduino_tnc
