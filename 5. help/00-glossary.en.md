---
title: "Glossary of terms and abbreviations"
description: "Basic radio communication, APRS and packet transmission terminology explained for people getting started with APRS."
sidebar:
  order: 0
---

APRS documentation uses terminology from radio communication, computing and packet transmission. This glossary explains terms encountered when reading articles, configuring stations and examining traffic. Entries are grouped by subject. Abbreviations retain their original expansions, while definitions explain their practical meaning.

## 1. Radio communication basics

**Antenna** - a component that radiates and receives radio waves. Its design, location and radiation pattern affect communication performance.

**CTCSS (Continuous Tone-Coded Squelch System)** - selective squelch opening using a continuous low-frequency tone transmitted with the signal. It does not provide confidentiality.

**Frequency** - the number of wave cycles per second, expressed in hertz (Hz). In Poland, conventional APRS on the 2 m band usually operates at 144.800 MHz.

**DCS (Digital-Coded Squelch)** - selective squelch opening using a transmitted digital code.

**Duplex** - an operating method using separate paths for transmission and reception. Full duplex permits simultaneous transmission and reception; half duplex requires alternating between them.

**FM (Frequency Modulation)** - modulation in which information changes the instantaneous frequency of a carrier. Used in analog transceivers, including when carrying AFSK audio.

**Radio channel** - a particular frequency or set of radio operating parameters, such as frequency, emission type and additional settings.

**Modulation** - varying a selected parameter of a carrier signal to convey information.

**Transmitter power** - power delivered by the transmitter, usually stated in watts (W). Power alone does not determine a station's range.

**Radio repeater** - a station that receives and retransmits a signal to extend coverage. Voice repeaters often use separate receive and transmit frequencies.

**Propagation** - the way radio waves travel. Frequency, terrain, antennas and atmospheric conditions affect signal paths and range. Favorable propagation can make distant APRS stations audible.

**PTT (Push To Talk)** - a button or control signal that switches a transceiver to transmit. In a computer-controlled station, PTT may be operated through a hardware interface.

**RF (Radio Frequency)** - radio frequency. In APRS documentation, “RF network” usually means the radio portion of the system, as distinct from APRS-IS.

**RX (Receive)** - reception; a designation for a receiver, receive path or receiving operation.

**Simplex** - strictly speaking, one-way transmission. In amateur-radio practice, “simplex contact” also commonly means direct, alternating communication on the same frequency without a repeater.

**Squelch** - a circuit that mutes receiver audio unless the received signal meets configured conditions. Setting its threshold too high can impair packet reception.

**TX (Transmit)** - transmission; a designation for a transmitter, transmit path or sending operation.

**UHF (Ultra High Frequency)** - frequencies from 300 MHz to 3 GHz, including the amateur 70 cm band.

**VHF (Very High Frequency)** - frequencies from 30 to 300 MHz, including the amateur 2 m band.

**VOX (Voice Operated Exchange)** - a circuit that automatically starts transmission when a sufficient audio signal is detected. Its reaction time matters when transmitting data.

**Radio range** - the area in which a station can be received. It depends on antennas, power, terrain, interference and propagation conditions.

## 2. Equipment and interfaces

**CAT (Computer Aided Transceiver)** - computer control of a transceiver, such as changing frequency or reading settings. Available functions depend on the radio.

**DTR (Data Terminal Ready)** - a serial-interface control signal that can operate PTT through suitable circuitry.

**GNSS (Global Navigation Satellite System)** - a general term for satellite navigation systems, used among other things to determine an APRS tracker's position.

**GPS (Global Positioning System)** - one of the GNSS systems. In everyday usage, “GPS” is also applied to receivers that use several satellite navigation systems.

**Interface** - a connection method or circuit between cooperating devices. A radio interface may carry audio between a computer and transceiver and provide PTT control.

**Sound card** - a device converting signals between analog and digital forms. Together with a software modem, it enables AFSK transmission and reception.

**Modem (Modulator-Demodulator)** - hardware or software that converts data into a signal suitable for a transmission medium and back. In conventional APRS, an AFSK modem generates audio from data and decodes received audio.

**Serial port** - an interface that transmits data sequentially, one bit at a time. It can connect a TNC, GNSS receiver or control circuit.

**Transceiver** - a device combining a radio transmitter and receiver. Not every transceiver has a built-in modem or TNC.

**RTS (Request To Send)** - a serial-interface control signal often used to operate PTT through suitable circuitry.

**SDR (Software Defined Radio)** - a radio in which software-based signal processing implements some receiver or transmitter functions.

**Terminal** - software or hardware for exchanging data with another system. In packet radio, it may work with a TNC.

