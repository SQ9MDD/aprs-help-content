---
title: APRS Paths in Practice
---

An APRS path specifies which digipeaters may retransmit a radio frame and in what order. It can identify individual stations or use aliases supported by multiple digipeaters. The actual course of a transmission depends on the path as written, the stations' configurations and their overlapping coverage areas.

This article covers conventional AX.25 addressing, the `WIDEn-N` mechanism, traced and untraced paths, regional and event-specific aliases, modern approaches to fill-in retransmission and the special case of satellite digipeaters. The examples illustrate possible frame transformations under assumed digipeater configurations. They do not imply that the aliases shown work throughout the entire APRS network.

## 1. Path structure and notation

The path is located in the address field of an AX.25 frame, following the destination and source addresses. In human-readable TNC2 notation, its elements are separated by commas:

```text
SQ9MDD-9>APRS,WIDE2-1:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SP2-2:...
```

`SQ9MDD-9` is the frame source, while `APRS` is an example destination address (TOCALL), not a digipeater name. Only the addresses after the first comma form the path. If none are present, the frame is transmitted without requesting digipeating:

```text
SQ9MDD-9>APRS:...
```

Devices may label this configuration `DIRECT`. However, that is neither an additional hop nor an alias to insert into the frame. A frame without a path can still be received by distant stations and forwarded to APRS-IS by IGates that hear it directly.

Under normal processing, a digipeater examines the **first unused element** of the path. Later elements are not active until the preceding ones have been handled. Exceptions include specifically configured mechanisms such as preemptive digipeating, discussed below.

### AX.25 limits

Each address in a radio AX.25 frame occupies seven bytes: six for the callsign or alias (space-padded if shorter) and one containing, among other things, the four-bit SSID and address flags. An AX.25 SSID ranges from `0..15`. The traditional format used by APRS supports up to eight digipeater addresses. This does not mean all eight should be used: every additional entry increases the frame length, and tracing may consume further free address slots.

Two cases must be distinguished when designing aliases:

- A simple alias, such as `ARISS` or `RAJD`, fits within the six-character address field.
- With an `n-N` alias such as `RAJD2-2`, `RAJD2` occupies the six-byte name field (`RAJD` plus digit `n`), while `-2` is the SSID used as the `N` counter.

For a single-digit `n`, the base name of this kind of alias must therefore be no more than five characters long. Text notation does not remove the limitations of the binary AX.25 address field.

### The asterisk and the H bit

Each digipeater address has its own **H** bit (*has been repeated*), indicating that the corresponding path element has already been used. In typical TNC2 monitor notation, an asterisk appears **only after the last used element**; all earlier elements are implicitly considered used:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...   # before digipeating
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...  # after the first digi
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...  # after the second digi
```

The last line does not mean that `SR5AAA` failed to retransmit the frame. In binary AX.25, the H bits of both addresses are set. Some diagnostic programs show an asterisk next to every used address, but this is not the usual abbreviated TNC2 notation. The textual `*` represents the H bit; it is not a character transmitted as part of the AX.25 address.

## 2. Origins of the New-N Paradigm

Older APRS networks used aliases including `RELAY`, `WIDE`, `TRACE` and `TRACEn-N`. They extended coverage, but some implementations of the time generated excessive duplicates of the same frames. Moreover, the original `WIDEn-N` often did not record digipeater callsigns, making traffic analysis and network planning more difficult.

The **New-N Paradigm** initiative, launched in late 2004, reorganized these mechanisms:

- The historical `RELAY` was replaced by `WIDE1-1`, preserving a way for mobile stations to use simple home fill-in digipeaters.
- `WIDEn-N`, with a remaining-retransmissions counter, became widespread in place of the old single `WIDE` alias.
- `WIDEn-N` began to be handled in trace mode, historically implemented, among other ways, through `UITRACE`.
- The untraced `UIFLOOD` mechanism was retained for uses including regional `SSn-N` networks.
- Limiting excessive counter values and suppressing duplicates became core elements of digipeater configuration.

The names `UITRACE` and `UIFLOOD` originate from particular TNC implementations. Other software may provide equivalent functions under different names. Today, `RELAY`, the old `WIDE` and `TRACE` should primarily be regarded as historical features or legacy configurations, not as interchangeable equivalents of modern `WIDEn-N`.

## 3. Routing by explicit callsigns and simple aliases

The simplest path directly names a digipeater. Successive callsigns specify the relay order:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...
```

