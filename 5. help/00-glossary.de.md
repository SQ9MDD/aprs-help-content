---
title: "Glossar der Begriffe und Abkürzungen"
description: "Grundbegriffe aus Funktechnik, APRS und Paketübertragung, erklärt für den Einstieg in APRS."
sidebar:
  order: 0
---

Die APRS-Dokumentation verwendet Begriffe aus Funktechnik, Informatik und Paketdatenübertragung. Dieses Glossar erklärt Ausdrücke, die beim Lesen von Artikeln, beim Einrichten von Stationen und bei der Verkehrsanalyse vorkommen. Die Einträge sind thematisch geordnet. Abkürzungen behalten ihre ursprünglichen Langformen; die Definitionen erläutern ihre praktische Bedeutung.

## 1. Grundlagen der Funkkommunikation

**Antenne** - Bauteil zum Abstrahlen und Empfangen von Funkwellen. Bauart, Standort und Richtcharakteristik beeinflussen die Qualität der Funkverbindung.

**CTCSS (Continuous Tone-Coded Squelch System)** - selektives Öffnen der Rauschsperre durch einen kontinuierlichen niederfrequenten Ton, der zusammen mit dem Signal übertragen wird. Es bietet keine Vertraulichkeit.

**Frequenz** - Anzahl der Schwingungen einer Welle pro Sekunde, angegeben in Hertz (Hz). In Polen arbeitet klassisches APRS im 2-m-Band üblicherweise auf 144,800 MHz.

**DCS (Digital-Coded Squelch)** - selektives Öffnen der Rauschsperre mittels eines übertragenen digitalen Codes.

**Duplex** - Betriebsart mit getrennten Sende- und Empfangswegen. Vollduplex erlaubt gleichzeitiges Senden und Empfangen; Halbduplex erfordert einen Wechsel zwischen beiden Vorgängen.

**FM (Frequency Modulation)** - Frequenzmodulation, bei der die Information die momentane Frequenz eines Trägers verändert. Sie wird in analogen Funkgeräten eingesetzt, auch zur Übertragung von AFSK-Tonsignalen.

**Funkkanal** - eine bestimmte Frequenz oder eine Kombination von Betriebsparametern, beispielsweise Frequenz, Sendeart und zusätzliche Einstellungen.

**Modulation** - Veränderung eines ausgewählten Parameters des Trägersignals zur Übermittlung von Informationen.

**Sendeleistung** - vom Sender abgegebene Leistung, üblicherweise in Watt (W) angegeben. Die Leistung allein bestimmt nicht die Reichweite einer Station.

**Funkrelais** - Station, die ein Signal empfängt und erneut aussendet, um die Reichweite zu erhöhen. Sprachrelais verwenden häufig unterschiedliche Empfangs- und Sendefrequenzen.

**Ausbreitung (Propagation)** - Ausbreitung von Funkwellen. Frequenz, Gelände, Antennen und atmosphärische Bedingungen beeinflussen Signalwege und Reichweite. Günstige Ausbreitungsbedingungen ermöglichen den Empfang weit entfernter APRS-Stationen.

**PTT (Push To Talk)** - Taste oder Steuersignal, das ein Funkgerät auf Sendung schaltet. Bei computergesteuerten Stationen kann PTT über eine Hardware-Schnittstelle ausgelöst werden.

**RF (Radio Frequency)** - Hochfrequenz beziehungsweise Funkfrequenz. In APRS-Beschreibungen bezeichnet „RF-Netz“ meist den funkbasierten Teil des Systems im Gegensatz zu APRS-IS.

**RX (Receive)** - Empfang; Bezeichnung für Empfänger, Empfangsweg oder Empfangsvorgang.

**Simplex** - im strengen Sinn eine Übertragung nur in eine Richtung. Im Amateurfunk bezeichnet „Simplex-Verbindung“ häufig auch direkte, abwechselnde Kommunikation auf derselben Frequenz ohne Relais.

**Squelch (Rauschsperre)** - Schaltung, die den Empfängerton stummschaltet, wenn das Signal die eingestellten Bedingungen nicht erfüllt. Eine zu hohe Schwelle kann den Paketempfang beeinträchtigen.

**TX (Transmit)** - Senden; Bezeichnung für Sender, Sendeweg oder Sendevorgang.

**UHF (Ultra High Frequency)** - Frequenzbereich von 300 MHz bis 3 GHz, einschließlich des Amateurfunkbands 70 cm.

