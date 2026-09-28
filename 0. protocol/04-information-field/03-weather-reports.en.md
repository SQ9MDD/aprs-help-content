---
title: "01. APRS Weather Reports"
description: "WX reports: history, formats, required fields, missing measurements, measurement methodology, and CWOP/MADIS data quality."
---

APRS makes it possible to transmit current meteorological measurements directly over a radio network and via APRS-IS. A WX report can contain the station's position, wind, temperature, precipitation, humidity, and atmospheric pressure. The protocol also provides for additional parameters and formats used by older equipment.

Weather does not have a single, exclusive Data Type Identifier (DTI). Meteorological data may form part of an ordinary position report, an APRS object, or a separate positionless report. Interpretation depends on the combination of the DTI, report structure, and station symbol.

## How WX reports evolved

APRS carried weather measurements long before modern Internet-based meteorological services for radio amateurs appeared. Early solutions worked with, among others, Peet Bros ULTIMETER and Davis weather stations. Remote operation was also possible: a weather station, TNC, and radio could relay readings without a computer running continuously. Applications included not only observing local weather but also exchanging reports in observer networks such as SKYWARN.

The development of the format illustrates how APRS was adapted to available hardware and emerging needs:

- **1990s** - APRS supported data from various weather-station models, including manufacturers' raw formats. `WX.TXT` records a format change in APRSdos 793 in June 1997 that was not backward-compatible with the older way of presenting data.
- **2000** - *APRS Protocol Reference 1.0.1* organized weather reporting into three formats: raw, positionless, and complete, the last carrying position and measurements together.
- **2001 and subsequent APRS 1.1 clarifications** - These clarified, among other points, the distinction between a missing measurement and a zero value, the meaning of precipitation counters, and the ambiguity of field `s`, which can mean wind speed or snowfall depending on the report variant.
- **Since July 2001** - Data from CWOP, which grew out of the amateur-radio community and APRSWXNET, have fed NOAA's MADIS system. Reports from amateur weather stations thus entered the wider circulation of meteorological observations.
- **2006** - The use of APRS for reporting water levels and flood hazards was documented. Water-gauge symbols appeared, along with a proposal to carry additional measurements in weather fields.
- **March 2011** - Following the Fukushima Daiichi nuclear accident, Bob Bruninga proposed extending the WX format to include radiation readings. A document dated 24 March 2011 describes field `Xxxx` and further standardization of how sensors and hazards could be indicated.

The history of the radiation field illustrates an important property of APRS: the WX format came to be seen as a carrier of environmental measurements, not just conventional meteorology. However, fields in the base specification must be distinguished from later proposals. Documentation of an extension does not mean every application implements it.

## Types of weather reports

*APRS Protocol Reference 1.0.1* distinguishes three formats:

| Format | Characteristics |
| --- | --- |
| **Complete Weather Report** | Meteorological data and position in one packet. The recommended format for new implementations. |
| **Positionless Weather Report** | Meteorological data without coordinates. The receiver must already know the station's position from a separate packet. |
| **Raw Weather Report** | Raw data in the proprietary format of a particular weather instrument. A historical solution, not recommended for new transmitters. |

Combining position and current measurements in one packet reduces dependence on earlier transmissions. This is particularly important on a radio channel, where reception of every frame is not guaranteed.

## Complete weather report

In its basic form, a Complete Weather Report is a position report with a weather symbol followed by WX data. Any of the four standard position DTIs may be used:

| DTI | Timestamp | Declared APRS messaging support |
| --- | --- | --- |
| `!` | no | no |
| `=` | no | yes |
| `/` | yes | no |
| `@` | yes | yes |

### Which elements are required in a complete report?

For an **uncompressed** Complete Weather Report with position, the required elements are the position structure specified by the selected DTI, a weather symbol, the seven-character wind direction and speed field `ddd/sss`, and the temperature field `txxx`. These **fields must be present, but the station does not have to possess every sensor**. If a measurement is unavailable, retain its field position and insert dots or spaces of the appropriate length.