`SR5BBB` will not retransmit the frame at the first stage merely because it received the transmission. The `SR5AAA` address must be handled first. Explicit paths are useful when deliberately defining a route through particular stations, for example for point-to-point communications.

A digipeater can also handle a **simple alias**, such as `RAJD`. Upon receiving a frame addressed through `RAJD`, it may replace the alias with its own callsign, or retain the alias and insert its callsign separately. The transformation depends on the implementation and configuration:

```text
SQ9MDD-9>APRS,RAJD:...
SQ9MDD-9>APRS,SR5AAA*:...       # alias replaced with the callsign
```

In the second variant, the station's identity can be inserted separately, at the cost of another address field. A simple alias has no counter of remaining retransmissions. It should therefore not be assumed to work like `WIDE2-2`, or that every station recognizing the same alias will follow identical retransmission rules.

## 4. Reading `n-N`

In the `WIDEn-N` alias family, `n` is the digit before the hyphen and `N` is the SSID value after it:

```text
WIDE2-2
    ^ ^
    n N
```

`n` identifies the alias class and its declared initial number of hops; **`N` counts the retransmissions still remaining**. A frame usually starts with `n = N`, but `WIDE2-1` is also valid: it belongs to the `WIDE2` family while requesting just one remaining hop.

Every eligible retransmission decrements `N` by one. A simplified sequence without identification of successive digipeaters looks like this:

```text
WIDE2-2 -> WIDE2-1 -> WIDE2*
SP2-2   -> SP2-1   -> SP2*
```

Once the counter has been exhausted, the element may appear without `-0`, since a zero SSID is omitted in textual notation. Its H bit then marks it as used. Implementations differ in how they retain or replace an exhausted alias, so real logs will not always contain exactly the same fields.

`WIDE2-2` does not guarantee **only two transmissions across the entire network**. It permits at most two successive retransmissions *along a given branch of the packet's route*. If several digipeaters hear the original frame, each may create its own branch, increasing the total number of transmissions.

## 5. Traced paths (trace)

In a traced path, each digipeater leaves information identifying itself. This is a fundamental feature of modern `WIDEn-N` operation under the New-N Paradigm: the route followed by a received copy of a frame can be reconstructed.

An example of one `WIDE2-2` tracing branch:

```text
SQ9MDD-9>APRS,WIDE2-2:...
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...
```

In this example, the digipeaters insert their callsigns and the exhausted alias remains in the path. Another correctly configured implementation may replace the alias with the final callsign, producing a shorter final notation, such as `SR5AAA,SR5BBB*`. Log analysis must therefore account for the behavior of the particular software rather than assume that all headers have an identical form.

Tracing makes diagnosis easier, but each inserted callsign consumes another seven bytes of the address field. Long paths may run out of space for further addresses.

## 6. Untraced paths (flood)

In untraced operation, a digipeater decrements the alias counter but **does not insert its own callsign into the path**. This is not a different protocol or a special APRS information-field format. It is a way digipeaters process an address, historically associated with `UIFLOOD`.

For example, consider a network with a regional `SP` alias configured for untraced operation:

```text
SQ9MDD-9>APRS,SP2-2:...
SQ9MDD-9>APRS,SP2-1:...
SQ9MDD-9>APRS,SP2*:...
```

All three lines may represent the same frame forwarded by different stations. The final header does not reveal which digipeaters took part. A receiver hearing `SP2-1` cannot establish from the path alone which station performed the preceding hop.

Key characteristics of flood operation:

- The path does not grow by another digi callsign after every retransmission.
- The absence of a complete trace makes it harder to reconstruct the transmission route.
- Counter limits and duplicate suppression remain necessary.
- Flood does not imply unlimited broadcasting: the participating stations and their configurations determine its coverage.

### Flood with partial identification

Untraced operation does not necessarily mean that every relay identity is lost. Historical `UIFLOOD` configurations with the `ID` option allowed information about, among other things, the first and last digipeater handling a regional path to be retained. In a mixed `WIDE1-1,SSn-N` path, the first callsign may additionally come from the separately handled `WIDE1-1` element.

