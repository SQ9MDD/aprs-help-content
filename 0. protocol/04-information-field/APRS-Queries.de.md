---
title: "APRS-Abfragen"
---

Eine APRS-Query ist ein Abfragemechanismus, mit dem eine Station eine andere Station, einen Server oder einen APRS-Dienst gezielt nach bestimmten Informationen oder einer definierten Antwort fragen kann. Anstatt darauf zu warten, dass die gewünschten Daten irgendwann im normalen APRS-Verkehr erscheinen, kann der Benutzer sie bei Bedarf aktiv anfordern.

Queries sind besonders nützlich, weil APRS im Wesentlichen ein Broadcast-orientiertes System ist. Stationen senden regelmäßig Positionen, Statusmeldungen, Wetterdaten, Objekte, Telemetrie und andere Informationen. Das bedeutet jedoch nicht, dass jede gewünschte Information genau in dem Moment verfügbar ist, in dem sie benötigt wird. Mit einer Query kann eine Information gezielt angefordert werden, ohne die Beacon-Rate des gesamten Netzes erhöhen zu müssen.

Typische Beispiele sind die Abfrage der aktuellen Position einer Station, ihres Status, der Softwareversion, einer Liste direkt gehörter Stationen oder von Informationen über den Betrieb eines IGATE. Andere Queries richten sich nicht an eine einzelne Station, sondern an Dienste innerhalb des APRS-Ökosystems, zum Beispiel Objektserver, Verzeichnisse, Gruppendienste, Rufzeichensuchdienste, Wetterdienste oder Gateways zu anderen Kommunikationssystemen.

Grundsätzlich gibt es zwei Arten von APRS-Queries:

allgemeine Queries, die an alle Stationen oder an eine bestimmte Gruppe von Stationen gerichtet sind,
gerichtete Queries, die als APRS-Nachricht an ein bestimmtes Rufzeichen oder einen bestimmten Dienst gesendet werden.

Eine Query beginnt normalerweise mit dem Zeichen ?, gefolgt von einer Kennung für den Abfragetyp. Welche Antwort zurückkommt, hängt davon ab, welche Funktionen die empfangende Station oder der betreffende Dienst unterstützt. Nicht jede APRS-Implementierung muss jede Query beherrschen, daher können verschiedene Programme und Geräte unterschiedliche Befehlssätze anbieten.

Ein wichtiges Merkmal von Queries ist, dass sie als einmalige Informationsanforderungen gedacht sind. Sie dienen nicht dazu, eine verbindungsorientierte Sitzung wie beim klassischen Packet Radio aufzubauen. Eine Station sendet eine Query, und wenn die Gegenseite diese erkennt und unterstützt, antwortet sie mit einem passenden Positionsbericht, Status, einer Nachricht oder einem anderen APRS-Paket.

Queries sollten außerdem nicht mit normalen APRS-Nachrichten verwechselt werden, die eine Bestätigung erfordern. Standardmäßige APRS-Queries enthalten keine Message-ID für das ACK-Verfahren, und auch die Antworten sollten keinen zusätzlichen Bestätigungsverkehr erzeugen. Dadurch führt eine einfache Informationsanfrage nicht zu einer unnötigen Folge weiterer Pakete.

In der Praxis werden Queries eingesetzt, wenn bestimmte Informationen nur bei Bedarf benötigt werden und nicht ständig ausgesendet werden müssen. Anstatt beispielsweise die Sendehäufigkeit von Statusinformationen oder Heard-Listen zu erhöhen, können solche Daten genau dann abgefragt werden, wenn sie tatsächlich gebraucht werden. Das passt sehr gut zur APRS-Philosophie, insbesondere auf Funkkanälen, auf denen unnötiger Verkehr vermieden und die verfügbare Kapazität möglichst effizient genutzt werden soll.

Das moderne APRS-Ökosystem hat das ursprüngliche Query-Konzept deutlich erweitert. Deshalb enthält dieses Dokument nicht nur klassische Befehle wie ?APRSP, ?APRSS oder ?APRSD, sondern auch Abfragen an Dienste wie ANSRVR, QRU, AVRS, WLNK-1, WHO-IS, WXBOT oder WHERE-IS. Sie alle folgen demselben Grundprinzip: Eine APRS-Station sendet eine kurze Anfrage, und die Gegenseite liefert eine bestimmte Information zurück oder führt eine definierte Aktion aus.

