---
title: APRS-Protokollschichten
description: Funktionale Aufteilung der APRS-Übertragung über Funk und APRS-IS sowie die Aufgaben von Anwendung, AX.25, Modem und Funkkanal.
template: doc
tableOfContents: true
---

APRS definiert, wie Informationen zwischen Stationen dargestellt und interpretiert werden, legt aber nicht den gesamten Übertragungsweg fest. In einem typischen Funknetz nutzt es AX.25-Frames, ein Modem und ein Funkgerät. Im Internet werden APRS-Informationen in Textform über APRS-IS mithilfe von TCP/IP übertragen.

Die folgenden Diagramme zeigen eine **praktische Aufteilung der Funktionen** und keine formale Zuordnung zum OSI-Modell. Die einzelnen Funktionen können durch separate Geräte oder integriert in einem einzigen Funkgerät beziehungsweise Computerprogramm realisiert werden.

## Funkübertragung

![Funktionale Aufteilung der APRS-Übertragung über Funk](./_img/diagram01.png)

Bei der klassischen VHF-Übertragung werden die von der Anwendung aufbereiteten Informationen in einen AX.25-Frame eingefügt. Das Modem wandelt die digitalen Daten in ein für den Funkübertragungsweg geeignetes Signal um, das das Funkgerät auf der gewählten Frequenz aussendet.

### Benutzeranwendung

Die Anwendung erstellt Informationen für die Aussendung oder interpretiert Daten, die von anderen Stationen empfangen wurden. Sie kann Positionsmeldungen, Nachrichten, Objekte, Telemetrie und Wetterinformationen verarbeiten. Sie kann als eigenständiges Programm oder als integrierte Funktion eines Funkgeräts oder Trackers arbeiten.

### APRS-Daten

APRS definiert Informationsformate und die Regeln für ihre Interpretation. Es legt unter anderem fest, wie Positionsmeldungen, Nachrichten oder Telemetriedaten codiert werden und wie sich der Informationstyp erkennen lässt.

In einem typischen APRS-Frame befinden sich die wesentlichen Daten im *Information*-Feld des AX.25-Frames. Das bedeutet jedoch nicht, dass die übrigen Felder für APRS bedeutungslos sind. Das Protokoll verwendet auch bestimmte Elemente der AX.25-Adressierung; beim Mic-E-Format wird ein Teil der Informationen im Zieladressfeld codiert.

APRS und AX.25 erfüllen daher unterschiedliche, aber zusammenwirkende Aufgaben: AX.25 definiert die Struktur des Funk-Frames, während APRS festlegt, wie die darin übertragenen Informationen dargestellt und interpretiert und wie ausgewählte Felder des Frames genutzt werden.

### AX.25

AX.25 ist ein Sicherungsschichtprotokoll (Data Link Layer) für Packet Radio. Es definiert einen Frame, der unter anderem Quell- und Zieladressen, eine optionale Liste von Digipeater-Adressen, ein Steuerfeld, eine Protokollkennung (PID), ein *Information*-Feld und eine Frame-Prüfsequenz (FCS) enthält.

Typischer APRS-Verkehr verwendet **UI**-Frames (*Unnumbered Information*), für die zuvor keine AX.25-Verbindung aufgebaut werden muss. So kann eine einzige Aussendung von mehreren Stationen innerhalb der Reichweite empfangen werden. Die Aussendung eines UI-Frames garantiert jedoch keine Empfangsbestätigung. Mögliche Bestätigungen von APRS-Nachrichten sind ein eigenständiger Mechanismus.

### Modem und Modulation

Das Modem wandelt digitale Daten in ein für den Sende- und Empfangsweg geeignetes Signal um und führt beim Empfang die umgekehrte Operation aus. Beim klassischen APRS auf VHF wird häufig **1200 AFSK** auf Basis von Bell 202 verwendet, mit einer Datenrate von 1200 bit/s und Audiotönen von 1200 und 2200 Hz.

Das Modem kann ein eigenständiges Gerät, Teil eines TNC, eine integrierte Schaltung im Funkgerät oder ein Computerprogramm mit Soundkarte sein. AFSK ist eine Möglichkeit zur Übertragung von Frames, aber kein APRS-Datenformat.

### Funkgerät und HF-Kanal

Das Funkgerät sendet und empfängt das Funksignal. Der HF-Kanal ist ein gemeinsam genutztes Medium für Stationen, die auf einer bestimmten Frequenz arbeiten. Die Übertragungsqualität hängt unter anderem von Antennen, Sendeleistung, Ausbreitungsbedingungen, Störungen und Kanalauslastung ab.

In europäischen VHF-APRS-Netzen wird häufig die Frequenz **144,800 MHz** verwendet. Weder diese Frequenz noch 1200 AFSK definieren jedoch APRS selbst. APRS-Informationen können auch mit anderen Verfahren und auf anderen Bändern übertragen werden.

## Zusammenspiel der Schichten: Paketbeispiel

In Protokollen und Anwendungen wird ein APRS-Paket häufig in lesbarer Textform dargestellt:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

In dieser Darstellung:

| Element | Bedeutung |
| --- | --- |
| `SQ9MDD-7` | AX.25-Quelladresse. |
| `APRS` | AX.25-Zieladresse, hier gemäß den APRS-Konventionen und nicht als Adresse eines bestimmten Empfängers verwendet. |
| `WIDE1-1` | Element des Digipeater-Pfads, das in den AX.25-Adressfeldern enthalten ist. |
| `:` | Trennzeichen zwischen Kopfzeile und Informationsfeld in der Textdarstellung. |
| `!5012.34N/01956.78E>` | Inhalt des *Information*-Felds: eine unkomprimierte APRS-Positionsmeldung. Das Zeichen `!` ist der Datentypbezeichner, und das abschließende `>` kennzeichnet das Stationssymbol. |

Das Beispiel zeigt, warum nicht die gesamte sichtbare Darstellung mit dem APRS-Datenfeld gleichgesetzt werden sollte. Die Kopfzeile verwendet AX.25-Felder, denen APRS zusätzliche Bedeutungen zuweisen kann; das *Information*-Feld enthält dagegen Daten im APRS-Format.

**Die Textdarstellung ist keine wörtliche Kopie des über Funk übertragenen Frames.** Der tatsächliche AX.25-Frame enthält außerdem binär codierte Felder, die oben nicht sichtbar sind, darunter das Steuerfeld, die PID und die FCS. Das Trennzeichen `:` gehört zur Textdarstellung und nicht zur Struktur des Funk-Frames.

Der Aufbau des Frames wird im Artikel [„Anatomie eines APRS-Pakets“](../03-packet-anatomy/) genauer beschrieben.

## Übertragung über das Internet

![Funktionale Aufteilung der APRS-Übertragung über das Internet](./_img/diagram02.png)

Auf der Internetseite erzeugt oder liest die Anwendung weiterhin APRS-Informationen. Für deren Übertragung benötigt sie jedoch weder ein Funkmodem noch einen AX.25-Frame in der über HF gesendeten Form. Der Client kommuniziert über eine **TCP/IP**-Verbindung mit den **APRS-IS**-Servern.

APRS-IS verwendet eine Textdarstellung der Pakete, die die Kopfzeile und das Informationsfeld umfasst. APRS-IS-Server empfangen Pakete und verteilen sie gemäß den Betriebsregeln des Netzwerks, einschließlich der verwendeten Filter, an die entsprechenden verbundenen Clients.

**APRS-IS ist kein Internettunnel für rohe AX.25-Frames.** Stattdessen ermöglicht es die Verteilung von APRS-Informationen über einen anderen Übertragungsmechanismus. APRS-IS-Server sind Bestandteile der Infrastruktur und keine eigenständige Schicht des OSI-Modells.

## IGate: Verbindung beider Umgebungen

Ein IGate verbindet das Funknetz mit APRS-IS. Nach dem Empfang eines Pakets über HF kann es dieses in der passenden Textdarstellung an das Internetnetz weiterleiten. Dabei können APRS-IS-spezifische Informationen hinzugefügt werden, beispielsweise ein *q-construct*. Das bedeutet nicht, dass diese Informationen bereits Bestandteil des ursprünglich von der Funkstation gesendeten Frames waren.

Für den Verkehr in die Gegenrichtung gelten eigene Regeln. Ein IGate sollte nicht jedes beliebige von APRS-IS empfangene Paket als Frame behandeln, der unmittelbar über HF ausgesendet werden kann. Die genauen Weiterleitungsregeln, einschließlich des Einsatzes des Formats *third-party traffic*, gehören zur Beschreibung des IGate-Betriebs.

Diese Unterscheidung erklärt, warum ein in APRS-IS sichtbares Paket zusätzliche Elemente enthalten kann, die bei seiner Funkübertragung nicht vorhanden waren.

## Zusammenfassung

| Element | Hauptfunktion |
| --- | --- |
| Anwendung | Informationen erzeugen, empfangen und darstellen. |
| APRS | Format und Interpretation von Informationen einschließlich der Nutzung ausgewählter Adressfelder. |
| AX.25 | Struktur des Funk-Frames, Adressierung, Pfad und Fehlerprüfung. |
| Modem | Digitale Daten in das Signal des jeweiligen Übertragungsverfahrens umwandeln und umgekehrt. |
| Funkgerät und HF-Kanal | Physikalische Übertragung des Signals zwischen Stationen. |
| APRS-IS | Austausch und Verteilung von Paketen in Textdarstellung über das Internet. |
| TCP/IP | Datentransport zwischen APRS-IS-Clients und -Servern. |
| IGate | Kontrollierte Weiterleitung von Paketen zwischen Funknetz und APRS-IS. |

Entscheidend ist die Unterscheidung zwischen der **Bedeutung der Informationen** und der **Art ihrer Übertragung**. APRS definiert die Bedeutung der Daten und nutzt dabei bestimmte Mechanismen von AX.25. Über Funk werden Informationen in AX.25-Frames übertragen, während sie in APRS-IS in Textdarstellung über TCP/IP verteilt werden.

## Quellen

- [*APRS Protocol Reference*, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF), Kapitel 3–5: Nutzung von AX.25 und APRS-Datenformate.
- [*AX.25 Link Access Protocol for Amateur Packet Radio*, Version 2.2](https://tarpn.net/t/faq/files/AX25.2.2-Sep%2017-1-10Sep17.pdf): Frame-Struktur und UI-Frames.
- [*Connecting to APRS-IS*](https://www.aprs-is.net/connecting.aspx) und [*Server Design*](https://www.aprs-is.net/ServerDesign.aspx): Client-Verbindungen, Textdarstellung und Paketverteilung.