**TNC (Terminal Node Controller)** - a hardware or software packet communication controller. In a typical AX.25 setup, it handles frames and works with a modem. Not every modem is a complete TNC.

**UART (Universal Asynchronous Receiver-Transmitter)** - a circuit implementing asynchronous serial communication, commonly found in microcontrollers.

**USB (Universal Serial Bus)** - an interface used to connect sound cards, serial adapters, GNSS receivers and transceivers, among other devices.

## 3. Basic APRS terminology

**APRS (Automatic Packet Reporting System)** - a system for automatically exchanging information using packet transmission. It supports positions, messages, objects, weather and telemetry, among other data.

**Beacon** - an information packet usually transmitted automatically, for example with a station's position or status. It is not a separate APRS frame type.

**Bulletin** - an APRS announcement intended for multiple recipients, transmitted using the APRS message format.

**Comment** - additional text attached to certain APRS reports, such as position reports.

**Object** - named APRS information, usually describing a location or event and published by another station. It can represent a repeater or event location, for example.

**Item** - a simplified format for named APRS information, distinct from the object format.

**Packet** - a unit of data transmitted over a network. In APRS discussions, “packet” and “frame” are sometimes used interchangeably, although their precise meanings depend on the protocol layer.

**Position report** - APRS data containing geographic coordinates and, depending on the format, time, symbol, course, speed, altitude or comment.

**SSID (Secondary Station Identifier)** - an additional identifier in an AX.25 address, ranging from 0 to 15. It distinguishes stations sharing a callsign. Not every textual suffix seen in APRS-IS is an AX.25 SSID.

**APRS station** - a device or application exchanging APRS information, such as a tracker, home station, DIGI or IGate.

**Status** - textual information about a station's state or activity, transmitted in the designated APRS format.

**APRS symbol** - a graphical representation of a station or object, specified in APRS data and displayed on maps.

**Telemetry** - remotely transmitted measurements or device states, such as voltage, temperature or digital signals.

**Tracker** - a device or application that automatically publishes its position, usually determined by a GNSS receiver.

**APRS message** - a short text message transmitted in a defined APRS format. Addressed messages may use identifiers and acknowledgments.

**Callsign** - a radio station identifier assigned under applicable regulations; in amateur APRS, it is the basis for addressing.

## 4. APRS network components

**APRS-IS (APRS Internet System)** - internet infrastructure for exchanging APRS data among clients, IGates and servers.

**DIGI (Digipeater, Digital Repeater)** - a digital relay station that receives radio packets and retransmits them according to path rules and its configuration.

**Duplicate packet** - another copy of a packet already received. It may occur when multiple stations receive and forward the same transmission.

**Hop** - one forwarding step for a packet. In radio APRS, it usually means one digipeater retransmission.

**IGate (Internet Gateway)** - a gateway between the radio APRS network and APRS-IS. It forwards radio-received packets to the internet; a bidirectional gateway may also forward selected APRS-IS data to RF.

**APRS client** - an application or device that receives, displays or sends APRS data over a supported medium.

**Retransmission** - transmitting a packet again, for example by a digipeater, according to forwarding rules.

**APRS-IS server** - a server distributing APRS data over the internet, serving clients and, depending on its role, connecting to other servers.

**APRS path (Path)** - an address field identifying stations or aliases involved in forwarding a packet over radio.

**WIDE1-1** - a common path alias allowing one retransmission by a suitably configured digipeater.

**WIDE2-2** - a WIDEn-N alias whose initial counter allows two retransmission steps by compatible digipeaters. It does not guarantee that a packet will actually be repeated twice.

## 5. Data transmission and protocols

**AFSK (Audio Frequency-Shift Keying)** - representing data by changing the frequency of an audio signal. Conventional 1200-baud APRS uses Bell 202-compatible AFSK.

**ALOHA** - a shared-medium access method in which stations attempt transmission without centrally assigned time slots. Sharing the channel and having no delivery guarantee in radio APRS make collisions possible.

**AX.25** - an amateur-radio packet transmission protocol defining, among other things, addressing and frame structure. APRS primarily uses UI frames.

**Baud** - a modulation-rate unit representing symbols per second. It does not always equal the number of bits per second.

**Bit/s (bps)** - the number of bits transmitted per second.

**CRC (Cyclic Redundancy Check)** - a method of computing a check value to detect errors in transmitted data.

**DTI (Data Type Identifier)** - a data-type identifier, usually the first character of the APRS information field, indicating how to interpret its contents.

**FCS (Frame Check Sequence)** - a frame check sequence. In AX.25, it uses CRC to detect transmission errors.