## Allgemeine Hinweise

- UI-View unterscheidet **Groß- und Kleinschreibung**.
- Bei Abfragen mit einem Rufzeichen sollte die **SSID verwendet werden, sofern vorhanden**.
- Diese Datei wurde **ausschließlich auf Grundlage des bereitgestellten PDFs** erstellt, ohne weitere Quellen zu verwenden.

---
## 1. Standard-APRS-Abfragen
| Eingabe | An | Bedeutung / Antwort | Beispiel |
|---|---|---|---|
| `?APRS?` | `callsign` | Anfrage nach einer Liste aller Abfragen, die die Station bzw. das System beantworten kann | `?APRS?` |
| `?WX?` | `CQ` | Anfrage an alle Wetterstationen | `?WX?` |
| `!PROGRAMNAME.EXE` | `callsign` | UI-View kann ein Programm aus dem Verzeichnis RCOMMAND starten, z. B. `!Orbitron.exe` oder `!Orbitron` | `!help` |
| `?ABOUT` | `callsign` | Softwareversion der Station, Betriebssystem, CPU-Auslastung, entspricht `?APRSV` und `?VER` | `?APRST APRSIS32 Win v6.1 b7601 p2 9.1/6.4%` |
| `?APRSD` | `callsign` | Direkt gehörte Stationen, ohne Digipeater-Hops | `?APRSD` |
| `?APRSH` | `callsign` | Gehörte Stationen zusammen mit dem Typ des Paketpfads: `I`, `G`, `D`, `d`, `R` | `?APRSH` |
| `?APRSH DF8LS-9` | `callsign` | Ob die Station das angegebene Rufzeichen gehört hat, die Antwort enthält Statistiken der letzten 18 Stunden | `?APRSH DF8LS-9` |
| `?APRSL` | `callsign` | Lokal in den letzten 30 Minuten gehörte Stationen, maximal 2 Hops | `?APRSL` |
| `?APRSM` | `callsign` | Message Query, fragt, ob die Station Nachrichten für mich in der Warteschlange hat | `?APRSM` |
| `?APRSO` | `callsign` | Object Query, sendet aktive, von der Station erzeugte Objekte, ausgenommen QRU-Server-Objekte | `?APRSO` |
| `?APRSP` | `callsign` | Position Query, löst normalerweise ein Beacon bei der empfangenden Station aus | `?APRSP` |
| `?APRSS` | `callsign` | Status Query, z. B. Status der APRSIS32-Ports | `?APRSS` |
| `?APRST` | `callsign` | Spur der Stationspakete zur APRS-IS-Internetschnittstelle oder über Funk, entspricht `?PING?` | `?APRST` |
| `?APRSV` | `callsign` | Softwareversion, System, CPU, entspricht `?ABOUT` und `?VER` | `?APRSV` |
| `?CPU` | `callsign` | Softwareversion, System, Kernel- und Benutzer-CPU-Auslastung | `?CPU` |
| `?DX` | `callsign` | Kurzform eines DX-Berichts, die am weitesten entfernte in der letzten Stunde empfangene Station | `?DX` |
| `?IGATE` | `callsign` | Aktivität an IGATE-Ports, in APRSIS32 zusätzlich Information über bidirektionalen Betrieb | `?IGATE` |
| `?PING?` | `callsign` | Dasselbe wie `?APRST` | `?PING?` |
| `?VER` | `callsign` | Dasselbe wie `?ABOUT` und `?APRSV` | `?VER` |

### Bedeutung der Kürzel in `?APRSH`

- `I` - Pakete aus dem Internet, einschließlich APRS-IS selbst
- `G` - Pakete gegateter Stationen, callsign-SSID nach der Konstruktion `qAS`, `qAR` usw.
- `D` - digipeatete Stationspakete, deren erste Kopie aus dem Internet kam
- `d` - digipeatete Pakete, die lokal gehört wurden
- `R` - über Funk gehörte Pakete

