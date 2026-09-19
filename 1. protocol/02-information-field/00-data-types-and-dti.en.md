---
title: Data Types and DTI
description: How the APRS Information field is structured, which main data types the protocol defines, what the Data Type Identifier is, and why DTI is not the same thing as an APRS data type.
template: doc
tableOfContents: true
---

The actual APRS data is carried in the **Information** field of an AX.25 UI frame.

In a textual monitor representation, such as TNC2 format, the Information field is the part after the `:` separator:

```text
SQ9MDD-4>APBOX0,WIDE1-1:!5213.45N/02100.12E...
                            ^^^^^^^^^^^^^^^^^^^^^^
                            Information field
```

The `:` separator itself is not an additional byte of the AX.25 Information field. It belongs to the textual representation of the frame.

APRS does not define one universal format for the entire Information field. A position, message, weather report, telemetry report or object is encoded differently. Before interpreting the data, a receiver must therefore determine which kind and variant of data it is dealing with.

The basic mechanism used for this is the **Data Type Identifier**, or **DTI**.

## Generic model of the Information field

APRS Protocol Reference 1.0.1 describes the generic Information field model as:

```text
DTI | APRS Data | APRS Data Extension | Comment
```

This does not mean that every packet must contain all four parts.

In the classic model:

- `DTI` is 1 byte,
- `APRS Data` has a length depending on the format,
- the classic `APRS Data Extension`, if present, is 7 bytes,
- `Comment` is optional and its length depends on the specific format.

`APRS Data Extension` is a term from the base specification and includes classic extensions such as `course/speed`, `PHG`, `RNG` or DF signal data. These 7 bytes should not, however, be treated as the only place where all later APRS extensions can exist. Later specifications introduced additional extensions encoded in the comment, for example `!DAO!` and Base91 Comment Telemetry.

The comment is also not simply "whatever text remains" in every possible packet. In some formats it can contain additional data with its own defined syntax.

## Main APRS data types

The APRS Protocol Reference defines **10 main APRS data types**. This is a semantic classification, so it mainly answers the question:

**what does this packet carry?**

It is not a list of DTI values. One data type can use several different DTIs, and some DTIs have historically overloaded meanings.

| APRS data type | Typical DTI and formats | Meaning |
|---|---|---|
| **Position** | `!`, `=`, `/`, `@`, Mic-E `` ` `` and `'`, raw NMEA `$` | Station position. The four basic position DTIs distinguish timestamp use and messaging capability. Mic-E has its own encoding, while `$` may carry raw NMEA sentences. |
| **Direction Finding** | usually `!`, `=`, `/`, `@`; historically also `%` | A standard DF report is a position report variant with an appropriate symbol and DF data, such as `/BRG/NRQ`. `%` was used by the Agrelo DFJr / MicroFinder format. |
| **Objects and Items** | `;` for Object, `)` for Item | Describes something other than the source station. Object and Item have separate DTIs and different syntax. |
| **Weather** | `_`, and also `!`, `=`, `/`, `@`; historically `!`, `#`, `$`, `*` for raw WX formats | `_` means a weather report without position. Weather can also be attached to a position report. Old raw weather-station formats still appear in the documentation, but APRS 1.1 does not recommend them for new implementations. |
| **Telemetry** | `T` in the classic format | Classic telemetry uses `T#...` packets. Telemetry can also appear elsewhere, historically in Mic-E or, later, as Base91 Comment Telemetry inside a position comment. |
| **Messages, Bulletins and Announcements** | `:` | All use the same DTI. Whether the data is a normal message, bulletin or announcement is determined by the following syntax. |
| **Queries** | `?` for general queries; `:` for directed queries | General queries start with `?`. A query directed to a specific station is encoded as an APRS message and therefore uses DTI `:`. |
| **Responses** | no single dedicated DTI | A response uses the format appropriate for the returned data, for example position, object, weather, status or message. A Station Capabilities response uses DTI `<`. |
| **Status** | `>` | A one-line station status. It can contain ordinary text and, in defined formats, additional information. |
| **Other** | among others `{`, `}`, `,` | Other mechanisms, such as User-Defined Format, third-party traffic and data explicitly marked invalid/test. |