This arrangement **does not provide a complete trace**. The identification method and the placement of retained aliases depend on the particular TNC. A regional alias should not automatically be equated with flood: exactly the same alias can instead be configured for trace operation.

## 7. Single- and multi-element paths and fill-in digipeaters

The number of elements is the number of comma-separated path entries. The number of hops follows from their counters and processing rules. One element may request more than one retransmission:

| Path | Number of elements | Requested hops along one branch |
| --- | ---: | ---: |
| `WIDE2-1` | 1 | 1 |
| `WIDE2-2` | 1 | 2 |
| `SP2-2` | 1 | 2 |
| `WIDE1-1,WIDE2-1` | 2 | 2 |
| `WIDE1-1,WIDE2-2` | 2 | 3 |
| `SP1-1,SP2-2` | 2 | 3, if both elements are supported |

Under normal processing, the second element only becomes active after the first has been exhausted. This does not mean that each element must be handled by a different *category* of device: the configured aliases determine that.

### Origins of `WIDE1-1`: local fill-ins for mobile stations

In older APRS configurations, mobile stations used, among other options, paths beginning with `RELAY`. A nearby home station with a simple TNC configured as a **fill-in digi** could perform the first hop even without the capabilities of a full regional digipeater. This was particularly valuable for mobile and handheld stations with lower power, less effective antennas or routes through local coverage gaps. Historical paths combining `RELAY` and `WIDE`, however, contributed to excessive duplicate traffic.

The introduction of the New-N Paradigm replaced `RELAY` with **`WIDE1-1`**. This design accounted for the capabilities of existing, uncomplicated home devices, including mini-digi designs. Such a TNC did not have to understand the `WIDEn-N` algorithm or decrement its counter: recognizing the exact `WIDE1-1` alias and retransmitting the frame once, marking that element as used, was enough. A full digipeater supporting `WIDEn-N` could handle the rest of the route.

This explains the path intended for mobiles using local fill-ins:

```text
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...          # mobile transmits
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...           # home fill-in, first hop
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...      # regional digi, second hop
```

Here, `SR5AAA` represents a simple fill-in that replaces `WIDE1-1` with its own callsign, while `SR5BBB` handles the remaining `WIDE2-1`. A more capable device may also retain the used `WIDE1` alias, making the final header longer. By contrast, a simple digipeater treating `WIDE1-1` as an ordinary alias may mark it as used without changing its SSID; in that case, the H bit, not necessarily a visible `WIDE1-0`, completes the first element.

**A classic simple fill-in supporting only `WIDE1-1` should not handle `WIDE2-1` or other elements of the wider path.** Its purpose is to forward a packet once from a local coverage gap toward a regional digipeater. Deploying such relays where mobile stations already have good access to the regional network adds unnecessary frame copies to the shared channel. Different rules may apply to a modern fill-in that also bases transmission on how a frame was received and on observed traffic; that is covered below.

`WIDE1-1` is not reserved exclusively for simple fill-ins. A full regional digipeater can also handle the alias if it hears the mobile station directly. In that case, the first hop of `WIDE1-1,WIDE2-1` is used without a home station's involvement; **this does not create an additional hop beyond the two requested by that path**.

### Why `WIDE1-1` was not recommended for home stations

It is important to distinguish **a home fill-in handling the `WIDE1-1` alias** from **a home station transmitting its own beacons with `WIDE1-1` in the path**. Under the original New-N Paradigm guidance, the former primarily served mobiles, while ordinary fixed stations were expected to use `WIDEn-N` paths with hop counts suited to the region, historically often `WIDE2-2` in the conditions described by the initiative's authors. `WIDE1-1` was not designed as the default first path element for home stations.

The reason is topological. A fixed station normally has a stable location and a better antenna installation, often allowing it to reach a regional digipeater directly. Including `WIDE1-1` also activates nearby fill-ins that the station does not need, and their retransmissions can overlap the regional digipeater's transmission. Replacing `WIDE2-2` with `WIDE1-1,WIDE2-1` does not increase the allowed hop count; it simply opens the first hop to an additional group of relays.