---
## 2. Nur für UI-View verfügbare Funktionen

| Eingabe | An | Bedeutung |
|---|---|---|
| `BCN` | `callsign` | Position und Kommentartext als Beacon-Paket |
| `LGS` oder `LG1` | `callsign` | Protokollierung aller empfangenen Stationen starten |
| `LGX` oder `LG0` | `callsign` | Protokollierung empfangener Stationen beenden, der Befehl muss von der Station selbst stammen |
| `QAS` | `callsign` | Alle gehörten Stationen |
| `QWS` | `callsign` | Gehörte Wetterstationen |

---

## 3. ANSRVR und CQSRVR
### ANSRVR
| Eingabe | An | Bedeutung |
|---|---|---|
| `?` | `ANSRVR` | Liste verfügbarer Interessengruppen, ohne Zeit- oder Aktivitätslimit |
| `? GROUP NAME` | `ANSRVR` | Anzahl der Mitglieder der angegebenen Gruppe |
| `D GROUP NAME` | `ANSRVR` | Gruppenbeschreibung und Mitgliederliste |
| `L` | `ANSRVR` | Gruppen, denen ich angehöre |
| `J GROUP NAME` | `ANSRVR` | Einer Gruppe beitreten, Mitgliedschaft 12 Stunden gültig |
| `U GROUP NAME` | `ANSRVR` | Eine Gruppe verlassen |
| `CQ GROUP NAME Text...` | `ANSRVR` | Gleichzeitig der Gruppe beitreten und eine Nachricht an die Mitglieder senden |

### Hinweise zu `CQ GROUP NAME Text...`

- Wenn die Gruppe nicht existiert, wird der Absender ihr Eigentümer.
- Beim Anlegen einer Gruppe darf der Name höchstens **46 Zeichen** lang sein.
- Der Eigentümer kann die Gruppe erst löschen, wenn sie keine Mitglieder mehr hat.

### CQSRVR

| Eingabe | An | Bedeutung |
|---|---|---|
| `INFO` | `CQSRVR` | Liste kurzfristiger Gruppen, Mitgliedschaft und Gruppe verfallen nach 12 Stunden ohne Aktivität |

---

## 4. QRU-Server
### Allgemeine Abfragen

| Eingabe | An | Bedeutung |
|---|---|---|
| `INFO` | `QRU` | Welche QRU-Objektgruppen innerhalb meines geografischen Radius verfügbar sind, standardmäßig z. B. 50 km |
| `INFO 250` | `QRU` | Dasselbe, aber für einen Radius von 250 km |
| `OBJECT GROUP NAME` | `QRU` | Welche Objekte der angegebenen Gruppe innerhalb meines Radius verfügbar sind |
| `OBJECT GROUP NAME 150` | `QRU` | Welche Objekte der angegebenen Gruppe innerhalb eines Radius von 150 km verfügbar sind |

### Verhalten der QRU-Antworten
- Bei einer Anfrage über das Internet werden alle Objekte aufgelistet.
- In APRSIS32 können zusätzlich Kartenfenster mit ihren Positionen geöffnet werden.
- Bei einer Anfrage über Funk wird eine abschließende Nachricht wie `Sent 5 RP70 Objects Max 5@50km` zurückgegeben.
- Die Objekte werden anschließend über das IGATE gesendet und können auf dem Funkgerätedisplay, in der Stationsliste oder im Navigationssystem erscheinen.
- Das Dokument gibt an, dass Objekte auf aprs.fi nicht erscheinen, wenn die Anfrage über das Internet erfolgte, aber erscheinen, wenn die Anfrage über Funk erfolgte.