This table already shows one of the most important properties of APRS:

**data type and DTI do not form a one-to-one relationship.**

## What is DTI?

The **Data Type Identifier** is the first byte of the Information field and determines how the rest of the content is interpreted.

In practice, DTI acts as the first parser selector.

For example:

```text
!5213.45N/02100.12E...
```

starts with `!`, so the parser expects a position format without timestamp and without a declaration of APRS messaging capability.

```text
>QRV 145.500 MHz
```

starts with `>`, so the following data is interpreted as status.

```text
;REPEATER*...
```

starts with `;`, so the remaining content follows the APRS object format.

```text
:SP9XYZ  :Test
```

starts with `:`, so it is interpreted according to the APRS message format.

DTI is therefore not simply the name of a data type. It selects a concrete syntax variant and can sometimes carry additional information at the same time.

### DTI should be the first byte

Original APRS 1.0.1 contained an exception for DTI `!` associated with old X1J digipeaters. Under that old rule, `!` could appear as late as the 40th position of the Information field.

APRS 1.1 later removed this exception. Today `!`, like the other DTIs, should appear at the beginning of the Information field.

New implementations should not generate the historical X1J variant.

## One data type, several DTIs

The clearest example is **Position**.

APRS has four basic DTIs for normal position reports:

| DTI | Timestamp | APRS messaging |
|---|---|---|
| `!` | no | no |
| `=` | no | yes |
| `/` | yes | no |
| `@` | yes | yes |

All four belong to the same main data type:

```text
Position
```

but they select different variants of the format.

It can be shown as a matrix:

| | Without timestamp | With timestamp |
|---|---|---|
| No APRS messaging | `!` | `/` |
| With APRS messaging | `=` | `@` |

In this case, DTI therefore conveys simultaneously:

1. that the packet is a position report,
2. whether a timestamp is present in the format,
3. whether the station declares APRS messaging capability.

This is a good example of why calling each individual DTI a "frame type" is too much of a simplification.

## Capabilities - two different concepts

APRS contains two mechanisms related to station capabilities that are easy to confuse.

### Messaging capability in a position DTI

DTI `=` and `@` mean a position report **with APRS messaging**.

DTI `!` and `/` mean the corresponding variants **without APRS messaging**.

This information is part of the meaning of the particular position DTI. It is not a separate data block appended to the report.

### Station Capabilities and DTI `<`

A separate mechanism is the formal **Station Capabilities** packet, whose DTI is:

```text
<
```

APRS101 describes capabilities as a comma-separated list of tokens:

```text
TOKEN
```

or:

```text
TOKEN=VALUE
```

A classic example is:

```text
<IGATE,MSG_CNT=43,LOC_CNT=14
```

In APRS101 such a packet is a response to the `?IGATE?` query.

The following must therefore not be confused:

```text
= or @
```

which carry APRS messaging capability as part of a position-report variant,

with:

```text
<
```

which is a separate Station Capabilities format.

Mic-E also has its own type and device-identification codes. This is yet another independent mechanism and should not be mixed with DTI `<` or with the messaging capability of position reports.

## DTI does not always determine the full semantics

DTI is the primary entry point for the parser, but this does not mean that one character always determines everything unambiguously.

Examples:

- `:` means the message format, but only the following fields distinguish an ordinary message, bulletin, announcement or directed query,
- `!`, `=`, `/` and `@` can carry an ordinary position, DF data or weather data attached to a position,
- `!` and `$` also have old overloaded meanings associated with raw weather-station formats,
- a response to a query has no single common DTI, because the response can be a position, status, weather report, object or Station Capabilities packet,
- Mic-E uses DTI together with the Information field and encoded data in the Destination Address.

A correct APRS parser therefore cannot stop at a simple `switch(DTI)`. DTI selects a family of rules, but a concrete format may require further recognition.

## Complete DTI table from APRS101

The table below shows the meanings defined in APRS Protocol Reference 1.0.1 together with the most important modern notes.

