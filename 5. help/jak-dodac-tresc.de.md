---

title: Inhalte zu APRS.help hinzufügen
description: So wird ein neuer Artikel vorbereitet und an das APRS.help-Repository übermittelt.
template: doc
tableOfContents: true
---------------------

APRS.help ist ein offenes Projekt und jeder kann dabei helfen, die Wissensbasis zu erweitern.

Die auf der Website angezeigten Inhalte werden im öffentlichen GitHub-Repository gespeichert:

`https://github.com/SQ9MDD/aprs-help-content`

APRS.help synchronisiert seine Inhalte mit diesem Repository. Neue Artikel oder Änderungen werden daher durch das Bearbeiten der entsprechenden Markdown-Dateien und das Einreichen der Änderungen als **Pull Request** hinzugefügt.

## Erforderliche Sprachversionen

Jeder neue Artikel muss gleichzeitig in vier Sprachversionen vorbereitet werden:

* Polnisch
* Englisch
* Spanisch
* Deutsch

Für jeden Artikel müssen vier zusammengehörige Dateien erstellt werden:

```text
artikelname.pl.md
artikelname.en.md
artikelname.es.md
artikelname.de.md
```

Beispiel:

```text
digipeater.pl.md
digipeater.en.md
digipeater.es.md
digipeater.de.md
```

Die Dateinamen sollten identisch sein und sich nur durch den Sprachcode unterscheiden.

Ein Pull Request mit einem neuen Artikel sollte alle vier Sprachversionen enthalten.

Fehlt eine der erforderlichen Sprachversionen, gilt der Artikel als unvollständig und sollte nicht in das Haupt-Repository übernommen werden.

## Dokumentformat

Die Dokumente werden im **Markdown-Format** (`.md`) erstellt.

Am Anfang jeder Datei sollten grundlegende Metadaten stehen:

```yaml
---
title: Titel des Artikels
description: Kurze Beschreibung des Inhalts.
template: doc
tableOfContents: true
---
```

Die Felder `title` und `description` sollten für jede Sprachversion übersetzt werden. Der Seitentitel wird ausschließlich aus dem Feld `title` übernommen; füge deshalb am Anfang des Artikelinhalts keine zusätzliche Überschrift der Ebene 1 (`# Titel`) ein.

Beispiel für eine deutsche Version:

```markdown
---
title: APRS-Digipeater
description: Grundlegende Informationen über die Funktionsweise von Digipeatern im APRS-Netz.
template: doc
tableOfContents: true
---

Ein Digipeater ist eine Station, die über Funk empfangene APRS-Pakete erneut aussendet.

## Funktionsweise eines Digipeaters

Artikelinhalt...
```

Die entsprechenden Dateien in den anderen Sprachen sollten dieselbe Dokumentstruktur beibehalten.

## Wo die Dateien abgelegt werden

Alle vier Sprachversionen sollten im selben Verzeichnis abgelegt werden, das thematisch zum Artikel passt.

Beispiel:

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
```

Wenn du nicht sicher bist, welche Kategorie am besten passt, wähle die thematisch ähnlichste. Der Speicherort kann während der Prüfung des Pull Requests angepasst werden.

## Neue Inhalte einreichen

Empfohlen wird der übliche GitHub-Ablauf:

1. Erstelle einen Fork des Repositorys `SQ9MDD/aprs-help-content`.
2. Bereite den Artikel im Markdown-Format vor.
3. Erstelle die vier erforderlichen Sprachversionen: `pl`, `en`, `es` und `de`.
4. Lege alle Dateien im passenden Verzeichnis ab.
5. Committe die Änderungen in deinem Repository.
6. Erstelle einen **Pull Request** zum Haupt-Repository von APRS.help.
7. Beschreibe kurz, welche Inhalte hinzugefügt oder geändert wurden.

Nach der Prüfung und Annahme wird der Pull Request in das Haupt-Repository übernommen.

Der APRS.help-Server synchronisiert seine Inhalte regelmäßig mit dem Repository. Akzeptierte Änderungen erscheinen daher anschließend auf der Website.

## Bestehende Artikel ändern

Ein Pull Request kann auch bestehende Inhalte verändern, zum Beispiel:

* einen technischen Fehler korrigieren,
* eine Beschreibung präzisieren,
* ein Beispiel hinzufügen,
* einen Tippfehler korrigieren,
* veraltete Informationen aktualisieren,
* einen Artikel erweitern.

Wenn sich durch die Änderung die Bedeutung des Inhalts ändert, sollten alle Sprachversionen des Dokuments aktualisiert werden.

## Einige Regeln

Beim Erstellen von Inhalten:

* klar und sachlich schreiben,
* größere Themen mit Überschriften gliedern,
* Codeblöcke für APRS-Frames, Konfigurationen und Befehle verwenden,
* in allen Sprachversionen eine ähnliche Struktur beibehalten,
* auch Titel, Beschreibungen und Überschriften übersetzen,
* bei Beschreibungen des Protokollverhaltens nach Möglichkeit eine Quelle oder Dokumentation angeben,
* keine größeren Abschnitte fremder Materialien kopieren,
* bei diskutierbaren Themen Protokollanforderungen, bewährte Verfahren und persönliche Erfahrungen voneinander trennen.

APRS.help soll eine gemeinsame, praktische und technische Wissensquelle über APRS sein, die von der Amateurfunk-Community weiterentwickelt wird.

## GitHub ist dir nicht vertraut?

Kein Problem.

Für einfache Änderungen kann der GitHub-Editor direkt im Webbrowser verwendet werden. Git oder zusätzliche Software müssen dafür nicht installiert werden.

Am wichtigsten sind nützliche Inhalte und alle erforderlichen Sprachversionen. Über den Pull-Request-Mechanismus können Änderungen vor der Veröffentlichung gemeinsam geprüft und bei Bedarf korrigiert werden.