| Element | Required in an uncompressed complete report? | If a reading is unavailable |
| --- | --- | --- |
| DTI and valid position | Yes | Position defines this variant; it cannot be replaced by WX dots. |
| Weather-station symbol | Yes | Conventionally `/_` or `\_`; both characters in the symbol matter. |
| `ddd/sss` - wind direction and speed | Yes | `.../...` or three spaces on each side of the slash. |
| `txxx` - temperature | Yes | `t...` or `t` followed by three spaces. |
| `gxxx` - wind gust | No, according to later clarifications of the complete format | It may be omitted or written as `g...`. |
| Precipitation, humidity, pressure, and other fields | No | Omit the field or replace its digits with dots/spaces. |

The distinction concerning gusts matters: in a **positionless report**, `gxxx` belongs to the required initial field set, whereas later clarifications of the complete format primarily require `ddd/sss` and `txxx`. APRS101 tables and examples also frequently contain `gxxx` when gust readings are available.

Example of a complete packet in monitor format:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_220/004g005t068r000p015P012h72b10132
```

The part after the colon is the Information field. Its main components are:

```text
! | 5003.50N | / | 01956.75E | _ | 220/004 | g005t068r000p015P012h72b10132
```

| Element | Meaning |
| --- | --- |
| `!` | DTI for a position report without a timestamp. |
| `5003.50N` | Latitude. |
| `/` | Primary symbol-table identifier. |
| `01956.75E` | Longitude. |
| `_` | Weather-station symbol code. |
| `220/004` | Wind direction and speed. |
| `g005...` | Remaining meteorological data. |

The characters `/` and `_` in the position portion together specify the symbol `/_`. Do not confuse the symbol code `_` with DTI `_`, which appears at the beginning of a separate positionless report.

### Measurement fields and units

The conventional WX format uses short, fixed-width fields. In a complete report, wind direction and speed appear without letter prefixes, in the seven-byte `ddd/sss` extension.

| Field | Measurement | Unit and encoding |
| --- | --- | --- |
| `ddd/sss` | Wind direction and average speed | Degrees and mph; speed averaged over 1 minute. **Required field.** |
| `gxxx` | Wind gust | mph; maximum speed during the previous 5 minutes. Optional in a complete report. |
| `txxx` | Temperature | °F; negative values are supported, e.g. `t-07`. **Required field.** |
| `rxxx` | Precipitation in the previous hour | Hundredths of an inch. |
| `pxxx` | Precipitation in the previous 24 hours | Hundredths of an inch; rolling 24-hour window. |
| `Pxxx` | Precipitation since midnight | Hundredths of an inch. |
| `hxx` | Relative humidity | Percent; `h00` means 100%. |
| `bxxxxx` | Atmospheric pressure | Tenths of hPa (mbar). |

Values are transmitted in the units specified by the protocol, regardless of the units an application uses to display them. A receiver may display temperature in °C, wind in km/h, and precipitation in millimeters, but this does not change the report encoding.

In the example above:

| Data | Value decoded from the report |
| --- | --- |
| Wind | 220°, 4 mph |
| Gust | 5 mph |
| Temperature | 68°F (20°C) |
| Precipitation in the previous hour | 0 |
| Precipitation in the previous 24 hours | 0.15 inch |
| Precipitation since midnight | 0.12 inch |
| Humidity | 72% |
| Pressure | 1013.2 hPa |

Fields `r`, `p`, and `P` represent different time intervals. In particular, `p` does not mean precipitation on the preceding calendar day, and `P` is not interchangeable with `p`. How these values are derived, and how APRS and CWOP wind measurements differ, is explained in the measurement-methodology section.

### Missing measurements: dots, spaces, and omitted fields

APRS distinguishes an **actual zero value** from an **unavailable measurement**. When a report includes a defined field but the station lacks the relevant sensor or the reading is temporarily unavailable, the numeric characters may be replaced with dots (`.`) or spaces. The field width stays the same. Later clarifications by the protocol's author recommend dots as the more readable notation.

| Situation | Example | Interpretation |
| --- | --- | --- |
| No anemometer | `.../...` | Wind direction and speed are unknown. |
| Direction unavailable, speed known | `.../004` | Speed is known to be 4 mph, but direction is unknown. |
| No thermometer | `t...` | Temperature is unknown. |
| No gust measurement, field present | `g...` | Gusts are unknown; this does not mean 0 mph. |
| No pressure reading, field present | `b.....` | Pressure is unknown. |
| No rain gauge reading, field present | `r...` | No data on precipitation in the previous hour. |
| Measured absence of rainfall | `r000` | The measured value is exactly 0.00 inch. |

**Empty optional fields do not have to be transmitted.** Both `r...` and omission of `r` indicate that no reading is available, whereas `r000` is a specific measurement result. Required fields, however, must not be deleted just because the corresponding sensor is absent.

Example of a minimal uncompressed report from a station with only a rain gauge:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_.../...t...r012
```

