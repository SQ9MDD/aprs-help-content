---
title: Mobile APRS-Station
description: Auswahl und Einbau der Ausrüstung für eine mobile APRS-Station.
template: doc
tableOfContents: true
---

Eine mobile APRS-Station meldet während der Fahrt die Position eines Fahrzeugs und kann APRS-Stationen sowie Nachrichten empfangen. Sie kann ein einfacher Tracker sein, der nur die Position sendet, oder ein vollständiges Funkterminal mit Display.

Entscheidend sind passend gewählte und korrekt montierte Geräte. Die Konfiguration von Paketen, Pfaden und Beaconing hängt vom lokalen Netz ab; diese Themen behandelt der Bereich Protokoll.

In Polen ist der primäre APRS-Kanal im 2-m-Band **144,800 MHz FM**.

## Woraus besteht eine Station?

Die einfachste Anlage besteht aus vier Elementen:

1. **Funkgerät oder Sender**
2. **APRS-Tracker oder TNC**
3. **GPS-Empfänger**
4. **Antenne und Stromversorgung**

Sie kann so aussehen:

```text
GPS
 |
Tracker / TNC
 |             \
 |              \ Audio + PTT
Funkgerät 144,800 MHz
 |
Fahrzeugantenne
```

Viele aktuelle APRS-Transceiver vereinen Tracker, TNC und GPS in einem Gehäuse. Dann genügen Funkgerät, Antenne und Stromversorgung für eine vollständige Station.

## Drei grundlegende Varianten

### Tracker und gewöhnliches Funkgerät

Dies ist die einfachste Konfiguration, wenn die Station vor allem ihre Position senden soll. Der Tracker empfängt GPS-Daten, erzeugt ein APRS-Paket, leitet Audiosignale an den Mikrofoneingang des Funkgeräts und schaltet PTT.

```text
GPS → Tracker → Funkgerät → Antenne
```

Eine solche Anlage ist klein, benötigt wenig Energie und kann ohne Computer arbeiten. Ihre typische Einschränkung ist der fehlende komfortable Empfang und die fehlende APRS-Nachrichtenfunktion.

### Funkgerät mit integriertem APRS

Ein Transceiver mit eigenem TNC und GPS ist die bequemste Lösung für alle, die eine Position senden und APRS ohne zusätzlichen Computer nutzen möchten. Neben der eigenen Position kann er empfangene mobile und feste Stationen, ihre Entfernung, Richtung und zuletzt empfangene Daten anzeigen.

In der Praxis erleichtert ein solches Funkgerät das Herstellen von Verbindungen. Man kann eine Station in der Nähe erkennen, ihr Rufzeichen und ihre Richtung prüfen und sie dann auf einer in einem APRS-Paket oder einer Objektinformation angegebenen Frequenz per Sprache anrufen. APRS-Objekte lassen sich genauso nutzen, etwa für Angaben zu einem lokalen Relais, seiner Frequenz oder seinem Zugangston.

Das integrierte APRS-Terminal ermöglicht in der Regel außerdem:

- APRS-Nachrichten zu empfangen und zu lesen,
- eine kurze Nachricht an eine ausgewählte Station zu senden,
- ein lokales APRS-Bulletin zu empfangen,
- empfangene Stationen und Objekte zu durchsuchen,
- grundlegende Positionsdaten ohne Starten eines Telefons anzuzeigen.

Funktionsumfang und Darstellung der Daten hängen vom Modell ab. Ein Funkdisplay ersetzt keine vollständige Karte, ist aber sehr praktisch, um schnell zu sehen, wer in der Nähe aktiv ist, oder im Stand kurze Nachrichten auszutauschen.

Vor dem Kauf sollte geprüft werden, ob das Funkgerät Folgendes bietet:

- integriertes GPS oder einen Eingang für einen externen Empfänger,
- ein AFSK-Modem mit 1200 Baud und einen TNC,
- Packet-Data-Unterstützung im 2-m-Band,
- eine Datenbuchse oder Unterstützung für ein externes TNC,
- eine Anzeige und Bedienung, die im Fahrzeug lesbar und sicher nutzbar ist.

Beispiele für APRS-fähige Transceiver sind das Handgerät **Kenwood TH-D75E/D75A** sowie die Mobilgeräte **Kenwood TM-D710G**, **Yaesu FTM-300DE/DR** und **Yaesu FTM-500DE/DR**. Diese Beispiele beschreiben Geräteklassen, keine Einkaufsliste: Einige Modelle können nicht mehr hergestellt werden; Verfügbarkeit und Bandvariante hängen vom Markt ab.

