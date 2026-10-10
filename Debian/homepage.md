# Homepage — kurze Beschreibung

Die Homepage dient als zentrale Dashboard- und Startseite für das Homelab. Sie sammelt zentrale Dienste und ermöglicht einen schnellen Überblick über Betrieb, Services und Verwaltungsseiten.

---

## Zweck

- Übersicht über wichtige Dienste im Homelab
- Verknüpfung zu Monitoring-, Portainer- und Verwaltungsoberflächen
- zentrale Landingpage für lokale und Remote-Zugriffe
- Integration mit Druck- oder Admin-Workflows

---

## Typische Einsatzgebiete

- Proxmox WEB-UI
- Grafana / VictoriaMetrics
- Portainer / Dockge
- AdGuard Home
- Resilio / Datei- und Sync-Dienste
- weitere interne Anwendungen

---

## Betriebsprinzipien

- Nur interne, dokumentierte Endpunkte verlinken
- Zugang über Tailscale oder lokales LAN
- keine produktiven Adminlinks ohne Authentifizierung
- keine Zugangsdaten oder kritischen Tokens direkt in Dashboards hinterlegen

---

## Integration

Die Homepage sollte nur auf bekannte, gut gepflegte Endpunkte verweisen. Bei einer Integration mit Druck- oder Print-Werkzeugen ist die App als "Read-only Dashboard" zu betreiben, während die eigentliche Druckausgabe über die spezifischen Print-Services und CUPS-/CUPS-ähnliche Backends erfolgt.

---

## Hinweise

- Das Dashboard ist ein Bedienungs- und Überblickstool, kein Sicherheits- oder Zugriffskontrollsystem.
- Die eigentliche Authentifizierung und Zugriffskontrolle liegt bei den jeweiligen Diensten.
- Für die Verwaltung sensibler Dienste sind separate, absichernde Zugangswege vorzuziehen.