This is not a prohibition imposed by AX.25. An exceptional fixed station in a genuine coverage gap may technically use a fill-in if justified by local topology and agreements between operators. Such an exception must, however, be distinguished from the **original purpose and recommendation**: `WIDE1-1` was introduced to support mobiles through simple local digipeaters, not as a universal path for all APRS equipment.

The `SP1-1,SP2-2` path behaves analogously in terms of element order and counters **provided that** the local network has appropriate rules for both elements. The notation `SP1-1` alone does not make the first digipeater a fill-in. With regional aliases, that role requires separate coordination between operators.

### Modern fill-ins: `direct-only` and `viscous delay`

The historical `WIDE1-1,WIDE2-1` arrangement addressed a specific problem: a simple home mini-digi recognized one alias and retransmitted the frame without being able to determine whether a larger regional digipeater had already done so. Modern software can base a retransmission decision on the frame's origin and traffic observed on the channel as well. This does not change AX.25 addressing rules; it permits more deliberate use of the available mechanisms.

Two complementary techniques are:

- **`direct-only`**: The digipeater considers only frames heard directly from the sender, not copies already retransmitted by another digi. This keeps the local relay from automatically becoming another stage of every path it encounters.
- **`viscous delay`**: The digipeater holds an eligible frame for a short, configured interval. If it hears a matching retransmission from another digipeater during that interval, it can cancel its own transmission. If it does not observe such a retransmission, it transmits the pending frame according to its rules.

These techniques are not a new path format. APRX documented the *viscous digipeater* as early as 2009, and its `directonly` mode can be combined with `viscous-delay`. The important point is not when the algorithms originated, but the option to use them instead of unconditional retransmission by simple mini-digis.

For example, a local intelligent fill-in can be **deliberately configured** to handle directly received `WIDE2-2` frames, with a delay and duplicate checks. If the regional digi retransmits the frame first and the local station hears that retransmission while waiting, the fill-in abandons its own TX. If the regional digi does not forward the frame, the local fill-in may perform the first hop, leaving `WIDE2-1` for subsequent handling. This illustrates one possible network policy, **not the default behavior of every digipeater**:

```text
SQ9MDD-9>APRS,WIDE2-2:...  # mobile transmission

# Case A: the regional digi receives the station directly
# The regional digi retransmits; the fill-in hears the copy and cancels its TX.

# Case B: the regional digi does not receive the station directly
# The fill-in hears no other copy and retransmits after the delay:
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
```

In such a network, what matters is not only **which alias was entered**, but also **whether a given digipeater really needs to transmit**. A separate `WIDE1-1` first element may then become unnecessary. It remains essential, however, where local equipment supports only that alias. Likewise, `direct-only` and `viscous delay` do not automatically grant a frame without a path permission to be retransmitted: the digi must have an appropriate addressing rule or explicitly configured special behavior.

A delay does not guarantee elimination of all duplicates. If the fill-in cannot hear the regional digipeater's transmission, it cannot infer that no retransmission took place. Delayed TX also increases delivery time, while too many similar relays may still overload the shared channel. Parameters and supported aliases must be chosen to match the network's actual topology.

This marks an important shift in approach: **the historical mobile-path recommendation was a way to work with the limitations of the infrastructure of its time, not a permanent protocol requirement**. In networks with intelligent digipeaters, retransmission policy may matter more than the traditional division between a special path for mobiles using a fill-in and a path for stations that can directly reach a regional digi. The path field nevertheless still determines which retransmissions are permitted.

## 8. Regional and event-specific aliases

A regional alias defines a *logical group of digipeaters* intended to handle specified traffic. The classic `SSn-N` concept was developed so frames could reach distant parts of a region without involving the entire neighboring `WIDEn-N` network. Naming conventions and configurations vary by country.

Examples of possible notation:

```text
SP2-2
WM2-2
```

Here, `SP` and `WM` are alias base names, not administrative boundaries automatically recognized by the protocol. They work only where operators have configured their support. Furthermore, the same alias may be processed in trace or flood mode, depending on configuration.

### Aliases for events, exercises and activities

The same mechanism can be used for a rally, communications exercises, an amateur-radio event or a temporary field network. Suppose several coordinated digipeaters handle the base alias `RAJD` in untraced mode:

```text
SQ9MDD-9>APRS,RAJD2-2:...
SQ9MDD-9>APRS,RAJD2-1:...
SQ9MDD-9>APRS,RAJD2*:...
```

