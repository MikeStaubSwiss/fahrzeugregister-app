# Fahrzeugregister

Eine Verwaltung für Fahrzeuge mit Schweizer Fahrzeugausweis, gebaut als einzelne HTML-Datei. Man fotografiert den Ausweis (Seiten 2 und 3), eine Texterkennung im Browser liest die Felder aus, und das Fahrzeug landet in einer durchsuchbaren Tabelle. Kein Konto, kein Schlüssel, keine Kosten.

## Funktionen

- **Ausweis einlesen** – Foto wählen, die Felder werden per Texterkennung ([Tesseract.js](https://github.com/naptha/tesseract.js)) direkt im Browser erkannt. Füllzeichen (`**2500`, `******`) und Trennlinien (`-----`) in den Vermerken werden übernommen.
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

## Ausweis einlesen: Texterkennung

Das Einlesen läuft vollständig im Browser. Das Foto verlässt das Gerät nicht.

1. Das Bild wird gerade ausgerichtet, Farbstreifen und Tabellenlinien werden entfernt.
2. Tesseract liest den ganzen Ausweis in zwei Durchgängen. Die Feldnummern (15, 17, 19 …) dienen als Raster für die Zuordnung.
3. Jede Feldzeile wird ein drittes Mal einzeln gelesen, Gewichte nur mit Ziffern und Sternchen.

Das dauert je nach Gerät etwa 20 bis 40 Sekunden. Beim ersten Mal lädt die App Tesseract und die deutschen Sprachdaten (rund 5 MB) von `cdn.jsdelivr.net`; danach kommen sie aus dem Browser-Cache.

Die Erkennung ist gut, aber nicht fehlerfrei: einzelne Ziffern, fehlende Felder oder Randzeichen kommen vor. Die App öffnet das Ergebnis deshalb immer zum Prüfen, bevor gespeichert wird. Für ein gutes Ergebnis den Ausweis flach, gleichmässig beleuchtet und gerade von oben fotografieren.

In der claude.ai-Variante (als Artefakt) liest stattdessen das eingebaute Claude den Ausweis.

## Dokumentation

Die Benutzerdokumentation liegt in [`docs/`](docs/) als Word-Datei und als PDF.

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
- Läuft auch als claude.ai-Artefakt: Dort werden automatisch der Artefakt-Speicher, das integrierte Claude und der Download-Dialog verwendet.

## Copyright

© 2026 Mike Staub
