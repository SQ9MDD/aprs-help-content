---
title: "Mic-E: history, applications and data format"
description: "APRS position encoding in the AX.25 destination address and information field, Mic-E history, extensions and decoding rules."
---


**Mic-E (Microphone Encoder)** is a format for compact APRS position reports. Its defining feature is that it uses not only the AX.25 information field but also the destination address. The six characters of that address carry the encoded latitude and additional information; the information field completes the report with longitude, course, speed and symbol. Mic-E is neither a separate link-layer protocol nor a variant of the Base-91 *Compressed Position* format.

The format carries substantial information in a short frame. According to the *APRS Protocol Reference*, the minimum complete Mic-E frame, with no digipeater addresses or optional data, occupies 25 bytes, excluding the AX.25 FCS and flags. This figure refers to the entire frame, not just its information field.

## History

Mic-E was developed by Bob Bruninga, WB4APR, and N3MIM in 1994. Initially it was a hardware microphone encoder that made it possible to transmit APRS data using a radio transceiver. The prototype was followed by the TAPR Mic-E kit; the format was also used in PIC Encoder devices.

While the formal APRS specification was being prepared, the Kenwood TH-D7 and TM-D700 radios were already under development. Device-type identifiers were therefore provided in the optional text field: `>` for the TH-D7 and `]` for the TM-D700. APRS 1.0, published in 2000, documented position encoding, Mic-E message codes, telemetry and status text.

As further devices appeared, the original identification scheme proved insufficient. The TM-D710 retained the family identifier `]`, with a final `=` added to distinguish the model. In 2007–2008 identification was extended with categories for other devices: `` ` `` denoted a message-capable device and `'` a one-way tracker. A final manufacturer-and-version pair (`Mv`) was subsequently introduced. Additional information was placed in the text without changing the underlying position encoding, preserving compatibility with older receivers.

*Mic-E TYPE CODES* documents further identifiers and devices, with updates through 4 June 2019. It is a historical list, not a guarantee of complete identification of every later model.

## Applications

Mic-E has been used in microphone encoders, standalone trackers and APRS radios. Its compact reports suit mobile stations that regularly transmit position, course and speed. A Mic-E message code can additionally convey one of the predefined states or a custom state.

The receiver, digipeater and IGate still handle an AX.25 frame. APRS software must, however, recognize that the apparent destination address carries data rather than an ordinary TOCALL application identifier. That address must therefore not be interpreted like the destination of a conventional APRS frame.

## Data layout within the frame

Mic-E uses a standard AX.25 UI frame. The data is divided as follows:

| Field | Mic-E information |
| --- | --- |
| Destination address, characters 1–3 | First three latitude digits and message bits A/B/C |
| Destination address, character 4 | Fourth latitude digit and N/S indicator |
| Destination address, character 5 | Fifth latitude digit and longitude offset |
| Destination address, character 6 | Sixth latitude digit and E/W indicator |
| Destination-address SSID | Historical APRS path code |
| Information field, byte 1 | Mic-E data type identifier (DTI) |
| Information field, bytes 2–4 | Longitude |
| Information field, bytes 5–7 | Speed and course |
| Information field, bytes 8–9 | Symbol code and symbol-table identifier |
| Subsequent bytes | Optional telemetry or status text with extensions |

The six destination-address characters are encoded as valid AX.25 address characters. This does not mean that they represent the destination station's callsign. The source address remains the transmitting station's identifier.

## Latitude in the destination address

The six latitude digits use the `DDMM.hh` notation: two degree digits, two minute digits and two hundredths-of-a-minute digits. Each address character encodes one digit and, depending on its position, additional information.

| Character range | Latitude digit | Additional information |
| --- | --- | --- |
| `0`–`9` | `0`–`9` | Bit `0`; at positions 4–6: S, offset `+0`, E, respectively |
| `A`–`J` | `0`–`9` | Custom-message bit `1` at positions 1–3 |
| `P`–`Y` | `0`–`9` | Standard-message bit `1`; at positions 4–6: N, offset `+100`, W |
| `K`, `L`, `Z` | No digit | Special values related to position ambiguity |

