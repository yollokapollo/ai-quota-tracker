# AI Quota Tracker

Ein leichtgewichtiger, serverloser Web-Client zum Verwalten und Überwachen von Nutzungslimits (Quotas) gängiger KI-Dienste (u. a. Claude, ChatGPT, Gemini, DeepSeek, Perplexity, Mistral, Grok, Cursor, Windsurf u. v. m.) über mehrere Accounts hinweg – inklusive **„Aktuelle Projekte“**, **Filter & Suche**, **Live-Countdown**, **Projekt-Verlauf** und **clientseitiger AES-256-GCM-Verschlüsselung**.

## Highlights & Architektur

Das Projekt verfolgt einen **Zero-Backend- & Zero-Knowledge-Ansatz**:

* **100 % Serverless:** Hosting der statischen Oberfläche über GitHub Pages, Datenhaltung über ein privates GitHub Gist (`limits.json`) via GitHub REST API.
* **Clientseitige AES-256-GCM-Verschlüsselung:** Quotas, Account-Bezeichnungen, aktuelle Projekte sowie das gesamte Änderungs-/Verlaufsprotokoll werden vor dem Upload im Browser mithilfe der nativen **Web Crypto API** (`PBKDF2` mit 250.000 Iterationen + `AES-GCM`) verschlüsselt. Das Tresor-Passwort bleibt ausschließlich im Arbeitsspeicher (`RAM`).
* **Public-Demo-Modus:** Ohne hinterlegte Zugangsdaten startet die Anwendung automatisch mit anonymisierten Beispieldaten (`Max Mustermann`, `Maria Musterfrau`), sodass die Oberfläche jederzeit öffentlich getestet werden kann, ohne echte Kontodaten preiszugeben.
* **Security-Hardening:** Strikte Content Security Policy (`CSP`), `no-referrer`, Eingabebegrenzung, Schema-Validierung nach dem Entschlüsseln, isolierte DOM-Manipulation über `textContent` (Schutz vor XSS) und keinerlei externe Abhängigkeiten oder Tracker.
* **Cross-Platform (Desktop & Mobile):** Nutzbar als isolierte Desktop-App unter Linux (z. B. Arch Linux via `--app`-Flag und dediziertem Browser-Profil) sowie als Web-App auf Android.

## Funktionen

* **Live-Countdown & Schnell-Reset:** Echtzeit-Anzeige der verbleibenden Sperrzeit pro KI-Dienst und Account, automatische Markierung (`VERFÜGBAR!`) sowie ein **„Jetzt verfügbar“**-Button direkt am Eintrag.
* **24-Stunden-Zeiteingabe & Schnellwahl:** Direktes Setzen von Reset-Zeiten im ISO-Datum- und 24h-Format (`HH:MM`, ohne AM/PM-Verwirrung) oder per Schnellwahl-Buttons (`+3 Std`, `+5 Std`, `+24 Std`).
* **Aktuelle Projekte pro Account:** Freitextfeld zur Zuordnung, welcher Account aktuell an welchen Projekten arbeitet.
* **Filter & Suche:** Filtern nach KI-Dienst sowie Live-Suche nach Projekten oder Accounts.
* **Verlauf & Projekt-Historie:** Protokolliert Aktionen (*Angelegt*, *Bearbeitet*, *Jetzt verfügbar*, *Gelöscht*) mit Zeitstempel und macht Änderungen an den Projekt-Titeln (`vorher → nachher`) nachvollziehbar – durchsuchbar, ein-/ausblendbar und leerbar.

## Setup & Eigene Nutzung

1. **Repository anlegen / klonen** (`ai-quota-tracker`) und in den Repository-Einstellungen **GitHub Pages** für den `main`-Branch aktivieren.
2. **Privates Gist erstellen:** Unter [gist.github.com](https://gist.github.com) ein *Secret Gist* mit dem Dateinamen `limits.json` und dem initialen Inhalt `[]` anlegen. Die Gist-ID aus der URL kopieren.
3. **Personal Access Token generieren:** In den GitHub Developer Settings einen Token mit ausschließlich der Berechtigung **`gist`** (und sinnvollem Ablaufdatum) erstellen.
4. **Tresor verbinden:** In der Web-App auf **Login / Tresor** klicken, Gist-ID, Token sowie ein frei gewähltes Tresor-Passwort eingeben und verbinden. Beim ersten Speichern wird der Tresor automatisch mit deinem Passwort verschlüsselt.

### Integration als Desktop-Fenster (Arch Linux)

Um die Anwendung vom regulären Browser-Profil und dessen Erweiterungen zu isolieren, kann eine `.desktop`-Datei unter `~/.local/share/applications/ai-quota-tracker.desktop` angelegt werden:

```ini
[Desktop Entry]
Name=AI Quota Tracker
Comment=Verschlüsselter AI Kontingent-Tracker
Exec=brave --user-data-dir=%h/.local/share/ai-quota-profile --app=https://yollokapollo.github.io/ai-quota-tracker/ --class=ai-quota-tracker
Terminal=false
Type=Application
Categories=Utility;
StartupWMClass=ai-quota-tracker

```

Anschließend bei Bedarf einmalig die Desktop-Datenbank aktualisieren:

```bash
update-desktop-database ~/.local/share/applications/

```

## Tech Stack

* **Frontend:** Vanilla HTML5, CSS3 (Custom Properties / Dark Theme), ES6+ JavaScript
* **Kryptografie:** Web Crypto API (`SubtleCrypto` – `PBKDF2`, `SHA-256`, `AES-GCM`)
* **Storage & API:** GitHub Gists REST API, Browser `localStorage` (nur für Gist-ID und Token)