| DTI | Meaning | Notes |
|---|---|---|
| `0x1c` | Current Mic-E Data, Rev 0 beta | Very old beta format. |
| `0x1d` | Old Mic-E Data, Rev 0 beta | Very old beta format. |
| `!` | Position without timestamp, no APRS messaging | Historically also Ultimeter 2000 WX. The old X1J exception allowing `!` later in the field has been removed. |
| `"` | Unused | Do not use as a new DTI. |
| `#` | Peet Bros U-II Weather Station | Raw historical WX format. New implementations should not choose it. |
| `$` | Raw GPS data or Ultimeter 2000 | For GPS, typically raw NMEA sentences. Historically overloaded by a WX format. |
| `%` | Agrelo DFJr / MicroFinder | Historical specialized direction-finding format. |
| `&` | Reserved - Map Feature | Reserved in APRS101. |
| `'` | Mic-E | APRS101 describes it as Old Mic-E, but the Kenwood TM-D700 used it for current Mic-E. In practice it should be handled according to Mic-E rules. |
| `(` | Unused | Do not use as a new DTI. |
| `)` | Item | Item format is defined, but APRS 1.1 does not recommend its use on RF because of compatibility problems. |
| `*` | Peet Bros U-II Weather Station | Raw historical WX format. |
| `+` | Reserved - Shelter data with time | Reserved in APRS101. |
| `,` | Invalid data or test data | Packet deliberately marked as non-standard or test data. |
| `-` | Unused | Do not use as a new DTI. |
| `.` | Reserved - Space weather | Reserved in APRS101. |
| `/` | Position with timestamp, no APRS messaging | One of the four basic position DTIs. |
| `0`-`9` | Do not use | Not intended for DTI use. |
| `:` | Message | The same DTI is used for messages, bulletins, announcements and directed queries. |
| `;` | Object | APRS object report. |
| `<` | Station Capabilities | Capability token format, e.g. `<IGATE,MSG_CNT=43,LOC_CNT=14`. |
| `=` | Position without timestamp, with APRS messaging | One of the four basic position DTIs. |
| `>` | Status | Status report. |
| `?` | Query | General query. Directed queries use message format `:`. |
| `@` | Position with timestamp, with APRS messaging | One of the four basic position DTIs. |
| `A`-`S` | Do not use | Range reserved as unsuitable for new DTIs. |
| `T` | Telemetry data | Classic `T#...` telemetry format. |
| `U`-`Z` | Do not use | Range reserved as unsuitable for new DTIs. |
| `[` | Maidenhead grid locator beacon | Marked obsolete in APRS101. |
| `\` | Unused | Do not use as a new DTI. |
| `]` | Unused | Do not use as a new DTI. |
| `^` | Unused | Do not use as a new DTI. |
| `_` | Weather Report without position | Standard positionless weather report. |
| `` ` `` | Current Mic-E Data | Standard Mic-E DTI. APRS101 notes an exception involving the TM-D700. |
| `a`-`z` | Do not use | Not intended for DTI use. |
| `{` | User-Defined APRS packet format | Reserved mechanism for application-defined formats. |
| `|` | Do not use as DTI | In APRS101 this character was associated with historical TNC stream switching. Later extensions may use `|` inside data, e.g. Base91 Comment Telemetry. |
| `}` | Third-party traffic | Wrapper for an APRS packet transported through an intermediate network. The inner packet contains its own header and APRS data. |
| `~` | Do not use as DTI | Historically associated with TNC stream switching. |

The APRS101 DTI table reflects the state of the protocol around 2000. Later addenda corrected some recommendations and marked some older mechanisms obsolete or not recommended.

## Weather shows why the DTI table alone is not enough

Weather is a particularly good example of APRS being layered.

A report without position can start with:

```text
_
```

Weather data can also be attached to a position report beginning with:

```text
!
=
/
@
```

In addition, APRS101 defined raw formats of particular weather stations using:

```text
!
#
$
*
```

APRS 1.1 recommends that new devices do not transmit raw weather-station formats, but instead convert the data to standard APRS formats.

This means that the DTI value alone can require looking at the following payload and understanding historical variants.

## Telemetry does not always have DTI `T`

Classic APRS telemetry has the form:

```text
T#001,123,045,255,010,100,10110011
```