**VHF (Very High Frequency)** - Frequenzbereich von 30 bis 300 MHz, einschließlich des Amateurfunkbands 2 m.

**VOX (Voice Operated Exchange)** - Schaltung, die bei Erkennung eines ausreichend starken Audiosignals automatisch auf Sendung schaltet. Bei der Datenübertragung ist ihre Reaktionszeit zu beachten.

**Funkreichweite** - Gebiet, in dem eine Station empfangen werden kann. Sie hängt von Antennen, Leistung, Gelände, Störungen und Ausbreitungsbedingungen ab.

## 2. Geräte und Schnittstellen

**CAT (Computer Aided Transceiver)** - Computersteuerung eines Funkgeräts, etwa zum Ändern der Frequenz oder Auslesen von Parametern. Der Funktionsumfang hängt vom Gerät ab.

**DTR (Data Terminal Ready)** - Steuersignal einer seriellen Schnittstelle, das über eine geeignete Schaltung zur PTT-Steuerung verwendet werden kann.

**GNSS (Global Navigation Satellite System)** - Sammelbegriff für satellitengestützte Navigationssysteme, die unter anderem die Position eines APRS-Trackers bestimmen.

**GPS (Global Positioning System)** - eines der GNSS-Systeme. Umgangssprachlich wird „GPS“ auch für Empfänger verwendet, die mehrere Satellitennavigationssysteme nutzen.

**Schnittstelle (Interface)** - Verbindungsmethode oder Schaltung zwischen zusammenarbeitenden Geräten. Eine Funkschnittstelle kann Audio zwischen Computer und Funkgerät übertragen und PTT steuern.

**Soundkarte** - Gerät zur Umwandlung zwischen analogen und digitalen Signalen. Zusammen mit einem Softwaremodem ermöglicht sie AFSK-Senden und -Empfangen.

**Modem (Modulator-Demodulator)** - Gerät oder Programm, das Daten in ein für das Übertragungsmedium geeignetes Signal umwandelt und umgekehrt. Bei klassischem APRS erzeugt das AFSK-Modem Audiosignale aus Daten und dekodiert empfangene Audiosignale.

**Serielle Schnittstelle** - Schnittstelle, die Daten nacheinander Bit für Bit überträgt. Sie kann einen TNC, GNSS-Empfänger oder eine Steuerschaltung anbinden.

**Funkgerät (Transceiver)** - Gerät, das Funksender und -empfänger kombiniert. Nicht jedes Funkgerät besitzt ein eingebautes Modem oder einen TNC.

**RTS (Request To Send)** - Steuersignal einer seriellen Schnittstelle, das häufig über eine geeignete Schaltung zur PTT-Steuerung verwendet wird.

**SDR (Software Defined Radio)** - softwaredefiniertes Funkgerät, bei dem digitale Signalverarbeitung in Software einen Teil der Empfangs- oder Sendefunktionen übernimmt.

**Terminal** - Programm oder Gerät zum Datenaustausch mit einem anderen System. Im Packet Radio kann es mit einem TNC zusammenarbeiten.

**TNC (Terminal Node Controller)** - Hardware- oder Softwarecontroller für Paketkommunikation. In einer typischen AX.25-Konfiguration verarbeitet er Frames und arbeitet mit einem Modem zusammen. Nicht jedes Modem ist ein vollständiger TNC.

**UART (Universal Asynchronous Receiver-Transmitter)** - Schaltung für asynchrone serielle Kommunikation, häufig in Mikrocontrollern vorhanden.

**USB (Universal Serial Bus)** - Schnittstelle zum Anschluss unter anderem von Soundkarten, seriellen Adaptern, GNSS-Empfängern und Funkgeräten.

## 3. APRS-Grundbegriffe

**APRS (Automatic Packet Reporting System)** - System zum automatischen Informationsaustausch mittels Paketdatenübertragung. Es unterstützt unter anderem Positionen, Nachrichten, Objekte, Wetterdaten und Telemetrie.

**Beacon (Bake)** - gewöhnlich automatisch gesendetes Informationspaket, beispielsweise mit Position oder Status einer Station. Es ist kein eigenständiger APRS-Frame-Typ.

**Bulletin (Rundmeldung)** - APRS-Mitteilung für mehrere Empfänger, die im APRS-Nachrichtenformat übertragen wird.

**Kommentar** - zusätzlicher Text in bestimmten APRS-Meldungen, beispielsweise Positionsberichten.

