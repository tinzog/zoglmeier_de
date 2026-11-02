# zoglmeier.de

Statische Website (Platzhalter), die per GitHub Actions automatisch auf den netcup-Webspace deployt wird.

## Struktur

```
public/                 ← wird 1:1 auf den Webspace hochgeladen
  index.html
  style.css
  favicon.svg
  .htaccess             ← index.html vor index.php, Security- und Cache-Header
  images/               ← web-optimierte Bilder
images/                 ← Originalbilder (werden nicht deployt)
.github/workflows/deploy.yml
```

## Lokal ansehen

```sh
python3 -m http.server 8000 --directory public
# → http://localhost:8000
```

## Deployment

Jeder Push auf `main`, der etwas unter `public/` ändert, startet den Workflow **Deploy to netcup**. Er lädt die geänderten
Dateien per FTPS (FTP über TLS) hoch. Manuell starten geht über *Actions → Deploy to netcup → Run workflow*.

Benötigte Repository-Secrets: `FTP_SERVER`, `FTP_USERNAME`, `FTP_PASSWORD` und `FTP_SERVER_DIR` (z. B. `zoglmeier_de/`).

### HTTPS

HTTPS läuft bereits über das Let's-Encrypt-Zertifikat von netcup/Plesk, inklusive Weiterleitung von HTTP auf HTTPS
und HSTS. Daran ändert sich durch den Wechsel des Dokumentenstamms nichts. Falls nach dem Umstellen die Weiterleitung
fehlt: Plesk → *Hosting-Einstellungen* → „Permanente, SEO-sichere 301-Weiterleitung von HTTP zu HTTPS“ aktivieren.

### Hinweise

- Der Workflow legt auf dem Server eine Datei `.ftp-deploy-sync-state.json` ab. Darüber merkt er sich, was schon
  hochgeladen ist. Die Datei bitte nicht löschen, sonst wird beim nächsten Lauf alles neu übertragen.
- Gelöscht werden nur Dateien, die der Workflow selbst hochgeladen hat. Fremde Dateien im Zielverzeichnis bleiben liegen.
- Für Seiten mit echtem Inhalt in Deutschland ist ein Impressum (§ 5 DDG) und ggf. eine Datenschutzerklärung nötig.