Characters `A`–`K` are not used at positions 4–6. The precise meaning of special characters depends on their position. For example, destination address `S32U6T` encodes latitude **33°25.64′ N**, message bits `100` (standard *Returning* state), longitude offset `+0` and the western hemisphere. This example comes directly from chapter 10 of the specification.

### Mic-E message code

The first three address characters contain bits A, B and C. Nonzero combinations can convey a standard or custom message. All three bits set to zero indicate *Emergency*.

| A | B | C | Standard | Custom |
| --- | --- | --- | --- | --- |
| 1 | 1 | 1 | M0: Off Duty | C0: Custom-0 |
| 1 | 1 | 0 | M1: En Route | C1: Custom-1 |
| 1 | 0 | 1 | M2: In Service | C2: Custom-2 |
| 1 | 0 | 0 | M3: Returning | C3: Custom-3 |
| 0 | 1 | 1 | M4: Committed | C4: Custom-4 |
| 0 | 1 | 0 | M5: Special | C5: Custom-5 |
| 0 | 0 | 1 | M6: Priority | C6: Custom-6 |
| 0 | 0 | 0 | Emergency | Emergency |

For standard messages, all set bits must use standard encoding; for custom messages, all must use custom encoding. Mixing the two encodings for set bits produces an unknown message type. Custom-message support is optional and was absent from the original Mic-E units. A Mic-E message code is not an APRS text message with an acknowledgement mechanism.

### Destination-address SSID

The original specification provides for encoding a conventional path or one of the historical generic APRS paths in the destination-address SSID. This field must not be confused with an application identifier, and historical path encoding must not be treated as a recommendation for configuring today's network. The actual digipeater-address list, when present, remains a separate AX.25 frame field.

## Information field

The basic Mic-E information field comprises nine bytes:

```text
DTI | d+28 | m+28 | h+28 | SP+28 | DC+28 | SE+28 | SYMBOL | TABLE
 1  |   2  |   3  |   4  |   5   |   6   |   7   |    8   |   9
```

Subsequent bytes are optional. Not every byte in the basic section is necessarily printable ASCII. If transmission or text conversion removes control characters, the remaining bytes shift position and decoding becomes incorrect. The specification requires discarding a packet whose information field is shorter than nine bytes.

### DTI and a historical incompatibility

Originally, `` ` `` indicated a current GPS position and `'` an old position. The Kenwood TM-D700, however, used the apostrophe for a current position. The specification records this incompatibility and suggests identifying the TM-D700 by the `]` identifier at the start of the status text. Early beta units also used `0x1c` and `0x1d`.

The **first information-field byte (DTI)** must not be confused with the **device identifier**, which can occur only after the nine-byte basic section. Both locations may contain `` ` `` or `'`, but their meanings differ.

### Longitude

Longitude is carried in the `d+28`, `m+28` and `h+28` bytes. The E/W hemisphere and `+0` or `+100` offset must be read from the destination address.

Decode degrees:

1. `d = byte_2_value - 28`.
2. If the address specifies offset `+100`, add `100`.
3. If the result is within `180–189`, subtract `80`.
4. If the result is within `190–199`, subtract `190`.

These unusual corrections are required by the encoding of the `0–9` and `100–109` degree ranges. A valid degrees byte has a decimal value of `38–127`.

Decode minutes:

1. `m = byte_3_value - 28`.
2. If `m >= 60`, subtract `60`.

Decode hundredths of a minute: `h = byte_4_value - 28`. The result represents hundredths of a minute. Preserve the original byte values and validate the decoded ranges.

### Speed and course

The next three bytes encode speed in knots and course in degrees. The specification provides for speeds of `0–799` knots and courses of `0–360°`; `0` represents an unknown or indefinite course, while `360` represents due north.

The decoder reconstructs these values after subtracting `28` from each byte:

```text
sp = byte_5 - 28
dc = byte_6 - 28
se = byte_7 - 28

speed  = sp * 10 + floor(dc / 10)
course = (dc % 10) * 100 + se

if speed >= 800: speed -= 800
if course >= 400: course -= 400
```

In practice, account for two historical encoding schemes for some `SP+28` and `DC+28` values, documented in the specification's tables. The decoding algorithm above handles both through its final corrections. Values outside the permitted ranges must be rejected or marked invalid rather than automatically displayed as a valid position.

