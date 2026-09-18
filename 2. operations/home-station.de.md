---
title: APRS-Heimstation
description: So baust du eine einfache APRS-Heimstation auf, vom Paketempfang bis zum iGate oder Digipeater.
template: doc
tableOfContents: true
---

Eine APRS-Heimstation ist eine der einfachsten Möglichkeiten, APRS praktisch zu nutzen.

Sie kann zunächst nur den lokalen Funkverkehr empfangen, empfangene Pakete als iGate an APRS-IS weiterleiten und, wenn das lokale Netz dies tatsächlich benötigt, zusätzlich als Digipeater arbeiten.

## Was wird benötigt?

Eine einfache Heimstation besteht aus vier Komponenten:

1. **Ein Funkgerät für das 2-m-Band**
2. **Eine Antenne**
3. **Ein Modem oder TNC**
4. **Ein Computer mit APRS-Software**

Die APRS-Frequenz hängt von der jeweiligen Region ab. In weiten Teilen Europas, einschließlich Polen, wird für APRS im 2-m-Band **144,800 MHz FM** verwendet.

Ein typischer Aufbau kann so aussehen:

```text
Antenne
  |
Funkgerät 144.800 MHz
  |
TNC / Modem
  |
Computer
  |
Internet
```

Der Computer muss kein klassischer PC sein. Ein Raspberry Pi, ein kleiner x86-Rechner oder ein anderes stromsparendes System, das dauerhaft laufen kann, reicht in der Regel aus.

## Funkgerät

Zum Empfang von APRS ist kein Funkgerät mit integrierter APRS-Funktion erforderlich.

Ein normales FM-Funkgerät, das auf der lokalen APRS-Frequenz arbeiten kann, genügt.

Idealerweise bietet das Gerät Zugriff auf:

- Empfangsaudio,
- Sendeaudio,
- PTT-Steuerung.

Bei vielen Funkgeräten stehen diese Signale an einer DATA-, PACKET- oder Zubehörbuchse zur Verfügung.

Auch die Verwendung des Lautsprecherausgangs und Mikrofoneingangs ist möglich, erfordert jedoch meist mehr Aufmerksamkeit bei der Einstellung der Audiopegel.

## Modem oder TNC

APRS im 2-m-Band verwendet üblicherweise AFSK mit 1200 Baud.

Zum Dekodieren der Pakete wird ein Modem benötigt.

Dabei kann es sich um einen klassischen Hardware-TNC handeln. Heute werden jedoch sehr häufig Softwaremodems eingesetzt.

Eine verbreitete Lösung ist **Dire Wolf**.

Dire Wolf verwendet die Soundkarte des Computers als AFSK-Modem und kann gleichzeitig anderen APRS-Anwendungen eine KISS-TCP-Schnittstelle bereitstellen.

```text
Funkgerät
  |
Soundkarte
  |
Dire Wolf
  |
KISS TCP
  |
APRS-Software
```

## Antenne

Bei einer Heimstation ist die Antenne häufig wichtiger als die Sendeleistung.

Für den Empfang des lokalen Verkehrs reicht eine einfache 2-m-Antenne.

Soll die Station als iGate oder Digipeater arbeiten, ist eine möglichst hoch montierte Antenne mit guter Sicht auf die Umgebung sinnvoll.

Die größtmögliche Reichweite ist jedoch nicht immer ein Vorteil.

Bei APRS geht es darum, ein nützliches Funknetz aufzubauen und nicht darum, möglichst viele Stationen aus sehr großen Entfernungen zu empfangen.

## Computer

APRS-Software kann beispielsweise betrieben werden auf:

- Raspberry Pi,
- PC,
- kleinem x86-Rechner,
- Heimserver,
- anderem Linux- oder Windows-System.

Für eine Station im Dauerbetrieb eignet sich häufig ein kleiner Linux-Rechner.

Eine Beispielkonfiguration:

```text
Raspberry Pi
   |
Dire Wolf
   |
KISS TCP
   |
APRSBox
```

## Erster Schritt: nur Empfang

Am besten beginnt man mit einer reinen Empfangskonfiguration.

Stelle das Funkgerät auf die in deiner Region verwendete APRS-Frequenz ein.

In Polen und weiten Teilen Europas:

```text
144.800 MHz
FM
kein CTCSS
kein DCS
```

Verbinde das Audiosignal des Funkgeräts mit dem Computer und starte das Modem.

Wenn der Audiopegel korrekt eingestellt ist, sollten erste APRS-Pakete erscheinen, zum Beispiel:

```text
SQ9ABC-9>APRS,WIDE1-1,WIDE2-1:...
```

In dieser Phase muss noch nichts gesendet werden.

Du kannst zunächst beobachten und prüfen:

- welche Stationen empfangen werden,
- welche Digipeater in der Umgebung arbeiten,
- wie stark der lokale Kanal ausgelastet ist,
- ob der Audiopegel korrekt eingestellt ist.

## iGate hinzufügen

Der nächste Schritt kann die Einrichtung eines iGate sein.

Ein iGate empfängt Pakete über Funk und leitet sie an APRS-IS weiter.

```text
RF 144.800 MHz
      |
  Funkgerät
      |
    Modem
      |
  APRSBox
      |
   APRS-IS
```

Damit können die von deiner Station empfangenen Funkstationen auch im globalen APRS-Netz sichtbar werden.

Für die Konfiguration werden üblicherweise benötigt:

- dein Rufzeichen,
- eine Verbindung zu APRS-IS,
- ein gültiger APRS-IS-Passcode,
- ein konfigurierter KISS-Port oder eine TNC-Verbindung.

## Was ist mit dem Senden?

Der Sender muss nicht sofort aktiviert werden.

Eine sinnvolle Reihenfolge ist:

```text
Empfang
  |
Lokales Netz beobachten
  |
RX-iGate
  |
Eigener Beacon
  |
Optional TX-iGate
  |
Optional Digipeater
```

So kann man zunächst das lokale APRS-Netz kennenlernen und anschließend entscheiden, welche zusätzlichen Funktionen tatsächlich benötigt werden.

## Eigener Beacon

Soll die Station im APRS-Netz sichtbar sein, kann sie regelmäßig ihre eigene Position senden.

Eine feste Heimstation muss dies nicht besonders häufig tun, da sich ihre Position nicht verändert.

Der Beacon kann außerdem zusätzliche Informationen enthalten, zum Beispiel:

```text
PHG...
QRV 145.500 MHz
```

Auch Hinweise auf einen lokalen Repeater, eine Webseite oder andere nützliche Dienste können enthalten sein.

## iGate in Richtung RF

Einige Stationen können ausgewählte Pakete von APRS-IS zurück auf den Funkkanal übertragen.

Dies wird hauptsächlich für Nachrichten verwendet, die an lokale Stationen adressiert sind.

Diese Funktion erfordert besondere Vorsicht.

APRS-IS sollte nicht als Quelle betrachtet werden, deren Datenverkehr unbegrenzt auf RF übertragen werden kann.

Der APRS-Funkkanal besitzt nur eine begrenzte Kapazität. Daher sollte der Verkehr aus dem Internet zum Funkkanal gefiltert und auf tatsächlich benötigte Pakete beschränkt werden.

## Digipeater

Eine Heimstation kann außerdem Pakete anderer Stationen wiederholen.

Das bedeutet jedoch nicht, dass jedes iGate gleichzeitig als Digipeater arbeiten sollte.

Vor der Aktivierung sollte geprüft werden:

- ob bereits Digipeater in der Umgebung vorhanden sind,
- welche Gebiete sie abdecken,
- ob tatsächlich Versorgungslücken bestehen,
- wie stark der Kanal ausgelastet ist.

Ein zusätzlicher Digipeater in einem Gebiet mit bereits guter Versorgung kann das Netz verschlechtern statt verbessern.

## Beispiel für eine vollständige Station

Eine mögliche Konfiguration sieht so aus:

```text
2-m-Antenne
    |
Funkgerät
    |
Audiointerface + PTT
    |
Dire Wolf
    |
KISS TCP
    |
APRSBox
    |
APRS-IS
```

APRSBox kann dabei unter anderem folgende Funktionen übernehmen:

- Empfang von Paketen,
- Anzeige des lokalen Verkehrs,
- iGate,
- eigene Beacons,
- APRS-Nachrichten,
- Digipeater,
- Beobachtung von Netzwerkstatistiken.

Dies ist nicht die einzige mögliche Konfiguration.

Anstelle von Dire Wolf kann ein Hardware-TNC, VP-Digi oder ein anderes Gerät mit KISS-Schnittstelle verwendet werden.

## Wo sollte man anfangen?

Ein einfacher Weg ist:

```text
1. Antenne
2. Funkgerät auf der lokalen APRS-Frequenz
3. Dire Wolf
4. Pakete empfangen
5. APRSBox oder andere APRS-Software
6. RX-iGate
```

Erst wenn alles zuverlässig funktioniert, sollte die Sendefunktion aktiviert werden.

So kann man zunächst den lokalen APRS-Verkehr kennenlernen, ohne dem Funkkanal sofort weitere Pakete hinzuzufügen.

Eine APRS-Heimstation muss also nicht vom ersten Tag an ein umfangreicher Netzwerkknoten sein. Sie kann als einfacher Empfänger beginnen und sich später mit der Erfahrung des Operators und den Anforderungen des lokalen Netzes weiterentwickeln.
