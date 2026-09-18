---
title: Dire Wolf
description: Softwarebasierter AX.25-Modem und TNC für APRS mit AFSK, KISS, FX.25, Digipeating und IGate-Unterstützung.
template: doc
tableOfContents: true
---

**Dire Wolf** ist ein softwarebasierter Packet-Radio-Modem, TNC sowie AX.25-Encoder und -Decoder, der die Audio-Schnittstelle eines Computers und digitale Signalverarbeitung nutzt.

Damit können AX.25-Frames ohne klassisches Hardware-TNC empfangen und gesendet werden. Dire Wolf kann eigenständig arbeiten oder als Funkschicht für andere APRS-Anwendungen dienen.

Offizielles Projekt-Repository:

https://github.com/wb2osz/direwolf

## Wofür Dire Wolf verwendet wird

In der einfachsten Anwendung verwandelt Dire Wolf einen Computer mit Audio-Schnittstelle in einen Packet-Radio-Modem.

```text
Funkgerät <-> Audio/PTT <-> Dire Wolf <-> KISS/AGW <-> APRS-Anwendung
```

Das Programm empfängt das Audiosignal des Funkgeräts, demoduliert es und dekodiert AX.25-Frames. In Gegenrichtung erzeugt es das Sendesignal und kann die PTT des Funkgeräts steuern.

Dire Wolf kann unter anderem eingesetzt werden als:

- Software-TNC,
- AX.25-Encoder und -Decoder,
- APRS-Decoder,
- KISS-Modem für andere Anwendungen,
- APRS-Digipeater,
- IGate,
- Tracker und Beacon-Generator,
- APRStt-Gateway.

## Hohe Dekodierleistung

Eine der wichtigsten Stärken von Dire Wolf ist die sehr gute Dekodierleistung bei AX.25-Frames.

Es handelt sich nicht nur um einen Software-Ersatz für ein älteres Hardware-TNC. Dire Wolf verwendet digitale Signalverarbeitung und mehrere Verfahren, die den Empfang unter realen Funkbedingungen verbessern.

Abhängig von der Konfiguration können unter anderem verwendet werden:

- verschiedene Demodulatorprofile,
- digitale Filter,
- automatische Pegelanpassung zwischen Mark und Space,
- mehrere parallele Demodulatoren,
- mehrere Entscheidungspunkte,
- parallele Dekodierversuche desselben Signals mit unterschiedlichen Methoden.

Das ist besonders bei schwachen Signalen, nicht idealen Audiowegen, unterschiedlichen Funkgerätecharakteristiken und im HF-Betrieb hilfreich.

Dire Wolf wurde außerdem mit klassischen TNCs unter Verwendung von Testmaterial wie der WA8LMF TNC Test CD verglichen. In der Praxis kann es einige Frames korrekt dekodieren, bei denen einfachere oder ältere TNC-Lösungen scheitern.

Ein guter Funkweg bleibt trotzdem wichtig. Richtige Audiopegel, ausreichende Bandbreite, Signal-Rausch-Abstand und Empfängerqualität spielen weiterhin eine große Rolle.

## 1200 bit/s AFSK

Der am häufigsten verwendete APRS-Modus auf VHF und UHF ist:

```text
MODEM 1200
```

Typische AFSK-Töne sind 1200 Hz und 2200 Hz.

Dies ist der Standardmodus für klassisches APRS im 2-m-Band und funktioniert normalerweise über gewöhnliche Audioanschlüsse des Funkgeräts.

## 300 bit/s AFSK auf HF

Dire Wolf unterstützt auch 300 bit/s AFSK, hauptsächlich für Packet Radio und APRS auf HF SSB.

```text
MODEM 300
```

Die Standardtöne sind 1600 Hz und 1800 Hz.

Auf HF ist die Möglichkeit besonders nützlich, mehrere Demodulatoren parallel mit leicht versetzten Frequenzen zu verwenden. Dadurch lassen sich kleine Abstimmungsunterschiede zwischen Stationen und Veränderungen im SSB-Signalweg besser tolerieren.

Beispiel einer erweiterten Konfiguration:

```text
MODEM 300 1600:1800 7@30 /4
```

Die konkreten Parameter sollten an den jeweiligen Funkweg und die verwendete Dire-Wolf-Version angepasst werden.

## 2400 und 4800 bit/s PSK

Dire Wolf unterstützt außerdem PSK-Modi mit 2400 und 4800 bit/s.

Sie sind deutlich seltener als klassisches 1200-AFSK, können aber für speziellere Packet-Radio-Anwendungen eingesetzt werden.

