# Nowplay

**Ein Spotify-Display für alte Smartphones.** Nowplay verwandelt ein ausgedientes Handy in einen Musik-Bildschirm für den Schreibtisch: großes Album-Cover, Titel, Fortschrittsbalken und Steuerung per Tippen und Wischen.

Das Handy spielt dabei selbst keine Musik ab. Es zeigt an, was gerade auf deinem PC, Mac oder Lautsprecher läuft, und steuert die Wiedergabe über Spotify Connect.

<!-- Screenshot hier einfügen, z. B.: ![Nowplay auf einem Galaxy S10](screenshot.jpg) -->

## Funktionen

- Großes Cover, Titel, Interpret und Album, mit Hintergrundfarbe passend zum Cover
- Play/Pause, Vor/Zurück, Zufallswiedergabe, Wiederholen
- Gesten: links/rechts wischen = Titel wechseln, hoch/runter wischen = Lautstärke, Tipp aufs Cover = Play/Pause
- Antippen des Fortschrittsbalkens springt im Titel
- Geräte wechseln und Lautstärke regeln
- Einbrennschutz für OLED-Displays: wandernde Uhr nach 2 Minuten Pause, Schwarzbild nach 30 Minuten, leichte Pixelverschiebung
- Hält den Bildschirm wach, solange Musik läuft
- Eine einzige HTML-Datei, kein Server-Code, keine Abhängigkeiten
- Anmeldung über OAuth mit PKCE – kein Client Secret nötig, keine Zugangsdaten im Code

## Voraussetzungen

- **Spotify Premium** (für selbst erstellte Spotify-Apps seit Februar 2026 erforderlich)
- Ein Smartphone oder Tablet mit einem aktuellen Browser (getestet ist das Layout für Querformat, z. B. Galaxy S10)
- Eine Möglichkeit, die Seite über `https://` oder `http://127.0.0.1` auszuliefern (siehe unten)

## Einrichtung

### 1. Seite bereitstellen

Spotify erlaubt die Anmeldung nur auf Adressen mit `https://` oder auf `http://127.0.0.1:PORT`. Du hast zwei Möglichkeiten:

#### Variante A: GitHub Pages (am einfachsten)

1. Forke dieses Repository oder lade `index.html` in ein eigenes Repository hoch.
2. Gehe zu **Settings → Pages**, wähle bei **Branch** `main` und `/ (root)` und klicke auf **Save**.
3. Nach 1–2 Minuten ist die Seite unter `https://DEINNAME.github.io/nowplay/` erreichbar.

#### Variante B: Lokal auf dem Handy mit Termux (komplett im Heimnetz)

1. Installiere [Termux](https://github.com/termux/termux-app/releases) (über F-Droid oder GitHub, nicht aus dem Play Store).
2. Installiere Python:
   ```bash
   pkg update && pkg upgrade -y
   pkg install python -y
   ```
3. Lade `index.html` auf das Handy herunter und kopiere sie in einen Ordner:
   ```bash
   termux-setup-storage
   mkdir -p ~/nowplay
   cp ~/storage/downloads/index.html ~/nowplay/
   ```
4. Starte den Webserver:
   ```bash
   cd ~/nowplay
   python -m http.server 8080 --bind 127.0.0.1
   ```
5. Tippe in der Termux-Benachrichtigung auf **Acquire wakelock** und stelle unter Einstellungen → Apps → Termux → Akku **Nicht eingeschränkt** ein.

<details>
<summary>Optional: Automatisch beim Hochfahren starten (Termux:Boot)</summary>

Installiere zusätzlich **Termux:Boot** aus derselben Quelle wie Termux und öffne die App einmal. Dann:

```bash
mkdir -p ~/.termux/boot
cat > ~/.termux/boot/nowplay.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/sh
termux-wake-lock
cd ~/nowplay
python -m http.server 8080 --bind 127.0.0.1
EOF
chmod +x ~/.termux/boot/nowplay.sh
```
</details>

Die Seite ist dann auf dem Handy unter `http://127.0.0.1:8080/` erreichbar.

### 2. Spotify-App anlegen

1. Öffne die Seite. Sie zeigt den Einrichtungsbildschirm mit deiner **Redirect URI** – kopiere sie.
2. Gehe zum [Spotify Developer Dashboard](https://developer.spotify.com/dashboard) und klicke auf **Create app**.
3. Fülle die Felder aus:
   - **App name:** z. B. `Nowplay`
   - **App description:** z. B. `Schreibtisch-Display`
   - **Redirect URIs:** die kopierte Adresse einfügen und auf **Add** klicken
   - **APIs:** **Web API** anhaken
4. Speichern, dann unter **Settings** die **Client ID** kopieren. Das Client Secret wird nicht benötigt.

### 3. Verbinden

1. Füge die Client ID auf der Nowplay-Seite ein und klicke auf **Mit Spotify verbinden**.
2. Bestätige den Zugriff bei Spotify.
3. Starte Musik auf einem beliebigen Gerät – sie erscheint nach wenigen Sekunden.

Tipp: Über das Chrome-Menü → **Zum Startbildschirm hinzufügen** startet Nowplay wie eine App. Ein Tipp aufs Display schaltet in den Vollbildmodus.

## Anpassen

Am Anfang des `<script>`-Blocks in `index.html` stehen die Zeiten für den Einbrennschutz:

```js
const IDLE_AFTER_MS = 2 * 60 * 1000;   // Uhr nach 2 Minuten ohne Wiedergabe
const DEEP_AFTER_MS = 30 * 60 * 1000;  // Schwarzbild nach 30 Minuten
const SHIFT_EVERY_MS = 2 * 60 * 1000;  // Pixelverschiebung alle 2 Minuten
```

## Fehlerbehebung

| Problem | Lösung |
|---|---|
| `INVALID_CLIENT: Invalid redirect URI` | Die Redirect URI im Spotify-Dashboard muss exakt der angezeigten entsprechen, inklusive `/` am Ende. |
| „Dafür ist Spotify Premium nötig“ | Die Spotify Web API funktioniert im Development Mode nur mit Premium. |
| „Gerade läuft nichts“ | Öffne Spotify auf einem Gerät oder wähle über „Gerät wählen“ eines aus. |
| Lautstärke lässt sich nicht ändern | Manche Geräte erlauben keine Fernsteuerung der Lautstärke. |
| Einrichtungsbildschirm erscheint erneut | Die Anmeldung ist abgelaufen oder wurde widerrufen – einfach neu verbinden. |

## Datenschutz

Nowplay hat kein Backend. Client ID und Anmelde-Token werden nur im Browser des jeweiligen Geräts gespeichert (`localStorage`). Die Seite kommuniziert ausschließlich direkt mit Spotify.

## Hinweis

Nowplay ist ein privates Hobbyprojekt und steht in keiner Verbindung zu Spotify. Spotify ist eine Marke der Spotify AB.

## Lizenz

[MIT](LICENSE)