This is **a design example**, not an existing, universally supported APRS alias. After the event, operators can disable `RAJD` without affecting normal `WIDEn-N` handling. If the route taken by a message needs to be traced, the same coordinated alias can instead be handled in trace mode.

A historical example of a similar use is `TEMPn-N`, described in the New-N Paradigm for temporary digipeaters deployed, among other occasions, during Field Day and emergencies. This does not mean every APRS device has the `TEMP` alias enabled by default.

Creating an alias requires agreement on at least its name, participating stations, tracing mode, permitted counter values, duplicate filtering and period of operation. Name collisions with the local network and the software's default rules should also be avoided.

**Separation by alias is logical, not radio-frequency isolation.** On the same frequency, every additional retransmission still occupies the shared channel. A digipeater configured with several aliases may also forward other frames under its remaining rules. A local alias in itself guarantees neither traffic isolation nor confidentiality.

## 9. Satellite aliases

A digipeater on a satellite or aboard the International Space Station can also be addressed through the AX.25 path field. Here, **simple aliases and explicit station callsigns** matter more than elaborate terrestrial `WIDEn-N` paths.

| Path address | Description |
| --- | --- |
| `ARISS` | Shared alias supported by the ISS and certain other satellites, depending on their current configuration. |
| `APRSAT` | Historical shared alias described in APRS materials; do not assume every satellite currently supports it. |
| `RS0ISS`, `NA1SS` | Callsigns used by the ISS station; whether they can be used as digi addresses depends on the active equipment and configuration. |
| Callsign of a specific satellite | Address specified in that relay's documentation, such as `W3ADO-1` or `PCSAT-1` for NO-44. |

Example of using a shared alias with a satellite that supports it:

```text
SQ9MDD-7>APRS,ARISS:...
```

Here, `ARISS` is one simple address. There is no reason to append a terrestrial `WIDE1-1,WIDE2-1` path without a specific justification. After a satellite retransmission, numerous ground stations and satellite gateways may receive the frame, but this does not change the meaning of the alias itself.

Historical APRS materials described shared `ARISS`, `APRSAT` and `WIDE` aliases and experiments with more elaborate satellite relaying. These are not universal present-day settings. The AMSAT list dated **7 September 2026** included `RS0ISS`, `NA1SS` and `ARISS` for the ISS, among others, as well as distinct addresses for other satellites. Before transmitting, check the current status of the specific satellite, its supported address, frequency and modulation. Listing alone does not guarantee that the service will be available during a particular pass.

## 10. Limiting duplicates and excessive retransmissions

The `N` counter limits the length of one route branch, not the total number of copies across the network. If `SR5AAA` and `SR5BBB` both hear a `WIDE2-2` frame directly, both may perform the first hop. Their copies may then be retransmitted a second time by different neighboring digipeaters. Thus, two requested hops do not mean only two RF transmissions.

Properly configured digipeaters should detect recently relayed duplicates, commonly using the source, destination and information field regardless of changes to the path. The exact algorithm, retention interval and exception rules depend on the implementation. Duplicate suppression cannot prevent every collision, however: two stations that receive the first copy simultaneously may independently decide to transmit.

Other important measures include:

- Limiting supported `n` and `N` values, including rejecting or reducing excessively long paths (*trapping*).
- Checking whether the counter is sensible, such as disallowing `WIDE1-7` under `WIDEn-N` rules.
- Avoiding retransmission by a digipeater whose callsign already appears in the used portion of the path.
- Controlling the frequency of one's own beacons and unnecessary retransmissions on the shared channel.

An excessively large counter on a regional alias can also overload the network within that region. Choosing a smaller or larger value requires knowledge of actual topology and local agreements, not just the transmitter's nominal range.

## 11. Preemptive digipeating

Normally, a digipeater handles only the first unused path element. Some implementations offer **preemptive digipeating**, which can recognize their own callsign or a special alias later in the path and modify the preceding elements appropriately.

This can be useful in deliberately engineered special-purpose networks when a frame directly reaches a station positioned further along the planned route. It must not be assumed to be standard behavior for every APRS digipeater. The outcome depends on the implementation and the selected mode for skipping earlier entries. All ordinary examples in this article assume processing without preemption.