### QRU-Objektgruppen
| Code | Bedeutung |
|---|---|
| `AERO` | Aerodrome |
| `AIRP` | Airport |
| `AMBU` | Ambulance |
| `CLUB` | Amateur Radio Club |
| `CIVD` | Civil Defense |
| `ECHO` | EchoLink |
| `FIRE` | Fire Department |
| `FOOD` | Restaurant |
| `FUEL` | Gas Station |
| `HELI` | Rescue Helicopter |
| `HOSP` | Hospital |
| `INFO` | Info Kiosk Collection |
| `LIFEBOAT` | Lifeboat |
| `POLI` | Police |
| `POST` | Post Office |
| `RAIL` | Railway |
| `RD2M` | 2m D-Star Repeater |
| `RD70` | 70cm D-Star Repeater |
| `RP10` | 10m Repeater |
| `RP23` | 23cm Repeater |
| `RP2M` | 2m Repeater |
| `RP6M` | 6m Repeater |
| `RP70` | 70cm Repeater |
| `SHOP` | Shopping Center |
| `SRAIL` | Steam Railway |
| `STOR` | Amateur Radio Shop |
| `T2SRV` | T2 Server |
| `VETE` | Veterinarian |

---
## 5. AVRS, Automatic Voice Relay System

| Eingabe | An | Bedeutung |
|---|---|---|
| `?` | `AVRS` | Wo sich die nächstgelegene EchoLink-, IRLP- oder Allstar-Station relativ zu meiner Position befindet |
| `? CALLSIGN-SSID` | `AVRS` | Wo sich die nächstgelegene EchoLink-, IRLP- oder Allstar-Station relativ zur Position der angegebenen Station befindet |
| `CALLSIGN-SSID` | `AVRS` | Anfrage nach den Informationen, die zum Aufbau eines Sprachkontakts über den nächstgelegenen EchoLink-Knoten erforderlich sind |

### Hinweis

Damit AVRS korrekt funktioniert, muss zuvor mindestens ein Positions-Beacon APRS-IS erreicht haben.

---
## 6. APRSlink, WLNK-1

APRSlink dient für Anfragen von Winlink-Benutzern mit einer Adresse im Format `callsign@winlink.org`.

| Eingabe | An | Bedeutung |
|---|---|---|
| `H` | `WLNK-1` | Hilfe |
| `I` | `WLNK-1` | Informationen zu APRSlink |
| `?L` | `WLNK-1` | Hilfe zu einem bestimmten Befehl |
| `L` | `WLNK-1` | Liste verfügbarer Nachrichten, normalerweise die letzten 5 |
| `R<number>` | `WLNK-1` | Eine bestimmte Nachricht lesen |
| `Y<number>` | `WLNK-1` | Auf eine bestimmte Nachricht antworten |
| `K<number>` | `WLNK-1` | Eine bestimmte Nachricht löschen |
| `F<number>` | `WLNK-1` | Eine bestimmte Nachricht weiterleiten |
| `SP <email/callsign/alias> <subject>` | `WLNK-1` | Senden einer längeren E-Mail in mehreren Schritten beginnen |
| `/EX` | `WLNK-1` | Vollständige Nachricht abschließen und senden |
| `P` | `WLNK-1` | Wiedergabe der Nachricht |
| `SMS ...` | `WLNK-1` | Eine einzeilige Nachricht senden |
| `A ALIAS=mail@provider.net` | `WLNK-1` | Alias erstellen oder aktualisieren |
| `A ALIAS=` | `WLNK-1` | Alias löschen |
| `AL` | `WLNK-1` | Aliasliste |
| `G<number>` | `WLNK-1` | Anfrage nach den nächstgelegenen RMS-Packet-Gateways, standardmäßig 1 |

### `SP`-Sequenz

1. Start: `SP <email oder callsign oder alias> <subject>`
2. Nacheinander Textabschnitte senden
3. Mit einer separaten Nachricht `/EX` abschließen

Das Dokument weist darauf hin, dass bis zum abschließenden Befehl Text ohne zusätzliche Identifikation an `WLNK-1` gesendet werden kann.

---
## 7. WHO-IS, Rufzeichenabfragen auf QRZ.com

| Eingabe | An | Bedeutung |
|---|---|---|
| `callsign` | `WHO-IS` | Kurze Anfrage: Klasse / Name / Land |
| `F callsign` | `WHO-IS` | Vollständige Anfrage: Name / Klasse / Straße / Stadt / Land |

Das Dokument gibt an, dass die Anfrage auch über `WHO-15` möglich ist.

---
## 8. QRZ, Objektabfragen