After symbol `_` come the required fields `.../...` and `t...`. Optional `g` is omitted because the station does not measure gusts. The only available measurement is `r012`: 0.12 inch of rain fell during the previous hour. This is valid: **the format requires particular fields to be present, but does not require every sensor to be installed**.

By contrast:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_r012
```

is not equivalent. It omits required fields in this type of uncompressed report and should not be generated as a valid Complete Weather Report.

After the required elements, additional parameters need not all be present or always appear in the same order. A parser should identify their field identifiers and fixed widths rather than expect the entire sequence of possible measurements.

## Position, time, and objects

A complete report can use either an uncompressed or a compressed position. In the uncompressed variant, the `ddd/sss` extension immediately follows the weather symbol. In the compressed variant, wind information occupies the appropriate fields of the compressed position, so the seven-byte `ddd/sss` must not be appended again. The examples in this article that replace wind values with dots concern the uncompressed format, not the compression bytes.

A report with a timestamp may look like this:

```text
SQ9MDD>APRS:@282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

DTI `@` indicates a timestamped position that declares APRS messaging support. `282000z` means day 28 of the month at 20:00 UTC.

Measurements can also be attached to an APRS object, for example when a station publishes data from a remote sensor:

```text
SQ9MDD>APRS:;WX-KRAKOW*282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

Here, the report starts with DTI `;`, and `WX-KRAKOW` names the object. Its coordinates and weather data describe the object, not necessarily the transmitting station's location.

## Positionless weather report

A Positionless Weather Report begins with DTI `_`. It is followed by an eight-digit `MMDDHHMM` timestamp and then the measurement fields. Here wind direction and speed are identified by letters `c` and `s`, rather than encoded as `ddd/sss`.

Example from the APRS Protocol Reference:

```text
_10090556c220s004g005t077r000p000P000h50b09900wRSW
```

| Fragment | Meaning |
| --- | --- |
| `_` | Positionless weather-report DTI. |
| `10090556` | October 9, 05:56. |
| `c220s004` | Wind 220°, 4 mph. |
| `g005t077` | Gust 5 mph; temperature 77°F. |
| `r000p000P000` | Three independent precipitation measurements. |
| `h50b09900` | Humidity 50%; pressure 990.0 hPa. |
| `wRSW` | Historical weather software and device identifier. |

In this variant the **required initial sequence** is: `_` + eight-digit timestamp `MMDDHHMM` + `cxxx` + `sxxx` + `gxxx` + `txxx`. These fields must remain in this order. Other parameters may follow in varying order or be omitted entirely. Here `s` means wind speed, not snowfall.

Example of a station equipped only with a rain gauge, following the structure documented in APRS101:

```text
_10090556c...s...g...t...P012
```

Fields `c`, `s`, `g`, and `t` **must still be present**, although none of those measurements is available. `P012` means 0.12 inch of precipitation since midnight. If wind direction were actually 0°, the correct value would be `c000`, not `c...`.

The packet does not contain coordinates. To display the weather station on a map, the receiver must know its location from an earlier position report. This is why later APRS recommendations favor complete reports carrying both position and current measurements.

## Historical raw weather reports

Older weather stations could transmit readings in their proprietary formats without converting them into generic WX data. APRS101 lists these identifiers:

| DTI | Historical device format |
| --- | --- |
| `!` | Ultimeter 2000 |
| `#` | Peet Bros U-II |
| `$` | Ultimeter 2000 |
| `*` | Peet Bros U-II |

