# AI Quota Tracker

Ein leichtgewichtiger, serverloser Web-Client zum Verwalten und Überwachen von Nutzungslimits (Quotas) verschiedener KI-Dienste (z. B. Claude, ChatGPT, Gemini, DeepSeek) über mehrere Accounts hinweg – inklusive sekundengenauem Live-Countdown und clientseitiger Ende-zu-Ende-Verschlüsselung.

## Highlights & Architektur

Das Projekt verfolgt einen **Zero-Backend- & Zero-Knowledge-Ansatz**:

* **100 % Serverless:** Hosting der statischen Oberfläche über GitHub Pages, Datenhaltung über ein privates GitHub Gist via GitHub REST API.
* **Clientseitige AES-256-GCM-Verschlüsselung:** Sensible Account-Informationen werden vor der Übertragung im Browser mithilfe der nativen **Web Crypto API** (`PBKDF2` mit 250.000 Iterationen + `AES-GCM`) verschlüsselt. Das Master-Passwort verlässt zu keinem Zeitpunkt den Arbeitsspeicher (`RAM`).
* **Public-Demo-Modus:** Ohne hinterlegte Zugangsdaten startet die Anwendung automatisch mit anonymisierten Beispieldaten (`Max Mustermann`), sodass die Oberfläche jederzeit öffentlich getestet werden kann, ohne echte Kontodaten preiszugeben.
* **Security-Hardening:** Strikte Content Security Policy (`CSP`), isolierte DOM-Manipulation über `textContent` (Schutz vor XSS) und keinerlei externe Abhängigkeiten oder Tracker.
* **Cross-Platform (Desktop & Mobile):** Nutzbar als isolierte Desktop-App unter Linux (z. B. Arch Linux via `--app`-Flag und dediziertem Browser-Profil) sowie als Web-App auf Android.

## Funktionen

* **Live-Countdown:** Echtzeit-Anzeige der verbleibenden Sperrzeit pro KI-Dienst und Account inklusive automatischer Status-Markierung (`VERFÜGBAR!`).
* **24-Stunden-Zeiteingabe & Schnellwahl:** Direktes Setzen von Reset-Zeiten im ISO-/24h-Format (`HH:MM`) oder per Schnellwahl-Buttons (`+3 Std`, `+5 Std`, `+24 Std`).
* **Vollständiges CRUD:** Einträge hinzufügen, bearbeiten und löschen mit automatischer Cloud-Synchronisation.

## Setup & Eigene Nutzung

1. **Repository forken / klonen** und in den Repository-Einstellungen **GitHub Pages** für den `main`-Branch aktivieren.
2. **Privates Gist erstellen:** Unter [gist.github.com](https://gist.github.com) ein *Secret Gist* mit dem Dateinamen `limits.json` und dem initialen Inhalt `[]` anlegen. Die Gist-ID aus der URL kopieren.
3. **Personal Access Token generieren:** In den GitHub Developer Settings einen Token mit ausschließlich der Berechtigung **`gist`** erstellen.
4. **Tresor verbinden:** In der Web-App auf **Login / Tresor** klicken, Gist-ID, Token sowie ein frei gewähltes Tresor-Passwort eingeben und verbinden.

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

## Tech Stack

* **Frontend:** Vanilla HTML5, CSS3 (Custom Properties / Dark Theme), ES6+ JavaScript
* **Kryptografie:** Web Crypto API (`SubtleCrypto` – `PBKDF2`, `SHA-256`, `AES-GCM`)
* **Storage & API:** GitHub Gists REST API, Browser `localStorage` (nur für Token/Gist-ID)