| Eingabe | An | Bedeutung |
|---|---|---|
| `object name` | `QRZ` | Kurze Anfrage zu einem höchstens 2 Stunden alten Objekt: Objektname und Text |
| `object*` | `QRZ` | Variante mit `*` als Wildcard am Ende, die Antwort enthält eine Auswahl verfügbarer Objekte |

---

## 9. METAR, Wetterabfragen

| Eingabe | An | Bedeutung |
|---|---|---|
| `ICAO Code` | `WXBOT` | Kurze Anfrage zum aktuellen Flughafenwetter, SA/METAR |

Beispiel aus dem Dokument: `EDDL`.

---
## 10. WHERE-IS, Positionen, Entfernung, Alarme

**Wichtig:** Das Dokument weist darauf hin, dass `WHERE` und `WHERE-IS` **Groß- und Kleinschreibung unterscheiden**.

| Eingabe | An | Bedeutung |
|---|---|---|
| `callsign` | `WHERE` oder `WHERE-IS` | Kurze Anfrage nach Entfernung, Peilung und Zeit des letzten Berichts |
| `where callsign` | `WHERE` oder `WHERE-IS` | Dasselbe wie oben |
| `dir callsign` | `WHERE` oder `WHERE-IS` | Nur Richtung und Zeit |
| `direction callsign` | `WHERE` oder `WHERE-IS` | Langform von `dir` |
| `dis callsign` | `WHERE` oder `WHERE-IS` | Nur Entfernung und Zeit |
| `distance callsign` | `WHERE` oder `WHERE-IS` | Langform von `dis` |
| `dis callsign @km` | `WHERE` oder `WHERE-IS` | Entfernung in bestimmten Einheiten |
| `pos callsign` | `WHERE` oder `WHERE-IS` | Nur Koordinaten und Zeit |
| `loc callsign` | `WHERE` oder `WHERE-IS` | Dasselbe wie `pos` |
| `position callsign` | `WHERE` oder `WHERE-IS` | Langform von `pos` |
| `location callsign` | `WHERE` oder `WHERE-IS` | Langform von `loc` |
| `last callsign` | `WHERE` oder `WHERE-IS` | Nur Datum und Zeit des letzten Berichts |
| `alert callsign < 15 km` | `WHERE` oder `WHERE-IS` | Alarm, wenn die Station näher als die angegebene Entfernung kommt |
| `alert callsign > 15 km` | `WHERE` oder `WHERE-IS` | Alarm, wenn sich die Station weiter als die angegebene Entfernung entfernt |
| `cancel callsign` | `WHERE` oder `WHERE-IS` | Löscht den gespeicherten Alarm |
| `help` | `WHERE` oder `WHERE-IS` | Zeigt verfügbare Befehle |

### Einheiten in `WHERE` / `WHERE-IS`

Verfügbare Einheiten:

- `@miles` oder `@mi`
- `@nm`
- `@yards` oder `@y`
- `@foot` oder `@f`
- `@meters` oder `@m`
- `@km`

### Einschränkungen der Alarme

- Ein Alarm gilt nur für **eine Station**
- Nach dem Auslösen wird der Alarm gelöscht
- Das Dokument sagt, dass eine dauerhafte Speicherung mehrerer Alarme noch nicht verfügbar war

---

## 11. SMSGTE

| Eingabe | An | Bedeutung |
|---|---|---|
| `@6135551234 free text...` | `SMSGTE` | System zur Verbindung von APRS und Mobiltelefonen |

### Hinweis

Das Dokument weist darauf hin, dass `SMSGTE` nur in den **USA und Kanada** verfügbar ist.

---
## 12. Kurze praktische Zusammenfassung

Die wichtigsten Abfragegruppen aus dem Dokument:

- Standard-APRS-Abfragen an Stationen und Systeme
- UI-View-spezifische Funktionen
- ANSRVR- und CQSRVR-Gruppen
- QRU-Objekte und Verzeichnisse
- AVRS zum Auffinden der nächstgelegenen Sprachknoten
- APRSlink zu Winlink über `WLNK-1`
- WHO-IS und QRZ für Informationen zu Rufzeichen und Objekten
- WXBOT für METAR
- WHERE / WHERE-IS für Position, Entfernung und Alarme
- SMSGTE als APRS-zu-SMS-Brücke