An example of a raw Peet Bros U-II report from the documentation:

```text
#50B7500820082
```

Some of these identifiers overlap with DTIs used for other APRS data types. Correct identification therefore requires examining the subsequent syntax. When designing a new transmitter, convert the device readings into the complete WX format instead of transmitting the manufacturer's raw format.

## Additional weather fields

Besides the basic measurements, the documentation also specifies additional fields. Not all devices and applications support them.

| Field | Meaning | Notes |
| --- | --- | --- |
| `Lxxx` | Solar irradiance | 0-999 W/m². |
| `lxxx` | Solar irradiance | 1000 W/m² and above; add 1000 to the three-digit number. |
| `sxxx` | Snowfall in the previous 24 hours | Inches; in a complete report, `s` does not conflict with the wind-speed field's position. |
| `#xxx` | Raw rain gauge counter | No universal unit; interpretation is device-specific. |

For example, `L700` means 700 W/m², while `l123` means 1123 W/m². In a positionless report, identifier `s` already denotes wind speed, so a snowfall field must not be added there in a way that causes this collision.

### Water gauges: the 2006 extension

In June 2006, the use of APRS to transmit water levels and indicate flooding was documented. Symbols `/w` (water gauge) and `\w` (flood) were introduced, along with a proposal to append water-level measurements to conventional weather data.

An example of the format historically used by water gauges on the FIRENET network is an APRS object:

```text
;09428508 *061713z3401.40N/11424.75Ww3.57gh/82cfs
```

Here `3.57gh` describes gauge height and `82cfs` the flow in cubic feet per second. This is a **textual object description**, not the `Fxxxx` field of a conventional WX report. The March 2011 update noted that this was the format then being used in practice, despite previous proposals to extend the weather format.

The proposed additional measurements within WX included these fields:

| Field | Meaning in the extension documentation |
| --- | --- |
| `Fxxxx` | Water level relative to a reference level, in tenths of a foot; positive and negative values are possible. |
| `Vxxx` | Supply voltage in tenths of a volt, e.g. `V128` = 12.8 V. |
| `Zxx` | Device-type code provided for in the extended sensor description. |

For a weather station equipped with a water gauge, the proposed approach was to preserve the conventional WX structure, e.g. `.../...t...V128F+123` (12.3 feet above the reference level in this example), rather than define an entirely new report. The 2011 descriptions distinguish water gauges sending their own textual data from weather stations carrying extended measurement fields. A water-gauge symbol alone does not guarantee that WX fields are present.

### Fukushima and the 2011 radiation-measurement proposal

Following the Fukushima Daiichi nuclear accident in March 2011, a need emerged to transmit radiation readings as well. Bob Bruninga explicitly referred to the events in Japan in *APRS 1.2.1 Weather Updates to the Spec*, dated 24 March 2011. The proposed solution reused the existing weather-report format rather than creating a separate transmission format.

The new `Xxxx` field was designed to encode radiation dose rate in nanosieverts per hour (`nSv/h`). The letter `X` would be followed by three digits: two significant digits and a base-ten exponent. For example:

| Field | Interpretation | Result |
| --- | --- | --- |
| `X123` | 12 × 10³ nSv/h | 12 µSv/h |
| `X456` | 45 × 10⁶ nSv/h | 45 mSv/h |

In parallel, overlays on existing symbols were proposed to indicate sensor type or hazards: the standard weather symbol for background readings, overlay `R` for a radiation-monitoring station, and a suitable overlay on the hazard symbol once a defined threshold was exceeded. This would allow existing sensor-display mechanisms on the map to be reused and would distinguish measurements from an indicated hazard.

**The status of this solution matters:** the March 2011 document describes `Xxxx` as a *proposed* extension. Support for this field and the proposed overlays must not be assumed in all current applications, nor should it be treated as a mandatory part of APRS101. Likewise, `Fxxxx`, `Vxxx`, and `Zxx` require consideration of actual receiving-software compatibility.

