---
title: Mobile APRS Station
description: How to choose and install equipment for a mobile APRS station.
template: doc
tableOfContents: true
---

A mobile APRS station reports a vehicle's position while travelling and can receive APRS stations and messages. It can be a simple tracker that only transmits a position, or a complete radio terminal with a display.

Well-chosen, properly installed equipment matters most. Packet, path, and beaconing configuration depends on the local network; those topics are covered in the protocol section.

In Poland, the primary APRS channel on 2 m is **144.800 MHz FM**.

## What does a station consist of?

The simplest setup has four elements:

1. **Radio or transmitter**
2. **APRS tracker or TNC**
3. **GPS receiver**
4. **Antenna and power supply**

It may look like this:

```text
GPS
 |
Tracker / TNC
 |             \
 |              \ audio + PTT
Radio 144.800 MHz
 |
Vehicle antenna
```

Many current APRS transceivers combine a tracker, TNC, and GPS in one enclosure. In that case, the radio, antenna, and power supply are enough for a complete station.

## Three basic approaches

### Tracker and an ordinary radio

This is the simplest configuration when the station is primarily intended to transmit a position. The tracker receives GPS data, creates an APRS packet, sends audio to the radio's microphone input, and keys PTT.

```text
GPS → tracker → radio → antenna
```

Such a setup is compact, consumes little power, and can operate without a computer. Its usual limitation is the lack of convenient reception and APRS messaging.

### Radio with built-in APRS

A transceiver with its own TNC and GPS is the most convenient choice for someone who wants to transmit a position and use APRS without an additional computer. In addition to its own position, it can display received mobile and fixed stations, their distance, direction, and last received data.

In practice, such a transceiver makes it easier to establish contacts. You can notice a station travelling nearby, check its callsign and direction, then call it by voice on a frequency included in an APRS packet or object information. APRS objects can be used in the same way, for example for details of a local repeater, its frequency, or access tone.

The built-in APRS terminal will usually also let you:

- receive and browse APRS messages,
- send a short message to a selected station,
- receive a local APRS bulletin,
- browse received stations and objects,
- view basic position data without starting a phone.

The available features and how data is presented depend on the model. A radio display does not replace a full map, but it is very convenient for quickly checking who is active nearby and exchanging short messages while stopped.

Before buying, check whether the radio has:

- built-in GPS or an input for an external receiver,
- a 1200-baud AFSK modem and TNC,
- packet-data support on the 2 m band,
- a data connector or support for an external TNC,
- an interface that remains readable and safe to use in a vehicle.

Examples of APRS-capable transceivers include the handheld **Kenwood TH-D75E/D75A** and the mobile **Kenwood TM-D710G**, **Yaesu FTM-300DE/DR**, and **Yaesu FTM-500DE/DR**. These are examples of device classes, not a shopping list: some models may have been discontinued, and availability and band variants depend on the market.

### Radio, TNC, and an application

An ordinary radio can also be connected to an external TNC, tablet, phone, or small computer. The application serves as the APRS terminal, while the TNC converts digital data to AFSK audio and controls PTT.

```text
GPS / phone
      |
APRS application
      |
     TNC
      |
Radio → antenna
```

This solution provides essentially the same basic APRS functions as a radio with a built-in terminal: position, station list, messages, bulletins, and objects. A larger screen also adds a map, making it possible to immediately see the position of your own station, other users, repeaters, and other APRS objects.

It offers considerable flexibility, but also more cables, connections, and potential points of failure. It works best when a map, larger screen, or more convenient message typing while stopped is needed.

## Radio

Mobile APRS requires an FM radio operating on 2 m. It does not need factory APRS support if it works with a tracker or TNC.

When choosing a radio, pay particular attention to:

- access to microphone input, audio output, and PTT,
- a DATA, PACKET, or accessory connector, which simplifies connecting a tracker,
- the ability to select appropriate transmit power,
- stable 12 V power,
- convenient mounting and operation without taking attention from the road.

Permanently installed mobile radios are usually most practical, but a handheld transceiver can also be used with a simple tracker. In that case, pay particular attention to the cable, accessory-connector adapter, and radio cooling during transmission.

