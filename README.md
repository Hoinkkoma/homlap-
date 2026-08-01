# Homelab Doku
moin moin das ist die doku meines homlaps 
## Übersicht

Dieses Homelab ist in zwei Hauptbereiche aufgeteilt:

- **Debian Monitoring Server** – Verwaltung, Monitoring und zentrale Überwachung
- **Proxmox Server** – Hosting der produktiven Dienste (VMs, LXC, Docker)

Als zentrale Anlaufstelle dient das "Homepage"-Dashboard (gethomepage), das Links und Statusinformationen zu allen Diensten bündelt.

---

## Architektur (Übersicht)

                     Internet
                         |
                      Router
                         |
                    Tailscale VPN
                         |
          +--------------+--------------+
          |                             |
   Debian Monitoring Server       Proxmox Server
   Verwaltung / Überwachung       Dienste / Anwendungen
          |                             |
  Prometheus                       Docker / VMs / LXC
  Grafana                          Portainer
  Loki                             Jellyfin
  Uptime Kuma                      Forgejo
  Scrutiny                         FileBrowser
                                   Vaultwarden

---

## Server 1 — Debian Monitoring Server

Zweck:

- Zentrale Management- und Monitoring-Plattform für das Homelab
- Sammlung, Visualisierung und Langzeitspeicherung von Metriken
- Zentrale Logverwaltung, Dashboarding und Serviceüberwachung

Netzwerkadresse:

```
100.113.28.9
```

Verwaltung:

```
Cockpit: https://100.113.28.9:9090
```

Kernfunktionen:

- Systemverwaltung und Updates
- Diensteverwaltung und Logs
- Metrik-Sammlung (Prometheus)
- Visualisierung (Grafana)
- Log-Aggregation (Loki)
- Service-Checks / Erreichbarkeitsmonitoring (Uptime Kuma)
- SMART-Überwachung (Scrutiny)

---

### Monitoring-Dienste (Debian)

Grafana

- URL: `http://100.113.28.9:3002`
- Aufgabe: Visualisierung, Dashboards, Alerts (gegebenenfalls über Alertmanager)

Prometheus

- URL: `http://100.113.28.9:9091`
- Aufgabe: Sammlung von Metriken (Debian, Proxmox, Container)

Loki

- URL: `http://100.113.28.9:3100`
- Aufgabe: Zentrale Logspeicherung, Suche und Analyse von Logs

Uptime Kuma

- URL: `http://100.113.28.9:3001`
- Aufgabe: Verfügbarkeitstests (HTTP, TCP, ICMP, Portchecks)

Scrutiny

- URL: `http://100.113.28.9:8085`
- Aufgabe: SMART-Monitoring für Festplatten (Zustand, Fehler, Lebensdauer)

---

## Server 2 — Proxmox Server

Zweck:

- Plattform für VMs, LXC-Container und produktive Dienste
- Verwaltung von Storage, Netzwerk und Ressourcen für gehostete Dienste

Netzwerkadresse:

```
100.83.105.59
```

Weboberfläche:

```
https://100.83.105.59:8006
```

---

### Auf dem Proxmox-Server gehostete Dienste

Portainer

- URL: `http://100.83.105.59:9000`
- Aufgabe: Verwaltung von Docker-Containern, Images, Netzwerken und Volumes

Jellyfin

- URL: `http://100.83.105.59:8096`
- Aufgabe: Medienserver für Filme, Serien und Musik

Forgejo

- URL: `http://100.83.105.59:3030`
- Aufgabe: Self-hosted Git-Server (Code, Konfigurationen, Backups)

FileBrowser

- URL: `http://100.83.105.59:8082`
- Aufgabe: Webbasierte Dateiverwaltung

Vaultwarden

- URL: `https://192.168.178.88:8000`
- Aufgabe: Privater Passwort-Manager (achten: lokale IP, ggf. Tailscale-Zugriff benötigen)

---

## Netzwerkkomponenten

Pi-hole

- Aufgabe: DNS-Filterung und Werbeblocker im lokalen Netzwerk

Tailscale

- Aufgabe: VPN-Lösung für sicheren Fernzugriff und Vernetzung der Homelab-Geräte

Hinweis: Tailscale ermöglicht sicheren Zugriff auf interne Dienste von außen, unter Beachtung von ACLs und Expose-Settings.

---

## Homepage Dashboard

Das Homepage-Dashboard bündelt Links und Statusinformationen zu allen Bereichen des Homelabs. Empfohlene Struktur:

- Homelab
  - Monitoring (Grafana, Prometheus, Loki, Uptime Kuma, Scrutiny)
  - Proxmox (Proxmox Web, Portainer, Jellyfin, Forgejo, FileBrowser)
  - Netzwerk (Pi-hole, Tailscale)
  - Verwaltung (Cockpit, GitHub Dokumentation)

---

## Dokumentation & Backup

Die Dokumentation wird in diesem Repository verwaltet (README und thematische Unterordner):

```
README.md
└── Netzwerk/
└── Proxmox/
└── Debian-Monitoring/
└── Docker/
└── Wartung/
└── Fehlerbehebung/
```

Backup-Strategie (Empfehlung):

- Regelmäßige, automatisierte Backups der wichtigen VMs/Container (z.B. Proxmox Backup Server)
- Versionskontrolle für Konfigurationsdateien (Forgejo / Git)
- Offsite-Backups kritischer Daten
- Testen der Wiederherstellung in definierten Intervallen

---

## Geplante Erweiterungen

Monitoring

- Alertmanager (Prometheus Alerting)
- Node Exporter (weitere Host-Metriken)
- Proxmox Exporter
- Blackbox Exporter (externes Uptime-Monitoring)

Infrastruktur

- Proxmox Backup Server
- Automatisierte Backup-Pipelines
- Verbesserte Alarmierung und On-Call-Prozesse

Verwaltung

- Automatisierte Updates (Staging → Prod)
- Zentrales Configuration Management (z. B. Ansible)

---

## Wartung (regelmäßig)

- Docker-Container aktualisieren
- Proxmox-Updates installieren und überprüfen
- Backup-Logs und Integrität prüfen
- SMART-Werte prüfen (Scrutiny)
- Monitoring-Dashboards und Alerts prüfen
- Berechtigungen und Zertifikate erneuern

---

## Verantwortlichkeiten & Kontakt

- Repo / Dokumentation: Hoinkkoma
- Administration: Betreiber des Homelabs (bei Fragen: per Git Issue im Forgejo / Git-Repo dokumentieren)

---

## Letzte Aktualisierung

2026-08-01

---

Hinweis: Diese Dokumentation ist als lebendes Dokument gedacht. Für Änderungen bitte Pull Requests (oder in Forgejo entsprechende Änderungen) nutzen und Versionierung beibehalten.
