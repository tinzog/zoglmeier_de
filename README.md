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

### Einmalige Einrichtung

1. **Neues Verzeichnis für die statische Seite anlegen** (netcup WCP → Webhosting → Plesk → Dateien),
   z. B. `/zoglmeier-static/`. So bleibt WordPress unangetastet und kann bei Bedarf zurückgeschaltet werden.

2. **FTP-Zugang** in Plesk anlegen bzw. die vorhandenen Zugangsdaten nachsehen
   (*Websites & Domains → FTP-Zugang*). Den Zugang am besten auf das neue Verzeichnis beschränken.

3. **GitHub-Secrets** anlegen (Repo → *Settings → Secrets and variables → Actions → Secrets*):

   | Secret         | Beispiel                       |
   |----------------|--------------------------------|
   | `FTP_SERVER`   | Hostname des netcup-Webspace (aus dem WCP, oder `zoglmeier.de`) |
   | `FTP_USERNAME` | FTP-Benutzername               |
   | `FTP_PASSWORD` | FTP-Passwort                   |

4. **GitHub-Variable** anlegen (gleiche Seite, Tab *Variables*):

   | Variable         | Beispiel              |
   |------------------|-----------------------|
   | `FTP_SERVER_DIR` | `/zoglmeier-static/` (mit Slash am Ende; relativ zum FTP-Root des Benutzers) |

5. Workflow einmal manuell laufen lassen und prüfen, ob die Dateien im Verzeichnis liegen.

6. **Dokumentenstamm umstellen**: Plesk → *Websites & Domains → zoglmeier.de → Hosting-Einstellungen →
   Dokumentenstamm* auf `zoglmeier-static` setzen. Ab dann liefert zoglmeier.de die statische Seite aus.

### HTTPS

HTTPS läuft bereits über das Let's-Encrypt-Zertifikat von netcup/Plesk, inklusive Weiterleitung von HTTP auf HTTPS
und HSTS. Daran ändert sich durch den Wechsel des Dokumentenstamms nichts. Falls nach dem Umstellen die Weiterleitung
fehlt: Plesk → *Hosting-Einstellungen* → „Permanente, SEO-sichere 301-Weiterleitung von HTTP zu HTTPS“ aktivieren.

### Hinweise

- Der Workflow legt auf dem Server eine Datei `.ftp-deploy-sync-state.json` ab. Darüber merkt er sich, was schon
  hochgeladen ist. Die Datei bitte nicht löschen, sonst wird beim nächsten Lauf alles neu übertragen.
- Gelöscht werden nur Dateien, die der Workflow selbst hochgeladen hat. Fremde Dateien im Zielverzeichnis bleiben liegen.
- Für Seiten mit echtem Inhalt in Deutschland ist ein Impressum (§ 5 DDG) und ggf. eine Datenschutzerklärung nötig.