**Objekt (Object)** - benannte APRS-Information, die gewöhnlich einen Ort oder ein Ereignis beschreibt und von einer anderen Station veröffentlicht wird. Sie kann beispielsweise ein Relais oder einen Veranstaltungsort darstellen.

**Element (Item)** - vereinfachtes Format für benannte APRS-Informationen, das sich vom Objektformat unterscheidet.

**Paket** - über ein Netz übertragene Dateneinheit. In APRS-Gesprächen werden „Paket“ und „Frame“ gelegentlich gleichbedeutend verwendet, obwohl ihre genaue Bedeutung von der Protokollschicht abhängt.

**Positionsbericht** - APRS-Daten mit geografischen Koordinaten und je nach Format auch Zeit, Symbol, Kurs, Geschwindigkeit, Höhe oder Kommentar.

**SSID (Secondary Station Identifier)** - zusätzlicher Bezeichner in einer AX.25-Adresse mit Werten von 0 bis 15. Er unterscheidet Stationen mit demselben Rufzeichen. Nicht jedes in APRS-IS vorkommende Textsuffix ist eine AX.25-SSID.

**APRS-Station** - Gerät oder Anwendung zum Austausch von APRS-Informationen, beispielsweise Tracker, Heimstation, DIGI oder IGate.

**Status** - Textinformation über Zustand oder Aktivität einer Station, übertragen im dafür vorgesehenen APRS-Format.

**APRS-Symbol** - grafische Darstellung einer Station oder eines Objekts, die in APRS-Daten festgelegt und auf Karten angezeigt wird.

**Telemetrie** - fernübertragene Messwerte oder Gerätezustände, beispielsweise Spannung, Temperatur oder digitale Signale.

**Tracker** - Gerät oder Anwendung, das beziehungsweise die automatisch die eigene Position veröffentlicht, meist anhand eines GNSS-Empfängers.

**APRS-Nachricht** - kurze Textmitteilung in einem festgelegten APRS-Format. Adressierte Nachrichten können Kennungen und Empfangsbestätigungen verwenden.

**Rufzeichen (Callsign)** - gemäß geltenden Vorschriften zugeteilter Bezeichner einer Funkstation; im Amateurfunk-APRS bildet er die Grundlage der Adressierung.

## 4. Komponenten des APRS-Netzes

**APRS-IS (APRS Internet System)** - Internetinfrastruktur für den Austausch von APRS-Daten zwischen Clients, IGates und Servern.

**DIGI (Digipeater, Digital Repeater)** - digitale Relaisstation, die Funkpakete empfängt und entsprechend den Pfadregeln und ihrer Konfiguration erneut aussendet.

**Doppeltes Paket (Duplikat)** - weitere Kopie eines bereits empfangenen Pakets. Sie kann entstehen, wenn mehrere Stationen dieselbe Aussendung empfangen und weiterleiten.

**Hop (Sprung)** - ein einzelner Weiterleitungsschritt eines Pakets. Bei Funk-APRS bezeichnet er gewöhnlich eine erneute Aussendung durch einen Digipeater.

**IGate (Internet Gateway)** - Gateway zwischen dem funkbasierten APRS-Netz und APRS-IS. Es leitet über Funk empfangene Pakete ins Internet weiter; ein bidirektionales Gateway kann außerdem ausgewählte APRS-IS-Daten über Funk aussenden.

**APRS-Client** - Anwendung oder Gerät, das APRS-Daten über ein unterstütztes Medium empfängt, darstellt oder sendet.

**Erneute Aussendung (Retransmission)** - wiederholtes Aussenden eines Pakets, beispielsweise durch einen Digipeater gemäß den Weiterleitungsregeln.

**APRS-IS-Server** - Server, der APRS-Daten über das Internet verteilt, Clients bedient und je nach Rolle Verbindungen zu anderen Servern unterhält.

**APRS-Pfad (Path)** - Adressfeld, das Stationen oder Aliase für die Weiterleitung eines Pakets über Funk angibt.

**WIDE1-1** - verbreiteter Pfadalias, der eine erneute Aussendung durch einen entsprechend konfigurierten Digipeater ermöglicht.

**WIDE2-2** - WIDEn-N-Alias, dessen anfänglicher Zähler zwei Weiterleitungsschritte durch kompatible Digipeater zulässt. Er garantiert nicht, dass das Paket tatsächlich zweimal wiederholt wird.

## 5. Datenübertragung und Protokolle

**AFSK (Audio Frequency-Shift Keying)** - Darstellung von Daten durch wechselnde Frequenzen eines Audiosignals. Klassisches APRS mit 1200 Baud verwendet Bell-202-kompatibles AFSK.