and uses DTI:

```text
T
```

This does not mean that all telemetry data in APRS must appear in a `T` packet.

Historical Mic-E telemetry used the Mic-E mechanism and was later considered obsolete after Mic-E Type Codes were introduced.

Later **Base91 Comment Telemetry** allows telemetry data to be placed directly in a position comment, for example between `|...|`. In that case, the DTI of the whole packet remains the position DTI, such as `!`, `=` or Mic-E, while telemetry is an extension located deeper inside the data.

This is another example showing that:

**an APRS data type is not the same thing as the DTI of the first byte.**

## User-Defined APRS packet format

DTI:

```text
{
```

is formally assigned to the **User-Defined APRS packet format**.

APRS101 defines the beginning of such a packet as:

```text
{UX...
```

where:

- `{` is the DTI,
- `U` is a one-byte User ID,
- `X` is a one-byte identifier of a specific packet type for that author,
- the author's format data follows.

Example from APRS101:

```text
{Q1qwerty
```

means:

```text
DTI:         {
User ID:     Q
Packet Type: 1
Data:        qwerty
```

For experiments, a User ID equal to `{` was reserved, so an experimental packet may start with:

```text
{{
```

Example:

```text
{{zasdfg
```

The later registry of experimental formats included, among others, headers such as:

```text
{BT
{BP
{Dx
{KY
{Q1
{Q2
{Sx
```

User-Defined Format does not mean that every APRS program must understand such a packet. APRS101 explicitly assumes these formats are optional, and an application that does not recognize a given User ID and Packet Type should safely ignore it.

The `{` mechanism is therefore a controlled way of extending APRS without pretending that non-standard data belongs to one of the standard formats.

## Third-party traffic and DTI `}`

DTI:

```text
}
```

means **third-party traffic**.

In this format, the Information field contains a wrapped original APRS packet together with its header and data.

Schematically:

```text
}SOURCE>DEST,PATH:DTI...
```

The first DTI of the outer packet is `}`, but the inner packet has its own DTI and must be interpreted again according to its format.

This is a special case where, after recognizing `}`, the parser returns to analysing a complete APRS packet contained inside the data.

## Characters `|` and `~` - historical restriction

APRS101 marks `|` and `~` as characters that should not be used as DTI because old TNCs used them for stream switching.

In the original specification, this restriction was also treated more broadly in relation to comments.

The later Base91 Comment Telemetry specification deliberately uses:

```text
|...|
```

as delimiters for telemetry data in a comment.

The authors of that extension note that the restriction came from the very old CONVerse mode of classic TNCs, while in practice the `|` character passes correctly through modern APRS networks and APRS-IS.

A modern parser should therefore not apply the old rule "the `|` character can never occur in APRS data". It is still not a standard DTI, but it can have a defined meaning inside a specific format or extension.

## Practical rule

The safest way to interpret the Information field is:

1. read the first byte as DTI,
2. select the format family indicated by the DTI,
3. remember that one main data type may have several DTIs,
4. account for additional DTI semantics, such as timestamp and APRS messaging in position reports,
5. if a format is overloaded or historical, inspect the following syntax,
6. parse classic Data Extension only where the selected format permits it,
7. after the main data, account for extensions and information encoded in the comment,
8. for `{`, apply the rules of the specific User ID and Packet Type,
9. for `}`, unwrap the inner packet and restart interpretation from its DTI,
10. do not assume that an unknown DTI can safely be interpreted as plain text.

DTI is therefore the first key to decoding the Information field, but the full meaning of a packet comes from the combination of:

```text
main data type
+
DTI
+
format variant
+
optional capabilities
+
extensions
+
comment
```

## Sources

This article is based on:

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [APRS 1.2 Addendum Proposals](https://www.aprs.org/aprs12.html)
- [APRS User Defined Data Formats List](https://www.aprs.org/aprs11/expfmts.txt)
- [APRS Documentation Project - APRS 1.2 compilation](https://github.com/wb2osz/aprsspec)
- [Base91 Comment Telemetry specification](https://github.com/hessu/aprs-specs/blob/master/aprs-base91-comment-telemetry.txt)