## Symbols and weather recognition

The traditional weather-station symbol is `/_`, while the alternate symbol table permits `\_`. APRS 1.1 clarifications also include `/W` and `\W` as additional symbols associated with weather stations. The 2011 document proposes harmonizing sensor recognition by using weather symbols with overlays and hazard symbols. This later proposal does not change the decoding rules for the basic WX fields.

This does not mean that every packet containing `_` is a WX report. In a position report, identify the symbol code in the correct location and verify the syntax of the data that follow it. In a positionless report, `_` has another function: it is the first byte of the Information field, that is, the DTI.

## CWOP: from APRS stations to professional meteorological observations

The WX format is used beyond amateur-radio networks. One example is the **Citizen Weather Observer Program (CWOP)**, which grew out of APRSWXNET and the amateur-radio community. The program enables volunteers to contribute measurements from private weather stations to a shared meteorological dataset. Both radio amateurs and observers who submit reports directly over the Internet, without radio transmission, may participate.

Since 1 July 2001, CWOP observations have been fed into **MADIS (Meteorological Assimilation Data Ingest System)**, developed by the US NOAA. MADIS integrates measurements from many independent sources, standardizes their formats, units, and timestamps, and performs automated quality control. Quality-control results are attached to the observations so data users can account for the reliability of individual readings.

For an amateur-radio station, a WX report may reach APRS-IS over radio via an IGate. CWOP stations can also submit their data over an appropriate Internet connection. A simplified flow for **stations participating in CWOP** is:

```text
WX station -> radio -> IGate -> APRS-IS --+
                                         +-> CWOP / APRSWXNET -> NOAA MADIS
WX station -> Internet -------------------+                         |
                                                                   +-> meteorological services
                                                                   +-> research institutions
                                                                   +-> universities and other users
```

This is a functional diagram, not a description of every internal connection. MADIS data-acquisition arrangements have changed over time: since 2023, NOAA has indicated direct acquisition from APRSWXNET and CWOP servers rather than the earlier route through the findU service.

CWOP data are made available to a large group of meteorological users, including forecast offices of the US **National Weather Service (NWS)**, research centers, universities, and private organizations. They can supplement professional-station observations, support local weather monitoring, forecast verification, and modeling. NWS also notes the use of such observations in preparing forecasts and weather warnings. This does not mean every individual reading is used for every such purpose.

### Station quality and registration matter

The practical value of these reports depends on more than correct WX syntax. Proper sensor siting, correct units and measurement times, up-to-date station coordinates, and prevention of measurement errors are also essential. MADIS quality control can detect some anomalies and flag questionable data, but it cannot replace proper weather-station installation.

**Not every weather report visible on APRS-IS is automatically ingested into MADIS.** CWOP participation requires registration and correct configuration of the data-delivery method. NOAA provides a separate form for new participants and updates to existing stations, including radio amateurs using callsigns.

CWOP demonstrates the broader significance of WX: a correctly encoded APRS packet can be more than an item displayed on a map; it can be part of a system collecting and distributing measurements used in professional meteorology.

## Measurement methodology: APRS compliance and CWOP data quality

A properly constructed WX frame does not guarantee reliable data. The APRS specification describes field formats and meanings, while the [2005 CWOP guide](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf) gives recommendations for measurement methodology, instrument performance, and siting. Both documents should be read together, but their requirements must not be treated as identical. A custom implementation should aim for correct APRS reporting and the highest practicable quality of observations sent to CWOP/MADIS, not claim a guaranteed pass through quality control.

### Wind: two different ways to derive values

| Parameter | Conventional APRS WX | Recommendations in the 2005 CWOP guide |
| --- | --- | --- |
| Average wind speed | Average over the previous 1 minute | Average over the previous 2 minutes |
| Wind direction | Direction from which the wind blows, in degrees | Average direction over the previous 2 minutes, relative to true north |
| Gust `gxxx` | Maximum speed over the previous 5 minutes | Maximum speed reading over the previous 10 minutes |
| Sampling | `WX.TXT` describes a historical example using four samples taken 15 seconds apart to calculate the one-minute average | The CWOP guide recommends reading sensors at least every 5 seconds |