## Tracker and TNC

A **tracker** is dedicated to automatically transmitting a position. It usually has a GPS input, audio output, PTT control, and configuration memory. Not every tracker receives and decodes APRS traffic; before buying, clearly check whether it is transmit-only or also a receiver.

A **TNC** is a packet-radio modem. It can work with an APRS application or with a radio and computer. Besides transmitting, it will usually receive and decode packets, but the feature set depends on the particular model and software.

In mobile equipment you will most commonly find:

- a hardware tracker with GPS or an NMEA input,
- an external TNC connected through USB, Bluetooth, or Wi-Fi,
- a TNC built into the radio,
- a software modem running on a phone or computer with an audio interface.

On 2 m, APRS normally uses 1200-baud AFSK. An external tracker or TNC must therefore support this mode and the audio levels used by the radio.

## GPS

GPS may be built into the radio or tracker, connected as a separate receiver, or provided by a phone.

The most reliable arrangement is a receiver that sends current position data directly to the APRS device. Older trackers often use NMEA communication over a serial port. Before installation, check the connector, supply voltage, and data-format compatibility.

The receiver should have as clear a view of the sky as possible. In a car, place it under the windscreen or use a GPS antenna in the location recommended by its manufacturer. Do not cover it with metal parts or hide it deep under the dashboard.

## Antenna

For mobile APRS, the antenna is often more important than high transmitter power. A well-installed 2 m antenna improves both transmit range and reception of stations and digipeaters.

The usual best location is the roof of the car. The metal body then provides a ground plane for the antenna. An antenna on a trunk lid, bracket, or roof rail may work correctly, but its pattern and range may differ.

When installing it, pay attention to:

- stable mounting resistant to vibration and weather,
- undamaged coaxial cable,
- a weatherproof connector outside the vehicle,
- routing the cable without pinching it in a door,
- spacing from other antennas and metal obstructions.

Before a longer journey, it is worth checking SWR. A high SWR can indicate an antenna, cable, or connector problem and can place unnecessary stress on the transmitter.

## Connecting to the radio

A tracker or TNC must be connected to the radio with three basic signals:

| Signal | Function |
|---|---|
| Audio to radio | Sends AFSK modulation to the microphone or DATA input |
| Audio from radio | Sends received audio to the TNC if the station is to receive |
| PTT | Keys the radio transmitter |

Many radios provide a dedicated DATA or PACKET connector. It is usually a better choice than the microphone connector because it provides more suitable audio levels and avoids some additional settings.

Audio connections require level adjustment. Too low a level may prevent decoding or packet reception; too high a level will distort the modulation. Verify the setting during actual reception and a test transmission, following the equipment manual.

## Power and installation in a vehicle

A mobile radio is best powered from the 12 V installation through appropriately sized cables and a fuse placed close to the power source. A radio draws much more current while transmitting than while receiving, so the power lead and accessory socket must be rated for that demand.

A tracker, TNC, and GPS may require 5 V, USB, or a separate voltage regulator. Do not assume that every connector on a radio can power an additional device; check its available voltage and current in the documentation.

Mount equipment and cables securely and outside airbag deployment zones. The screen, microphone, and controls must not restrict visibility or make driving difficult.

## Before the first trip

Before permanent installation, run the setup on a bench or while parked. Check, in order:

1. that the radio is on the correct APRS channel,
2. that GPS supplies a current position,
3. that the tracker or TNC keys PTT correctly,
4. that the audio level does not overdrive the transmitter,
5. that the antenna and cables are in good condition,
6. that equipment remains stable after starting the engine.

The callsign, symbol, beaconing, and path settings depend on APRS rules and local coverage. Configure them according to the protocol-section articles, then check after the first trips that received positions match the actual route.

## Where is the best place to start?

The simplest route to mobile APRS is:

```text
1. A 2 m antenna
2. A radio on 144.800 MHz
3. A tracker with GPS or a radio with built-in APRS
4. Reliable power and mounting
5. An audio, PTT, and reception test
6. APRS configuration following local network practice
```

Only after a successful test is it worth extending the station with a map display, APRS messaging, an additional TNC, or phone integration.