**ALOHA** - Zugriffsverfahren für gemeinsam genutzte Medien, bei dem Stationen ohne zentral zugewiesene Zeitschlitze zu senden versuchen. Die gemeinsame Kanalnutzung und die fehlende Zustellgarantie bei Funk-APRS ermöglichen Kollisionen.

**AX.25** - Paketübertragungsprotokoll für den Amateurfunk, das unter anderem Adressierung und Frame-Struktur definiert. APRS verwendet überwiegend UI-Frames.

**Baud** - Einheit der Modulationsgeschwindigkeit in Symbolen pro Sekunde. Sie entspricht nicht immer der Bitrate.

**Bit/s (bps)** - Anzahl der pro Sekunde übertragenen Bits.

**CRC (Cyclic Redundancy Check)** - Verfahren zur Berechnung eines Prüfwerts, mit dem Fehler in übertragenen Daten erkannt werden können.

**DTI (Data Type Identifier)** - Datentypkennung, gewöhnlich das erste Zeichen des APRS-Informationsfelds, die bestimmt, wie dessen Inhalt interpretiert wird.

**FCS (Frame Check Sequence)** - Frame-Prüfsequenz. In AX.25 wird CRC zur Erkennung von Übertragungsfehlern verwendet.

**FEC (Forward Error Correction)** - Fehlerkorrektur mithilfe zusätzlicher Daten, die zusammen mit der Information übertragen werden, ohne eine erneute Übertragung zu benötigen.

**FSK (Frequency-Shift Keying)** - Modulation, bei der Symbole durch unterschiedliche Signalfrequenzen dargestellt werden.

**FX.25** - AX.25-Erweiterung mit FEC. Sie kann einen Teil beschädigter Übertragungen wiederherstellen, wenn der Empfänger FX.25 unterstützt.

**KISS (Keep It Simple, Stupid)** - einfaches Protokoll zwischen Anwendung und TNC zur Übertragung von Frames und ausgewählten Steuerbefehlen. Eine KISS-Schnittstelle allein garantiert keine vollständige AX.25-Unterstützung des Geräts.

**Mic-E** - kompaktes APRS-Format, bei dem ein Teil der Positions- und Statusinformationen auch im AX.25-Adressfeld codiert wird.

**Payload (Nutzdaten)** - Daten, die auf einer bestimmten Protokollschicht transportiert werden. In APRS-Beschreibungen ist damit häufig der Inhalt des Informationsfelds eines Frames gemeint.

**Frame (Rahmen)** - Dateneinheit der Sicherungsschicht. Ein AX.25-Frame enthält unter anderem Adressen, Steuerfeld, Informationsfeld und Prüfsequenz.

**TCP/IP** - Familie von Netzwerkprotokollen, die unter anderem zur Kommunikation zwischen Clients und APRS-IS verwendet wird.

**TOCALL** - gebräuchliche Bezeichnung für das APRS-Zieladressfeld, dessen Werte häufig die sendende Software oder das Gerät kennzeichnen. Nicht jeder Zieladresswert ist eine Produktkennung.

**UI Frame (Unnumbered Information Frame)** - AX.25-Frame zur Datenübertragung ohne Verbindungsaufbau und ohne Bestätigung jedes einzelnen Frames auf Sicherungsschicht. Er bildet die Grundlage des klassischen APRS.

## 6. Stationsbetrieb und Konfiguration

**APRS Passcode** - Code für die traditionelle APRS-IS-Anmeldung. Er wird aus dem Rufzeichen berechnet und bietet keinen starken kryptografischen Schutz.

**DCD (Data Carrier Detect)** - Signal oder Mechanismus zur Erkennung einer laufenden Datenübertragung, unter anderem zur Beurteilung der Kanalbelegung.

**APRS-IS-Filter** - Regeln zur Begrenzung der an einen Client gelieferten Daten, beispielsweise nach Standort oder Rufzeichen.

**Host** - Computer oder Gerät, das einen Dienst bereitstellt, beispielsweise einen KISS-TCP-Server.

**KISS Serial** - Übertragung von KISS-Frames und -Befehlen über eine serielle Schnittstelle.

**KISS TCP** - Übertragung von KISS-Daten über eine TCP-Verbindung, sodass eine Anwendung über ein Computernetz mit einem TNC kommunizieren kann.

**TCP-Port** - Nummer zur Kennzeichnung eines TCP-Dienstes auf einem Gerät. Die Portnummer hängt von der Dienstkonfiguration ab.