This difference is documented. The authors of the CWOP guide themselves put changes to the APRS intervals, from **1 to 2 minutes** and from **5 to 10 minutes**, on a list of proposed format changes. The CWOP periods should therefore not be presented as the definitions adopted by APRS101. Importantly, fields `ddd/sss` and `gxxx` carry no metadata telling the receiver which measurement period was used. Software serving both purposes should retain raw samples and calculate separate values for an APRS profile and a CWOP measurement profile. The profile used for the transmitted report must be chosen deliberately and documented.

A suitable time window is sufficient for calculating average wind speed. However, directions must not be averaged with an ordinary arithmetic mean: readings of 359° and 1° indicate northerly wind, not 180°. Use a circular mean or a suitable vector algorithm, taking account of how the sensor supplies its readings. In calm conditions, when direction is unreliable, avoid false precision. Also remember that the maximum of instantaneous samples described by the CWOP guide may not match other gust definitions, such as the maximum three-second average in WMO methodology.

### Precipitation: three independent intervals

The safest basis for calculations is a continuous, timestamped history of precipitation increments, for example events from a tipping-bucket rain gauge. From these data, the generator separately calculates `rxxx` for the previous 60 minutes, `pxxx` for the rolling previous 24 hours, and `Pxxx` for the period since the station's **local midnight**. Do not derive `p` from `P` or substitute rainfall since the start of the current day for a rolling window. Encode results in hundredths of an inch, regardless of the sensor's native units.

After a device restart or loss of history, do not report an incomplete total as though it covered the entire required interval. If the archive cannot reconstruct a particular window, mark that measurement unavailable or omit the optional field. When the rain-gauge counter resets, distinguish actual rainfall from the jump caused by the reset. The station's correct time zone and retention of timestamps for precipitation increments also matter.

### Temperature, humidity, and pressure

APRS defines the units and encoding for these fields but does not mandate a single averaging window for them. The 2005 CWOP guide **recommends** an average temperature over the previous 5 minutes and a previous-one-minute humidity average used to calculate dew point. These are data-quality recommendations, not additional mandatory bytes in a WX report. Every reported result should represent the actual measurement time, not the last arbitrary value retained in memory.

Field `bxxxxx` requires particular care: APRS defines its unit (tenths of hPa), but correct encoding alone does not settle **which pressure** an instrument supplies. The 2005 CWOP guide identifies *altimeter setting* (QNH), pressure reduced using the appropriate method rather than a raw reading at sensor elevation, as the intended parameter. QNH must also not be assumed identical to meteorological sea-level pressure (QFF) without verification. Before submitting data to CWOP, check which value the station exports, its calibration, and the recommendations of the software in use.

### Sensor installation and quality control

An algorithm cannot correct errors caused by poor equipment placement. The CWOP guide recommends a ventilated thermometer shielded from radiation at approximately 1.5 m above representative ground, an anemometer ideally 10 m high in as open a location as possible, and a level rain gauge protected from airflow disturbances. Compromises in built-up areas may be unavoidable, but they should be documented. Station metadata, particularly coordinates and elevation, must correspond to the actual measurement site.