**FEC (Forward Error Correction)** - correcting errors using additional data transmitted alongside the information, without requiring retransmission.

**FSK (Frequency-Shift Keying)** - modulation in which symbols are represented by different signal frequencies.

**FX.25** - an AX.25 extension adding FEC. It can recover some damaged transmissions when the receiver supports FX.25.

**KISS (Keep It Simple, Stupid)** - a simple protocol between an application and TNC for carrying frames and selected control commands. A KISS interface alone does not guarantee that a device fully supports AX.25.

**Mic-E** - a compact APRS format in which some position and status information is also encoded in the AX.25 address field.

**Payload** - user data carried at a particular protocol layer. In APRS descriptions, it often means the contents of a frame's information field.

**Frame** - a link-layer data unit. An AX.25 frame contains addresses, a control field, an information field and a check sequence, among other elements.

**TCP/IP** - a family of network protocols used, among other things, for communication between clients and APRS-IS.

**TOCALL** - the common name for the APRS destination-address field, whose values often identify the transmitting software or device. Not every destination-address value is a product identifier.

**UI Frame (Unnumbered Information Frame)** - an AX.25 frame carrying data without establishing a connection or acknowledging each frame at the link layer. It underpins conventional APRS.

## 6. Station operation and configuration

**APRS Passcode** - a code used in traditional APRS-IS login. It is calculated from the callsign and is not strong cryptographic security.

**DCD (Data Carrier Detect)** - a signal or mechanism detecting the presence of data transmission, used among other things to assess channel occupancy.

**APRS-IS filter** - a set of rules limiting the data delivered to a client, for example by location or callsign.

**Host** - a computer or device providing a service, such as a KISS TCP server.

**KISS Serial** - carrying KISS frames and commands over a serial interface.

**KISS TCP** - carrying KISS data over a TCP connection, allowing an application to communicate with a TNC across a computer network.

**TCP port** - a number identifying a TCP service on a device. The port number depends on the service configuration.

**Proportional Pathing** - a method of sending successive position reports using different paths, with longer retransmission paths used less often to reduce network load.

**q-construct** - a special element added to a packet's textual representation in APRS-IS. It conveys information about how the packet entered or was forwarded through the internet network. It is not an AX.25 radio retransmission path.

**SmartBeaconing** - a method of adapting position-report frequency to station movement, particularly speed and changes in direction.

**TX Delay** - time reserved for the transmitter and the receiving station's receive path to become ready before the actual frame data is sent. The precise meaning of the setting depends on the modem or TNC.

**TX Tail** - additional time for keeping transmission active after the actual data has ended, if supported by the modem or TNC.

## 7. Diagnostics and operation

**Buffer** - a memory area temporarily holding data before further processing or transmission.

**Duplicates** - multiple copies of the same information. Diagnostics must distinguish multiple receptions of one transmission from a packet transmitted again by the source station.

**Packet collision** - overlapping transmissions that prevent or hinder correct decoding.

**Queue** - a mechanism holding packets awaiting processing or transmission.

**Log** - a chronological record of events used to analyze station operation and diagnose problems.

**Packet monitor** - a tool displaying received or sent frames, their addresses, paths and contents.

**Latency** - the time between specified stages of packet processing or transmission.

**Audio level** - the signal level fed to a modem or transmitter. Too low or too high a level can cause decoding problems.

**Overdrive** - signal distortion caused by exceeding the permitted level in a processing path.

**RSSI (Received Signal Strength Indicator)** - an indicator of received radio signal strength. Its scale and measurement method depend on the device.

**SNR (Signal-to-Noise Ratio)** - the ratio of useful signal power to noise power, usually expressed in decibels.

**Channel occupancy** - the state in which an ongoing transmission is using the channel. A TNC may detect occupancy before transmitting.

## 8. Terms that should not be confused

**Modem and TNC** - a modem converts between signals and data. A TNC handles packet communication, such as AX.25 frames, and may contain or work with a modem.

**DIGI and IGate** - a DIGI retransmits packets over radio. An IGate connects the radio network to APRS-IS. One device can perform both functions, but they are separate roles.

**Baud and bit/s** - baud counts symbols per second; bit/s counts bits per second. With one bit per symbol, their numerical values can match.

**GPS and GNSS** - GPS is one of the GNSS systems. A multi-system receiver can also use other satellite constellations.

**SSID and textual suffix** - an AX.25 SSID ranges from 0 to 15. Other suffixes in textual internet identifiers do not thereby become AX.25 SSIDs and may not be suitable for forwarding to RF.

**APRS and APRS-IS** - APRS defines an information exchange system that can use different media. APRS-IS is its internet-based data distribution infrastructure.