The specification gives an example of **86 knots at a course of 194°**: `SP+28` can be `t` or `$`, `DC+28` can be `]` or `Y`, and `SE+28` is `z`.

### Symbol and symbol table

Byte eight is the symbol code; byte nine is the symbol-table or overlay identifier. Their order is the reverse of the commonly used textual APRS symbol notation, which places the table before the symbol code. The document on Mic-E's evolution links this ordering to the later addition of the alternate symbol table to the original format.

## Position ambiguity

Mic-E supports deliberately reducing the precision of a reported position. Ambiguity is indicated in the six latitude characters, and the same degree of ambiguity must be applied to longitude. The specification's example `T4SQZZ` indicates that the last two latitude digits are ambiguous; when decoding longitude, the corresponding last two precision digits must also be ignored. Software must not display such data as precise coordinates.

## Optional telemetry and status text

After the nine basic bytes, **either Mic-E telemetry or status text** may follow. The first byte of the optional section determines how it is interpreted.

| Flag | Meaning in APRS 1.0 |
| --- | --- |
| `` ` `` | Two printable hexadecimal telemetry values; channels 1 and 3 |
| `'` | Five printable hexadecimal telemetry values |
| `0x1d` | Five binary telemetry values; historical beta units |

In the printable variant, each `0–255` value occupies two hexadecimal characters. The specification's example `'7200007100` represents five values: `114`, `0`, `0`, `113`, `0`.

If the optional section is status text, it must not begin with characters reserved as telemetry flags, as this would cause misinterpretation. The text may contain a comment, a Maidenhead locator and altitude. The specification also permits a standard APRS-formatted position inside Mic-E status text; a receiver may use it in place of the position encoded in Mic-E. Historically, this was useful, among other cases, when operating an encoder without a GPS receiver.

### Maidenhead locator

A locator may occur in status text. If followed by a comment, the specification requires appropriate separation of the text. Locator recognition must account for any preceding device identifier.

### Altitude in `aaa}` format

Mic-E altitude is encoded using three Base-91 characters followed by `}`. The reference level is 10,000 m below mean sea level. To encode altitude in metres:

```text
v = altitude_m + 10000
char_1 = floor(v / (91 * 91)) + 33
char_2 = floor(v / 91) % 91 + 33
char_3 = v % 91 + 33
```

Decoding reverses this operation and subtracts `10000`. The specification gives an example of 200 feet (approximately 61 m), encoded as `"4T}`. Later guidance places optional `aaa}` immediately after the device-type identifier, before the remaining text.

## Evolution of the text field

Original devices transmitted status text without an elaborate identifier. Later extensions were placed in the same area so that older receivers could still decode the basic position. WB4APR's documents describe these elements:

| Element | Meaning | Position |
| --- | --- | --- |
| `T` | Optional device-type identifier | Start of status text |
| `aaa}` | Optional Base-91 altitude | Immediately after `T`, if present |
| `FFF.FFFMHz` | Optional frequency | Start of the actual text, after any altitude |
| Free text | Station comment | After fields requiring a fixed position |
| `/A=xxxxxx` | Older altitude notation | Later in the comment, if used |
| `!DAO!` | Extended position precision and datum | Recommended near the end of the text |
| `Mv` | Manufacturer and version identifier | End of text in the newer identification scheme |

This table combines elements from different stages of Mic-E development. Not all are required or present at once. Here `!DAO!` denotes the family of extensions described in APRS 1.2 documentation, not a literal value to transmit. Its exact meaning and validation must follow the documentation of that extension.

When altitude and frequency both occur, the recommended order is `T`, `aaa}`, `FFF.FFFMHz`, followed by text and further additions. The 2008 document also recommends a space after frequency for compatibility with older Yaesu FTM-350 devices.

## Device identification

The device identifier is part of the **optional text**, not the destination address or DTI. The original specification provided, among other identifiers, `>` for the Kenwood TH-D7 and `]` for the TM-D700. Later arrangements extended the identifiers:

| Form | Interpretation in WB4APR documentation |
| --- | --- |
| `>...` | Kenwood TH-D7 family |
| `]...` | Kenwood TM-D700 |
| `]...=` | Kenwood TM-D710 |
| `>...=` | Kenwood TH-D72 |
| `>...^` | Kenwood TH-D74 |
| `` `...Mv `` | Other message-capable Mic-E device |
| `'...Mv` | Other one-way Mic-E tracker |

In this table, ellipses represent variable-length text, not a specific byte count. For later devices, the combination of the initial character and final `Mv` may identify the manufacturer and model. *Mic-E TYPE CODES* lists Yaesu, Byonics, AnyTone and SCS models, among others, but was last updated in 2019. Identifiers absent from the known table must not cause an otherwise valid Mic-E position to be discarded.

Text display must also preserve compatibility: a recognized type identifier can be removed from the operator-visible comment and shown separately as device information. Unrecognized trailing characters must not be removed unconditionally, since they may be part of the comment.

## Decoding examples

### Destination address `S32U6T`

Example from the APRS Protocol Reference:

- Six digits: `332564`, or **33°25.64′**.
- Fourth character `U` indicates the northern hemisphere.
- Fifth character `6` indicates longitude offset `+0`.
- Sixth character `T` indicates the western hemisphere.
- Message bits: `100`, standard state **M3: Returning**.

The address alone does not contain the complete longitude: its numerical values must be taken from the information field.

### Longitude from the information field

In the specification's example, bytes `(`, `_`, `f` have decimal values `40`, `95` and `102`. With offset `+100` and a W indicator, the decoder obtains:

```text
degrees:    40 - 28 + 100 = 112
minutes:    95 - 28 - 60 = 7
hundredths: 102 - 28 = 74
result:     112°07.74′ W
```

### Speed and course from the information field

The same example uses bytes `n`, `"`, `O`:

```text
sp = 110 - 28 = 82
dc =  34 - 28 = 6
se =  79 - 28 = 51

speed  = 82 * 10 + 0 = 820; 820 - 800 = 20 knots
course = 6 * 100 + 51 = 651; 651 - 400 = 251°
```

Result: **20 knots at a course of 251°**. Final bytes `j/` specify the jeep symbol from the primary symbol table. These examples demonstrate decoding individual parts of the format; they must not be combined into a new frame without checking every field.

## Implementation guidance

A Mic-E decoder should process bytes rather than assume that the entire information field is ordinary text. Recommended processing order:

1. Validate the AX.25 frame and information-field length (at least nine bytes).
2. Recognize the Mic-E DTI and decode the six destination-address characters, distinguishing standard, custom and unknown messages.
3. Reconstruct coordinates from both fields and account for position ambiguity.
4. Decode speed, course, symbol and symbol table.
5. Recognize optional telemetry or status text without confusing their flags with the DTI.
6. Within status text, recognize only extensions and device identifiers with known syntax; preserve unrecognized data.
7. Do not modify the encoded destination address while forwarding the packet. Changing its characters may alter coordinates, message code or hemisphere indicators.

When converting data to text, take particular care with non-printable Mic-E bytes. Losing even one byte can prevent correct decoding of the entire report. Software should also account for historical deviations in certain devices rather than assume that the DTI alone always determines whether a GPS fix is current.

## Sources and scope

- *APRS Protocol Reference*, APRS Protocol Version 1.0, Document Version 1.0.1, 29 August 2000, chapter 10: **Mic-E Data Format**. Basic encoding of the destination address, information field, messages, position ambiguity, telemetry and text.
- Bob Bruninga, WB4APR, [*Mic-E TEST EXAMPLES*](https://www.aprs.org/aprs12/mic-e-examples.txt), 10 December 2008. Evolution of the text field, extension ordering and backward-compatibility examples.
- Bob Bruninga, WB4APR, [*Mic-E TYPE CODES*](https://www.aprs.org/aprs12/mic-e-types.txt), updated 4 June 2019. Historical and later device identifiers.

The 2008 and 2019 documents expand on topics not fully covered by the original 2000 specification. The device-identifier description is not a complete register of all contemporary Mic-E implementations. General APRS extension rules, such as the full `!DAO!` syntax and universal frequency notation, are separate topics; only their placement within Mic-E status text is discussed here.
