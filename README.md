# Fahrzeugregister

Eine Verwaltung für Fahrzeuge mit Schweizer Fahrzeugausweis, gebaut als einzelne HTML-Datei. Man fotografiert den Ausweis (Seiten 2 und 3), Claude liest die Felder aus, und das Fahrzeug landet in einer durchsuchbaren Tabelle.

## Funktionen

- **Ausweis einlesen** – Foto wählen, die Felder werden über die Anthropic API erkannt. Die Werte werden zeichengenau übernommen, auch Füllzeichen (`**2500`, `******`) und Trennlinien (`-----`) in den Vermerken.
- **Tabelle** – Volltextsuche über alle Felder, Sortieren per Klick auf die Spaltenköpfe (Datumsangaben werden chronologisch sortiert).
- **Ausweise** – Jedes Fahrzeug als Nachbildung der Ausweisseiten 2 und 3 mit Feldnummern, dazu das Originalfoto.
- **Detail** – Umschalten zwischen Ausweis-Ansicht und bearbeitbaren Feldern, Löschen mit Bestätigung.
- **Duplikaterkennung** – Gleiche Stammnummer oder Fahrgestell-Nr. aktualisiert den bestehenden Eintrag.
- **Backup / Restore** – Export als JSON-Datei; beim Wiederherstellen wahlweise zusammenführen oder alles ersetzen.
- Hell- und Dunkelmodus, funktioniert auf dem Handy.

## Erfasste Felder

| Nr. | Feld | Nr. | Feld |
|---|---|---|---|
| 01–06 | Halter, Adresse | 27 | Plätze (total / vorne) |
| 07 | Geburtsdatum | 30 | Leergewicht |
| 08 | Heimatstaat | 31 | Anhängelast |
| 09 | Versicherung | 32 | Nutz-/Sattellast |
| 13/14 | Kantonale Vermerke, Verfügungen | 33 | Gesamtgewicht |
| 15 | Kontrollschild, Schildfarbe | 35 | Gewicht des Zuges |
| 17 | Besondere Verwendung | 36 | 1. Inverkehrsetzung |
| 18 | Stammnummer | 37 | Hubraum |
| 19 | Art des Fahrzeugs, Code | 38 | Ort, Datum |
| 21 | Marke und Typ | 39 | Prüfungen |
| 23 | Fahrgestell-Nr. | 55 | Dachlast |
| 24 | Typengenehmigung | 72 | Emissionscode |
| 25 | Karosserie, Code | 76 | Leistung |
| 26 | Farbe | 78 | kW/kg Leergewicht |

## Starten

Keine Installation, kein Build. Entweder `index.html` direkt im Browser öffnen oder über GitHub Pages veröffentlichen:

1. Repository auf GitHub anlegen und die Dateien pushen.
2. **Settings → Pages → Build and deployment → Source: GitHub Actions** wählen.
3. Der Workflow in `.github/workflows/pages.yml` veröffentlicht die Seite bei jedem Push auf `main`.

```bash
git init
git add .
git commit -m "Fahrzeugregister"
git branch -M main
git remote add origin https://github.com/<benutzer>/fahrzeugregister.git
git push -u origin main
```

## Ausweis einlesen: API-Schlüssel

Für die Bilderkennung braucht es einen eigenen Anthropic-API-Schlüssel ([console.anthropic.com](https://console.anthropic.com)).

1. In der App auf **Einstellungen** klicken.
2. Schlüssel eintragen, optional das Modell ändern (Standard: `claude-sonnet-5-5`).

Der Schlüssel wird nur im `localStorage` des Browsers gespeichert und direkt an `api.anthropic.com` gesendet (Header `anthropic-dangerous-direct-browser-access`). Er gelangt nie ins Repository. Jedes Einlesen kostet API-Guthaben. Auf einem fremden oder geteilten Gerät den Schlüssel nach Gebrauch über **Schlüssel löschen** entfernen.

Ohne Schlüssel funktioniert alles andere weiterhin; Fahrzeuge lassen sich über **Manuell** erfassen.

## Datenspeicherung

- Alle Fahrzeuge liegen im `localStorage` des jeweiligen Browsers. Es gibt keinen Server und keine Synchronisation zwischen Geräten.
- Pro Fahrzeug wird ein verkleinertes Foto (ca. 100 KB) mitgespeichert. Browser erlauben meist rund 5 MB, das reicht für etwa 40 Fahrzeuge mit Foto.
- Browserdaten löschen entfernt auch das Register. **Regelmässig ein Backup erstellen.**
- Über Backup/Restore lassen sich Daten zwischen Geräten übertragen.

> **Datenschutz:** Fahrzeugausweise enthalten Name, Adresse und Geburtsdatum. Backups (`fahrzeugregister-*.json`) sind per `.gitignore` ausgeschlossen. Keine echten Backups in ein öffentliches Repository committen.

## Backup-Format

```json
{
  "format": "fahrzeugregister",
  "version": 1,
  "exportiert": "2026-11-08T18:00:00.000Z",
  "fahrzeuge": [
    { "id": "fz_…", "schild": "ZH123456", "markeTyp": "…", "leergewicht": "**1450", "bild": "data:image/jpeg;base64,…" }
  ]
}
```

Ein Beispiel mit erfundenen Daten liegt in [`beispiele/beispiel-backup.json`](beispiele/beispiel-backup.json) und kann über **Restore** geladen werden.

## Technik

- Eine Datei, reines HTML/CSS/JavaScript, keine Abhängigkeiten ausser Google Fonts.
- Läuft auch als claude.ai-Artefakt: Dort werden automatisch der Artefakt-Speicher, das integrierte Claude und der Download-Dialog verwendet; die Einstellungen sind dann ausgeblendet.

## Copyright und Lizenz

© 2026 Mike Staub. Veröffentlicht unter der MIT-Lizenz, siehe [LICENSE](LICENSE).