## 12. `RFONLY`, `NOGATE` and entry into APRS-IS

The following markers may appear at the end of a path:

```text
SQ9MDD-9>APRS,WIDE2-1,RFONLY:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1,NOGATE:...
```

`RFONLY` and `NOGATE` are **markers intended for gateways**, not additional requests for retransmission. They do not increase the hop count. Their presence in the path field does not waive AX.25 limits on the number and length of addresses.

The APRS-IS specification lists them as reasons not to forward a frame from RF to the internet, but **IGate handling of both markers is optional**. Consequently, there is no guarantee that every gateway will block the packet. They do not provide radio-transmission privacy.

After a valid frame is passed to APRS-IS, the gateway adds an appropriate *q-construct*, such as `qAR` followed by its callsign or `qAO` for a receive-only gateway. These are elements of the internet header, **not part of the radio AX.25 path**. They should not be included in frames transmitted directly by an APRS radio. Further details belong to the separate topic of IGate operation.

## 13. Interpreting example frames

| Notation | What can be determined |
| --- | --- |
| `SQ9MDD-9>APRS:...` | The sender did not request digipeating. Direct reception by an IGate is still possible. |
| `SQ9MDD-9>APRS,WIDE2-1:...` | One further hop is requested through a station supporting the `WIDE2` family. |
| `SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...` | `SR5AAA` is the last used address and `WIDE2-1` remains active. |
| `SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...` | Both specified addresses have been used; the asterisk appears only on the last one. |
| `SQ9MDD-9>APRS,SP2-1:...` | One hop remains for the `SP` alias, but under flood operation the preceding digi's callsign cannot be recovered. |
| `SQ9MDD-9>APRS,SP2*:...` | The `SP2` element is exhausted; this does not establish how many copies other stations received. |
| `SQ9MDD-9>APRS,ARISS:...` | The sender specified the simple `ARISS` alias; support depends on the receiving satellite's current configuration. |
| `SQ9MDD-9>APRS,WIDE2-1,NOGATE:...` | One hop is requested, together with a marker asking for the frame not to be gated to APRS-IS. |

When analyzing packets, keep three questions separate: **what the sender placed in the path**, **how the receiving digipeater actually transformed it**, and **what the APRS-IS infrastructure added later**. Otherwise, an untraced flood path is easily mistaken for direct reception, or multiple copies of one message for successive hops along a single branch.

## Documentation and sources

- [Bob Bruninga, *Fixing the APRS Network: The New n-N Paradigm*](https://www.aprs.org/fix14439.html) - history and rules for `WIDEn-N`, `SSn-N`, `UITRACE`, `UIFLOOD` and `TEMPn-N`, as well as the original guidance for mobile, fixed and fill-in stations.
- [Bob Bruninga, *MD/VA Digipeater Plan*](https://www.aprs.org/digis/digis-md.html) - historical detailed distinction between paths recommended for mobiles needing fill-ins and those for fixed stations.
- [aprs.fi, *How APRS paths work*](https://blog.aprs.fi/2020/02/how-aprs-paths-work.html) - AX.25 path interpretation and actual `WIDEn-N` and fill-in examples.
- [APRX, *Viscous Digipeater*](https://github.com/PhirePhly/aprx/blob/master/ViscousDigipeater.README) - delaying frames, observing duplicates and canceling fill-in transmissions.
- [APRX, *aprx(8)*](https://manpages.debian.org/testing/aprx/aprx.8.en.html) - `directonly` and `viscous-delay` modes and their parameters.
- [Argent Data Systems, *Digipeater Setup*](https://argentdata.com/support/digipeater_setup/) - alias handling, duplicate suppression and preemptive digipeating in a particular implementation.
- [APRS-IS, *IGate Details*](https://www.aprs-is.net/IGateDetails.aspx) - gating rules, `NOGATE`, `RFONLY` and q-constructs.
- [AMSAT, *Live Digipeater Satellites*](https://www.amsat.org/live-digipeater-satellites/) - satellite digipeater addresses and parameters; operational data should be rechecked before use.
- [APRS-AX.25](https://wiki.sral.fi/wiki/APRS-AX.25.en) - address fields and H bits used in APRS radio frames.
