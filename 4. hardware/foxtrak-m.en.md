---
title: "FoxTrak-M"
slug: "foxtrak-m"
type: "hardware"
category: "tracker"
manufacturer: "Fox Delta"
status: "legacy"
protocols:
  - "APRS"
  - "AX.25"
  - "NMEA 0183"
tags:
  - "tracker"
  - "GPS"
  - "1200-baud"
  - "Bell-202"
  - "PIC"
source:
  - "https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf"
---

## 1. Basic information

**FoxTrak-M** is a compact APRS tracker module from Fox Delta. It was designed as a small module intended to be integrated into another device or project, rather than as a complete standalone unit in its own enclosure.

The design is based on **PIC16F84A or PIC16F628A** microcontrollers. The board measures about **7 × 4 cm** and uses 0.1-inch pin headers.

FoxTrak-M is a variant of the earlier FoxTrak tracker. The main design difference is the replacement of D-Sub connectors with pin headers, making the module easier to integrate into other equipment.

## 2. Intended use

FoxTrak-M is intended primarily for:

- automatic transmission of station position via APRS,
- building a mobile GPS tracker,
- integrating an APRS tracker as a module in a custom device,
- operation with the FoxView-2 terminal,
- experiments with custom firmware for the PIC microcontroller.

The device is a transmit-only tracker. It is not a full APRS terminal and does not decode received APRS frames.

## 3. How it works

The tracker receives GPS position data in **NMEA** format, in particular sentences such as:

```text
$GPRMC
```

Based on this data, it generates a **1200 bps** packet radio signal that can be fed directly into the transmitter microphone input.

No external TNC is required for basic operation.

Automatic beacons are transmitted only when the device receives valid position data from the GPS.

## 4. APRS and supported features

FoxTrak-M performs the basic APRS tracker function, namely periodic transmission of position reports.

The DK7IN firmware supports, among other things:

- fixed beacon interval,
- beacon interval changes depending on speed,
- an additional beacon after a significant change of direction,
- configurable TX Delay,
- configuration of callsign and other APRS parameters,
- generation of test tones for audio path adjustment.

The device does not receive or interpret APRS traffic from the radio channel.

## 5. Smart Beaconing

FoxTrak-M implements an early form of adaptive beaconing. The position transmission rate can change depending on speed.

Two parameter sets are available and can be selected with a switch.

| Speed | Faster profile | Slower profile |
|---|---:|---:|
| < 4 km/h | 25 min | 30 min |
| > 4 km/h | 4 min | 5 min |
| > 11 km/h | 120 s | 160 s |
| > 24 km/h | 60 s | 80 s |
| > 50 km/h | 30 s | 40 s |
| > 100 km/h | 20 s | 30 s |
| > 150 km/h | 10 s | 20 s |

The maximum speed since the previous beacon was sent is taken into account.

If speed exceeds **15 km/h**, the device can also send a beacon after a significant change in direction:

- more than **40°** in the faster profile,
- more than **60°** in the slower profile.

If the previous transmission occurred less than 10 seconds earlier, the next beacon will be delayed.

## 6. GPS interface and configuration

The same serial interface is used for:

- receiving data from the GPS receiver,
- configuring parameters stored in PIC memory.

The documentation describes configuration utilities for:

- DOS,
- Windows,
- Linux.

It was also possible to use the TinyTrak configuration program.

An example firmware version identified itself as:

```text
DK7IN V.1.6
```

The fixed beacon interval can be set in 10-second steps, while TX Delay can be adjusted with a resolution of about 6.6 ms.

## 7. Radio path

FoxTrak-M generates AFSK modulation compatible with the **Bell 202** system used in packet radio.

The nominal tone frequencies are:

- **1200 Hz**
- **2200 Hz**

For adjustment and diagnostics, the device can generate test tones.

Example terminal commands:

```text
ESC T 0
```

generates a 1200 Hz tone.

```text
ESC T 1
```

generates a 2200 Hz tone.

```text
ESC T M
```

generates a mixed 1200/2200 Hz signal.

The configuration communication described in the manual uses:

```text
4800 baud, 8N1
```

## 8. Radio connection

The radio connector provides, among other signals:

| Signal | Function |
|---|---|
| Audio In | audio z odbiornika do trackera |
| CD | Carrier Detect, jeśli dostępny |
| GND | masa |
| SW1 | wejście przycisku natychmiastowej transmisji |
| Audio Out | audio trackera do wejścia mikrofonowego radia |
| +5V | zasilanie modułu |

The module provides audio level adjustment to and from the radio.

There is also an option to adapt PTT control for some handheld radios by adding a 2.2 kΩ resistor.

## 9. GPS / PC connector

The second 8-pin 0.1-inch connector is used for communication with the GPS or a computer.

The documentation lists the following signals:

| Signal | Function |
|---|---|
| GND | masa |
| S0 | dane szeregowe OUT |
| SI | dane szeregowe IN |
| PI | PTT IN |
| PO | PTT OUT |
| A | niewykorzystywane w FoxTrak-M |
| B | niewykorzystywane w FoxTrak-M |

## 10. Indicators and controls

FoxTrak-M has LEDs indicating device status.

**CD**

Lights when transmission from another station is detected.

**GPS**

Lights when valid GPS position data is available. It flashes when GPS data is being received but the position is not valid.

**PTT**

Lights while the transmitter is keyed.

During startup, the LEDs perform an initialization sequence after which a beacon may be transmitted.

## 11. Power supply

FoxTrak-M requires an external power supply:

```text
+5 V DC
```

The module does not have its own 5 V regulator.

It also does not provide power for the GPS receiver, so the GPS must have its own power source.

## 12. Firmware

The main firmware described in the documentation was developed by **DK7IN**.

The hardware design allows the firmware to be replaced and, if needed, the PIC16F84A to be substituted with a PIC16F628A.

FoxTrak-M was therefore designed not only as a ready-made tracker, but also as a platform for experiments with custom APRS software.

## 13. Limitations

The main limitations stated in the documentation are:

- no decoding of received APRS frames,
- no full TNC functionality,
- the device focuses on GPS position transmission,
- external 5 V power supply required,
- the GPS is not powered by the module,
- the design is based on older PIC microcontrollers.

## 14. Applications

Typical FoxTrak-M applications:

- vehicle APRS tracker,
- tracker embedded in another device,
- mobile GPS/APRS station,
- experimental APRS platform,
- educational project related to AFSK, AX.25 and APRS.

## 15. Classification

| Feature | Value |
|---|---|
| Type | hardware |
| Class | APRS tracker |
| Direction | TX |
| Position source | GPS |
| GPS data | NMEA, GPRMC |
| Radio layer | AFSK 1200 bps |
| Modulation | Bell 202 |
| Microcontroller | PIC16F84A / PIC16F628A |
| Power | 5 V DC |
| Radio interface | audio + PTT |
| APRS reception | no |
| Smart Beaconing | yes |
| PC configuration | yes |

## 16. Historical significance

FoxTrak-M represents the class of simple hardware APRS trackers that made it possible to build a mobile position-reporting station without a full TNC and computer.

Its design clearly illustrates the classic APRS tracker model:

```text
GPS
  ↓
NMEA
  ↓
mikrokontroler
  ↓
APRS / AX.25
  ↓
AFSK 1200 bps
  ↓
radio
```

## 17. Sources

This description is based on the manufacturer documentation:

**Fox Delta, FD - FoxTrak-M, Technical information: Compact PIC 16F84A/628A APRS Tracker Module**

https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf

The document is dated 8 June 2008.