[MADIS](https://madis.ncep.noaa.gov/madis_qc.shtml) checks value ranges, internal consistency, temporal changes, and spatial consistency, depending on the parameter and available quality-control level. Results are quality flags attached to observations. This is not a universal test that permanently certifies an entire station; even a correct measurement may be flagged as suspect, while a syntactically valid frame may contain incorrect values. Operators should regularly review feedback, compare observations against suitable reference stations, and check calibration.

### Guidance for WX software developers

Separate three processing stages: **acquiring samples**, **deriving observations**, and **encoding APRS**. In this way, changing the transmission interval will not accidentally change the averaging periods, and the same measurement stream can supply different observation profiles. In particular:

1. Retain each sample's timestamp, original unit, validity status, and sufficient history for the longest measurement window in use.
2. Detect missing data, lost sensor communication, counter resets, and incomplete windows after startup; do not turn these conditions into zero readings.
3. Calculate statistics from source data and only then round and convert to APRS frame units. Avoid repeated conversions and intermediate rounding.
4. Preserve documentation of the methodology used, averaging intervals, and sensor setup. This helps interpret observations correctly and diagnose possible MADIS quality flags.

Protocol compliance alone does not guarantee CWOP ingestion or a favorable assessment of every observation. Station registration, proper delivery, reliable measurements, and ongoing quality monitoring are also required.

## Implementation notes

Regardless of measurement methodology, WX generators and parsers must maintain syntactic compliance with the protocol. The following principles are especially important:

1. Prefer a Complete Weather Report, which carries position and measurements in a single transmission.
2. Preserve protocol units at input and output. Convert to metric for presentation, or before encoding the values to be transmitted.
3. Treat `r`, `p`, and `P` as three different precipitation measurement intervals.
4. Validate **required-field presence** separately from the availability of the measurements themselves. In an uncompressed complete report, retain `ddd/sss` and `t`; in a positionless report, retain the timestamp and `c`, `s`, `g`, and `t`.
5. Do not equate an unavailable measurement with zero; handle dots, spaces, and permissible omitted fields. Prefer dots for readability in text dumps.
6. Recognize the different wind encodings in complete, positionless, and compressed-position reports.
7. Distinguish classical WX fields from later extensions and allow unknown fields to be ignored. Do not assume all clients support the `Xxxx` proposed in 2011.
8. Do not confuse a measurement report with a weather warning. Hazard notifications, including NWS-WARN and other alerting systems, use separate mechanisms.
9. Do not change field semantics just because an application integrates with CWOP. Document the measurement profile and distinguish APRS format requirements from data-quality recommendations.

## Sources

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF), chapter 12: Weather Reports.
- [APRS 1.1: Weather Specification Comments](https://www.aprs.org/aprs11/spec-wx.txt), WB4APR, updated 24 March 2011.
- [APRS 1.2.1: Weather Updates to the Spec](https://www.aprs.org/aprs12/weather-new.txt), WB4APR, 24 March 2011; also contains proposed extensions.
- [Water Gauges in APRS](https://www.aprs.org/aprs12/watergage.txt), WB4APR, 2006; updated 24 March 2011.
- [IAEA: information on the Fukushima Daiichi accident](https://www.iaea.org/newscenter/news/fukushima-nuclear-accident-update-log-20), documentation of the March 2011 events.
- WB4APR, *WX.TXT: Using APRS in Weather and SKYWARN Applications*, version 8.3.5, 10 March 1999, updated 18 August 2010 (historical material).
- [NOAA MADIS: Citizen Weather Observer Program Data](https://madis.ncep.noaa.gov/madis_cwop.shtml), CWOP goals, history, and data processing.
- [NOAA MADIS: APRSWXNET/CWOP Snow Project](https://madis.ncep.noaa.gov/snow_project.shtml), data users and examples of how observations are used.
- [NOAA NWS: Join CWOP](https://www.weather.gov/pub/JoinCWOP), applications of reports in forecasting and warnings.
- [NOAA MADIS: Registration and Update Form](https://madis.ncep.noaa.gov/cwop_signup.shtml), station registration requirements.
- [CWOP Weather Station Siting, Performance, and Data Quality Guide](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf), version 1.0, 8 March 2005; measurement recommendations and historical list of proposed APRS changes.
- [NOAA MADIS: Quality Control](https://madis.ncep.noaa.gov/madis_qc.shtml), overview of quality control and observation flags.
- [NOAA MADIS: Meteorological Surface Quality Control](https://madis.ncep.noaa.gov/madis_sfc_qc.shtml), scope and levels of surface-observation checks.
- [WMO Guide to Meteorological Instruments and Methods of Observation](https://www.weather.gov/media/epz/mesonet/CWOP-WMO8.pdf), reference for international wind and gust measurement definitions.
- [NOAA MADIS: Recent Updates](https://madisqa.ncep.noaa.gov/madis_recent.shtml), change in CWOP data acquisition in 2023.