**Proportional Pathing** - Verfahren, bei dem aufeinanderfolgende Positionsberichte mit unterschiedlichen Pfaden gesendet werden. Längere Weiterleitungspfade werden seltener verwendet, um die Netzbelastung zu verringern.

**q-construct** - spezielles Element, das der textuellen Paketdarstellung in APRS-IS hinzugefügt wird. Es liefert Informationen darüber, wie das Paket ins Internetnetz gelangte oder dort weitergeleitet wurde. Es ist kein AX.25-Funkweiterleitungspfad.

**SmartBeaconing** - Verfahren zur Anpassung der Häufigkeit von Positionsmeldungen an die Bewegung einer Station, insbesondere an Geschwindigkeit und Richtungsänderungen.

**TX Delay** - Zeitspanne, damit Sender und Empfangsweg der Gegenstation bereit sind, bevor die eigentlichen Frame-Daten übertragen werden. Die genaue Bedeutung der Einstellung hängt vom Modem oder TNC ab.

**TX Tail** - zusätzliche Zeit, während der nach dem Ende der eigentlichen Daten weiter gesendet wird, sofern das Modem oder der TNC dies vorsieht.

## 7. Diagnose und Betrieb

**Puffer** - Speicherbereich, in dem Daten vor der weiteren Verarbeitung oder Übertragung zwischengespeichert werden.

**Duplikate** - mehrere Kopien derselben Information. Bei der Diagnose ist mehrfacher Empfang einer Aussendung von einer erneuten Übertragung durch die Quellstation zu unterscheiden.

**Paketkollision** - Überlagerung von Aussendungen, die eine korrekte Decodierung verhindert oder erschwert.

**Warteschlange** - Mechanismus, der Pakete bis zur Verarbeitung oder Aussendung aufbewahrt.

**Log (Protokolldatei)** - chronologische Ereignisaufzeichnung zur Analyse des Stationsbetriebs und zur Fehlersuche.

**Paketmonitor** - Werkzeug zur Anzeige empfangener oder gesendeter Frames mit Adressen, Pfaden und Inhalten.

**Latenz** - Zeit zwischen bestimmten Schritten der Paketverarbeitung oder -übertragung.

**Audiopegel** - Signalpegel am Eingang eines Modems oder Senders. Zu niedrige oder zu hohe Pegel können Decodierungsprobleme verursachen.

**Übersteuerung** - Signalverzerrung durch Überschreiten des zulässigen Pegels in einer Verarbeitungsstufe.

**RSSI (Received Signal Strength Indicator)** - Anzeige der empfangenen Funksignalstärke. Skala und Messverfahren hängen vom Gerät ab.

**SNR (Signal-to-Noise Ratio)** - Verhältnis zwischen Nutzsignalleistung und Rauschleistung, gewöhnlich in Dezibel angegeben.

**Kanalbelegung** - Zustand, in dem eine laufende Übertragung den Kanal nutzt. Ein TNC kann die Belegung vor dem Senden erkennen.

## 8. Begriffe, die nicht verwechselt werden sollten

**Modem und TNC** - ein Modem wandelt zwischen Signalen und Daten um. Ein TNC verarbeitet Paketkommunikation, beispielsweise AX.25-Frames, und kann ein Modem enthalten oder mit ihm zusammenarbeiten.

**DIGI und IGate** - ein DIGI sendet Pakete über Funk erneut aus. Ein IGate verbindet das Funknetz mit APRS-IS. Ein Gerät kann beide Funktionen übernehmen, sie bleiben jedoch getrennte Rollen.

**Baud und Bit/s** - Baud zählt Symbole pro Sekunde, Bit/s zählt Bits pro Sekunde. Bei einem Bit pro Symbol können die Zahlenwerte übereinstimmen.

**GPS und GNSS** - GPS ist eines der GNSS-Systeme. Ein Mehrsystemempfänger kann auch andere Satellitenkonstellationen nutzen.

**SSID und Textsuffix** - eine AX.25-SSID liegt zwischen 0 und 15. Andere Suffixe in textuellen Internetkennungen werden dadurch nicht zu AX.25-SSIDs und eignen sich möglicherweise nicht für die Weiterleitung über Funk.

**APRS und APRS-IS** - APRS beschreibt ein Informationsaustauschsystem, das verschiedene Übertragungsmedien nutzen kann. APRS-IS ist dessen internetbasierte Infrastruktur zur Datenverteilung.