### Funkgerät, TNC und Anwendung

Ein gewöhnliches Funkgerät kann auch mit einem externen TNC, Tablet, Telefon oder kleinen Computer verbunden werden. Die Anwendung dient dann als APRS-Terminal, während das TNC digitale Daten in AFSK-Audio umsetzt und PTT steuert.

```text
GPS / Telefon
      |
APRS-Anwendung
      |
     TNC
      |
Funkgerät → Antenne
```

Diese Lösung bietet im Wesentlichen dieselben APRS-Grundfunktionen wie ein Funkgerät mit integriertem Terminal: Position, Stationsliste, Nachrichten, Bulletins und Objekte. Auf dem größeren Bildschirm kommt jedoch eine Karte hinzu, auf der sich die eigene Station, andere Nutzer, Relais und weitere APRS-Objekte sofort sehen lassen.

Sie bietet viel Flexibilität, hat aber auch mehr Kabel, Verbindungen und mögliche Fehlerquellen. Sie eignet sich besonders, wenn eine Karte, ein größerer Bildschirm oder bequemeres Schreiben von Nachrichten im Stand gebraucht wird.

## Funkgerät

Für mobiles APRS wird ein FM-Funkgerät für das 2-m-Band benötigt. Es braucht keine werksseitige APRS-Unterstützung, wenn es mit einem Tracker oder TNC zusammenarbeitet.

Bei der Auswahl sind besonders wichtig:

- Zugang zu Mikrofoneingang, Audioausgang und PTT,
- eine DATA-, PACKET- oder Zubehörbuchse, die den Anschluss eines Trackers vereinfacht,
- die Möglichkeit, eine geeignete Sendeleistung einzustellen,
- stabile 12-V-Stromversorgung,
- eine bequeme Montage und Bedienung ohne Ablenkung vom Straßenverkehr.

Fest eingebaute Mobilfunkgeräte sind meist am praktischsten, aber für einen einfachen Tracker kann auch ein Handfunkgerät verwendet werden. Dann sind Kabel, Adapter zur Zubehörbuchse und die Kühlung des Geräts beim Senden besonders wichtig.

## Tracker und TNC

Ein **Tracker** ist auf das automatische Senden einer Position spezialisiert. Er verfügt meist über einen GPS-Eingang, Audioausgang, PTT-Steuerung und einen Speicher für Einstellungen. Nicht jeder Tracker empfängt und dekodiert APRS-Verkehr; vor dem Kauf sollte klar geprüft werden, ob er nur sendet oder auch empfängt.

Ein **TNC** ist ein Packet-Radio-Modem. Es kann mit einer APRS-Anwendung oder mit Funkgerät und Computer zusammenarbeiten. Neben dem Senden ermöglicht es meist den Empfang und das Dekodieren von Paketen, der Funktionsumfang hängt jedoch vom jeweiligen Modell und der Software ab.

In mobilen Geräten findet man am häufigsten:

- einen Hardware-Tracker mit GPS oder NMEA-Eingang,
- ein externes TNC über USB, Bluetooth oder WLAN,
- ein im Funkgerät integriertes TNC,
- ein Softwaremodem auf Telefon oder Computer mit Audio-Interface.

Auf 2 m verwendet APRS üblicherweise AFSK mit 1200 Baud. Ein externer Tracker oder ein TNC muss diesen Modus und die vom Funkgerät verwendeten Audiopegel unterstützen.

## GPS

GPS kann im Funkgerät oder Tracker integriert, als separater Empfänger angeschlossen oder von einem Telefon bereitgestellt werden.

Am zuverlässigsten ist ein Empfänger, der aktuelle Positionsdaten direkt an das APRS-Gerät übermittelt. Ältere Tracker verwenden häufig NMEA-Kommunikation über eine serielle Schnittstelle. Vor dem Einbau sollten Stecker, Versorgungsspannung und Datenformat auf Kompatibilität geprüft werden.

Der Empfänger sollte einen möglichst freien Blick zum Himmel haben. Im Auto lässt er sich unter der Windschutzscheibe platzieren; alternativ wird eine GPS-Antenne an der vom Hersteller empfohlenen Stelle verwendet. Er sollte nicht durch Metallteile abgedeckt oder tief unter dem Armaturenbrett verborgen werden.

## Antenne

Bei mobilem APRS ist die Antenne oft wichtiger als hohe Sendeleistung. Eine gut montierte 2-m-Antenne verbessert sowohl die Sendereichweite als auch den Empfang von Stationen und Digipeatern.