## 9600 bit/s

Dire Wolf unterstützt auch 9600 bit/s im G3RUH-Stil.

Dies sollte nicht einfach als schnellere Variante von 1200 AFSK betrachtet werden. Ein normaler Lautsprecherausgang und Mikrofoneingang bieten wegen Filterung, Preemphasis und Deemphasis in der Regel keinen geeigneten Signalweg.

In der Praxis wird ein ausreichend breitbandiger und möglichst direkter Datenweg im Funkgerät benötigt, beispielsweise ein 9600-Packet-, Discriminator- oder Direct-Audio-Anschluss.

Für eine typische APRS-Station mit normaler Audioverbindung bleibt 1200 AFSK daher die praktischste Lösung.

## FX.25

Dire Wolf unterstützt auch **FX.25**, eine AX.25-Erweiterung mit Vorwärtsfehlerkorrektur.

FX.25 fügt der Übertragung Reed-Solomon-Fehlerkorrekturdaten hinzu. Dadurch kann ein FX.25-fähiger Empfänger unter bestimmten Bedingungen einen gültigen Frame trotz Übertragungsfehlern wiederherstellen.

Eine wichtige Eigenschaft von FX.25 ist die Kompatibilität mit normalem AX.25.

Der Empfang von FX.25 ist in Dire Wolf automatisch aktiviert. Die Übertragung von FX.25 muss bewusst eingeschaltet werden.

Beispiel:

```text
FX25TX 1
```

Je nach Version kann auch eine bestimmte Anzahl von Paritätsbytes ausgewählt werden, beispielsweise 16, 32 oder 64.

FX.25 erhöht die über Funk übertragene Datenmenge und ist daher nicht immer für einen stark belegten APRS-Kanal geeignet. Für Packet Radio und schwierigere Funkbedingungen ist es jedoch eine interessante Möglichkeit.

## Sehr wichtig: FIX_BITS

Dire Wolf enthält einen Mechanismus namens `FIX_BITS`, der versucht, einen AX.25-Frame mit ungültigem FCS wiederherzustellen.

Dabei können Bits im empfangenen Frame verändert und anschließend geprüft werden, ob daraus ein gültiges FCS entsteht.

Für Packet-Radio-Experimente kann das interessant sein, in APRS-Infrastruktur ist jedoch besondere Vorsicht erforderlich.

Ein Frame mit ungültigem FCS wurde fehlerhaft empfangen. Ein Reparaturversuch kann dazu führen, dass veränderte Daten weitergegeben werden und nachgelagerte Anwendungen sie nicht mehr von einem korrekt empfangenen Frame unterscheiden können.

Ein einziges verändertes Bit kann betreffen:

- Rufzeichen,
- SSID,
- Pfad,
- Position,
- Nachricht,
- Telemetrie,
- Nachrichtenkennung,
- jeden anderen Teil des APRS-Payloads.

Für typische APRS-Infrastruktur, besonders wenn Dire Wolf als Modem für einen IGate oder Digipeater dient, sollte dieser Mechanismus ausdrücklich deaktiviert werden:

```text
FIX_BITS 0
```

Damit gilt eine einfache Regel: Es werden nur Frames weitergegeben, die tatsächlich mit gültigem FCS empfangen wurden.

### FIX_BITS und FX.25

`FIX_BITS` und FX.25 sind unterschiedliche Mechanismen.

FX.25 nutzt zusätzliche FEC-Informationen, die von der sendenden Station bewusst übertragen wurden. Der Empfänger besitzt dadurch mathematische Redundanz zur Korrektur einer bestimmten Anzahl von Fehlern.

`FIX_BITS` sucht dagegen nach Bitänderungen in einem bereits beschädigten normalen AX.25-Frame, die zu einem gültigen FCS führen.

Daher kann gleichzeitig:

```text
FIX_BITS 0
```

verwendet werden und dennoch können FX.25-Frames empfangen und korrigiert werden.

## KISS und andere Anwendungen

Dire Wolf kann als virtuelles TNC arbeiten und empfangene Frames anderen Anwendungen bereitstellen.

Unterstützt werden unter anderem:

- KISS über TCP,
- KISS über serielle Schnittstelle oder Pseudo-Terminal,
- die AGW-Netzwerkschnittstelle.

Eine typische Architektur kann so aussehen:

```text
Funkgerät
  |
  | Audio + PTT
  v
Dire Wolf
  |
  | KISS TCP
  v
APRSBox / YAAC / Xastir / andere Anwendung
```