Der übliche beste Ort ist das Fahrzeugdach. Die Metallkarosserie bildet dann eine Gegengewichtsebene für die Antenne. Eine Antenne an Kofferraumdeckel, Halterung oder Dachreling kann korrekt funktionieren, ihr Diagramm und ihre Reichweite können aber anders sein.

Bei der Montage ist zu beachten:

- eine stabile Befestigung, die Vibrationen und Wetter standhält,
- ein unbeschädigtes Koaxialkabel,
- ein wetterfestes Anschlussstück außerhalb des Fahrzeugs,
- Kabelführung ohne Einklemmen in einer Tür,
- Abstand zu anderen Antennen und metallischen Hindernissen.

Vor einer längeren Fahrt empfiehlt sich eine SWR-Prüfung. Ein hohes SWR kann auf ein Problem mit Antenne, Kabel oder Stecker hindeuten und den Sender unnötig belasten.

## Anschluss an das Funkgerät

Ein Tracker oder TNC wird über drei grundlegende Signale mit dem Funkgerät verbunden:

| Signal | Funktion |
|---|---|
| Audio zum Funkgerät | Führt die AFSK-Modulation zum Mikrofon- oder DATA-Eingang |
| Audio vom Funkgerät | Führt empfangenes Audio zum TNC, wenn die Station empfangen soll |
| PTT | Schaltet den Sender des Funkgeräts ein |

Viele Funkgeräte besitzen eine eigene DATA- oder PACKET-Buchse. Sie ist meist besser geeignet als die Mikrofonbuchse, da sie passendere Audiopegel bereitstellt und einige zusätzliche Einstellungen vermeidet.

Die Audioverbindung erfordert die Einstellung der Pegel. Ein zu niedriger Pegel kann Dekodierung oder Paketempfang verhindern; ein zu hoher Pegel verzerrt die Modulation. Die Einstellung sollte bei echtem Empfang und einer Testsendeung nach Anleitung des Geräts geprüft werden.

## Stromversorgung und Einbau im Fahrzeug

Ein Mobilfunkgerät wird am besten über ausreichend dimensionierte Leitungen und eine nahe der Stromquelle platzierte Sicherung aus der 12-V-Installation versorgt. Beim Senden zieht es deutlich mehr Strom als beim Empfang; deshalb müssen Stromleitung und Zubehörsteckdose dafür ausgelegt sein.

Tracker, TNC und GPS können 5 V, USB oder einen eigenen Spannungsregler benötigen. Es darf nicht vorausgesetzt werden, dass jede Buchse am Funkgerät ein zusätzliches Gerät versorgen kann; verfügbare Spannung und Strom müssen in der Dokumentation geprüft werden.

Geräte und Kabel müssen sicher und außerhalb der Entfaltungsbereiche von Airbags montiert sein. Display, Mikrofon und Bedienelemente dürfen weder die Sicht einschränken noch das Fahren erschweren.

## Vor der ersten Fahrt

Vor dem festen Einbau sollte die Anlage auf dem Tisch oder im stehenden Fahrzeug getestet werden. Prüfe der Reihe nach:

1. ob das Funkgerät auf dem richtigen APRS-Kanal arbeitet,
2. ob das GPS eine aktuelle Position liefert,
3. ob Tracker oder TNC PTT korrekt schalten,
4. ob der Audiopegel den Sender nicht übersteuert,
5. ob Antenne und Kabel in Ordnung sind,
6. ob die Geräte nach dem Starten des Motors stabil arbeiten.

Einstellungen für Rufzeichen, Symbol, Beaconing und Pfad richten sich nach den APRS-Regeln und der lokalen Abdeckung. Sie sollten anhand der Artikel im Protokollbereich vorgenommen werden; nach den ersten Fahrten ist zu prüfen, ob die empfangenen Positionen der tatsächlichen Route entsprechen.

## Womit beginnt man am besten?

Der einfachste Weg zu mobilem APRS ist:

```text
1. Eine 2-m-Antenne
2. Ein Funkgerät auf 144,800 MHz
3. Ein Tracker mit GPS oder ein Funkgerät mit integriertem APRS
4. Zuverlässige Stromversorgung und Montage
5. Test von Audio, PTT und Empfang
6. APRS-Konfiguration nach der lokalen Netzpraxis
```

Erst nach einem erfolgreichen Test lohnt es sich, die Station um Kartendarstellung, APRS-Nachrichten, ein zusätzliches TNC oder die Integration eines Telefons zu erweitern.