Dire Wolf kann sich damit ausschließlich um Funk und AX.25 kümmern, während die APRS-Logik in einer anderen Anwendung liegt.

Der Standardport für KISS TCP ist üblicherweise:

```text
8001
```

## Digipeater

Dire Wolf unterstützt APRS-Digipeating.

Es kann Frames vom Funkkanal empfangen, deren Pfad auswerten und Pakete entsprechend den konfigurierten Regeln erneut aussenden.

Vor dem Betrieb eines Digipeaters sollte die Funktionsweise von APRS-Pfaden verstanden werden, insbesondere `WIDE1-1`, `WIDE2-1` sowie die Notwendigkeit, unnötige Wiederholungen zu vermeiden.

Ein falsch konfigurierter Digipeater kann die Kanalbelegung deutlich erhöhen.

## IGate

Dire Wolf kann auch als Gateway zwischen dem APRS-Funknetz und APRS-IS arbeiten.

Die einfachste Richtung ist:

```text
RF -> Dire Wolf -> APRS-IS
```

Dire Wolf enthält außerdem Funktionen für die Übertragung ausgewählter Informationen von APRS-IS auf RF.

Der Betrieb APRS-IS -> RF erfordert besondere Sorgfalt. Internetverkehr darf nicht ohne geeignete Einschränkungen auf den Funkkanal übertragen werden.

## PTT-Steuerung

Für das Senden muss normalerweise der Sender des Funkgeräts gesteuert werden.

Dire Wolf unterstützt mehrere PTT-Verfahren, darunter:

- RTS und DTR einer seriellen Schnittstelle,
- GPIO,
- Schnittstellen auf Basis von CM108/CM119,
- Hamlib,
- ausgewählte spezielle Funkinterfaces.

Beispiel mit RTS:

```text
PTT /dev/ttyUSB0 RTS
```

Je nach Interface kann eine invertierte Polarität erforderlich sein.

## Audio-Schnittstellen und SDR

Dire Wolf verwendet Standard-Audiogeräte des Betriebssystems.

Es kann unter anderem arbeiten mit:

- eingebauten Soundkarten,
- USB-Audioadaptern,
- Funkinterfaces mit integriertem Audiocodec,
- virtuellen Audiogeräten,
- SDR-Empfängern.

Beispiel für einen SDR-Empfangsweg:

```text
RTL-SDR
   |
   v
rtl_fm / andere SDR-Software
   |
   v
Dire Wolf
   |
   v
KISS / APRS
```

Damit lässt sich ein einfacher APRS-Empfänger oder RX-only-IGate aufbauen.

## Dauerbetrieb

Dire Wolf eignet sich gut für Installationen im Dauerbetrieb.

Es kann auf kleinen Computern wie Raspberry Pi als Modem, TNC, IGate, Digipeater oder Funkschicht für eine andere Anwendung laufen.

Dies ist eine eigenständige Stärke und nicht mit der hohen Leistung der Demodulatoren gleichzusetzen.

## Beispiel einer Basiskonfiguration

Eine stark vereinfachte Konfiguration für eine typische APRS-Station mit 1200 bit/s kann so aussehen:

```text
ADEVICE plughw:0,0

CHANNEL 0
MYCALL SQ9ABC

MODEM 1200

FIX_BITS 0
```

In einer realen Installation müssen zusätzlich das richtige Audiogerät, Signalpegel, PTT, KISS-Schnittstellen und stationsspezifische Funktionen konfiguriert werden.

Vor dem Aktivieren des Senders sollte zunächst der Empfang eingerichtet und die Dekodierqualität geprüft werden.

## Diagnose

Dire Wolf zeigt beim Start und Empfang viele nützliche Informationen im Terminal an.

Angezeigt werden können unter anderem:

- empfangene AX.25-Frames,
- dekodierte APRS-Inhalte,
- Audiopegel,
- Informationen zum Empfangskanal,
- Informationen zum Demodulator,
- Diagnosemeldungen.

Dadurch ist Dire Wolf auch sehr nützlich zum Einstellen des Audiowegs und zur Fehlersuche beim Empfang.

## Dokumentation

Wichtige Quellen:

- Projekt-Repository: https://github.com/wb2osz/direwolf
- Projektdokumentation: https://github.com/wb2osz/direwolf/tree/master/doc
- zusätzliche Dokumentation: https://github.com/wb2osz/direwolf-doc
- Beispielkonfiguration: https://github.com/wb2osz/direwolf/blob/master/conf/generic.conf

Bei der Konfiguration einer bestimmten Dire-Wolf-Version sollte die dazu passende Dokumentation verwendet werden.
