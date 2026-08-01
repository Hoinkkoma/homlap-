# 🏠 Hoinkkoma Homelab Dokumentation

Moin moin 👋
dies ist die Dokumentation meines Homelabs.

Das Homelab ist in zwei Hauptbereiche aufgeteilt:

* **Debian Monitoring Server** → Verwaltung, Monitoring und zentrale Überwachung
* **Proxmox Server** → Hosting der produktiven Dienste (VMs, LXC, Docker)

Als zentrale Anlaufstelle dient das **Homepage Dashboard (gethomepage)**.
Dort werden alle Dienste, Links und Statusinformationen gebündelt.

---

# Architektur Übersicht

```text
                         Internet
                             |
                          Router
                             |
                       Tailscale VPN
                             |
              +--------------+--------------+
              |                             |
              |                             |
 Debian Monitoring Server              Proxmox Server
 Verwaltung / Überwachung             Dienste / Anwendungen
              |                             |
              |                             |
       Prometheus                    Docker / VMs / LXC
       Grafana                       Portainer
       Loki                          Jellyfin
       Uptime Kuma                   Forgejo
       Scrutiny                      FileBrowser
                                     Vaultwarden
```

---

# 🖥 Server 1 — Debian Monitoring Server

## Zweck

Der Debian Server ist die zentrale Management- und Monitoring-Plattform.

Aufgaben:

* Überwachung aller Systeme
* Sammlung und Speicherung von Metriken
* Visualisierung über Dashboards
* zentrale Logverwaltung
* Überwachung der Dienste
* Verwaltung des Homelabs

## Netzwerk

```text
IP: 100.113.28.9
```

## Verwaltung

Cockpit:

```text
https://100.113.28.9:9090
```

---

# Monitoring Dienste (Debian)

## Grafana

URL:

```text
http://100.113.28.9:3002
```

Aufgabe:

* Dashboards
* Visualisierung der Metriken
* Auswertung von Systemdaten
* zukünftige Alarmierungen

---

## Prometheus

URL:

```text
http://100.113.28.9:9091
```

Aufgabe:

* Sammlung von Metriken
* Zeitreihendatenbank
* Überwachung von:

  * Debian Server
  * Proxmox Server
  * Docker Containern

---

## Loki

URL:

```text
http://100.113.28.9:3100
```

Aufgabe:

* zentrale Speicherung von Logs
* Fehleranalyse
* Suche und Auswertung von Systemereignissen

---

## Uptime Kuma

URL:

```text
http://100.113.28.9:3001
```

Aufgabe:

Überwachung der Erreichbarkeit:

* HTTP
* TCP
* ICMP
* Ports
* Dienste

---

## Scrutiny

URL:

```text
http://100.113.28.9:8085
```

Aufgabe:

SMART-Festplattenüberwachung:

* SSD Zustand
* HDD Zustand
* Fehlererkennung
* Lebensdauerüberwachung

---

# 🖥 Server 2 — Proxmox Server

## Zweck

Der Proxmox Server stellt die Plattform für alle produktiven Dienste bereit.

Aufgaben:

* virtuelle Maschinen
* LXC Container
* Docker Dienste
* Storage Verwaltung
* Netzwerkverwaltung

## Netzwerk

```text
IP: 100.83.105.59
```

## Webinterface

```text
https://100.83.105.59:8006
```

---

# Dienste auf Proxmox

## Portainer

URL:

```text
http://100.83.105.59:9000
```

Aufgabe:

Docker Verwaltung:

* Container
* Images
* Netzwerke
* Volumes

---

## Jellyfin

URL:

```text
http://100.83.105.59:8096
```

Aufgabe:

Medienserver:

* Filme
* Serien
* Musik
* Streaming

---

## Forgejo

URL:

```text
http://100.83.105.59:3030
```

Aufgabe:

Self-hosted Git Server:

* Quellcode
* Konfigurationen
* Backups
* Dokumentation

---

## FileBrowser

URL:

```text
http://100.83.105.59:8082
```

Aufgabe:

Webbasierte Dateiverwaltung.

---

## Vaultwarden

URL:

```text
https://192.168.178.88:8000
```

Aufgabe:

Privater Passwortmanager.

Hinweis:

Der Dienst befindet sich aktuell auf einer lokalen IP und benötigt gegebenenfalls Zugriff über Tailscale oder lokales Netzwerk.

---

# Netzwerk

## Pi-hole

Aufgabe:

* DNS Filter
* Werbeblocker
* lokale DNS Verwaltung

---

## Tailscale

Aufgabe:

VPN Netzwerk für:

* sicheren Fernzugriff
* Verbindung der Homelab Systeme
* Zugriff auf interne Dienste

---

# Homepage Dashboard

Das Homepage Dashboard ist die zentrale Übersicht.

Struktur:

```text
Homelab

├── Monitoring Debian
│   ├── Grafana
│   ├── Prometheus
│   ├── Loki
│   ├── Uptime Kuma
│   └── Scrutiny
│
├── Proxmox
│   ├── Proxmox Web
│   ├── Portainer
│   ├── Jellyfin
│   ├── Forgejo
│   └── FileBrowser
│
├── Netzwerk
│   ├── Pi-hole
│   └── Tailscale
│
└── Verwaltung
    ├── Cockpit
    └── GitHub Dokumentation
```

---

# Dokumentation & Backup

Die Dokumentation wird über Git verwaltet.

Struktur:

```text
README.md

├── Netzwerk
├── Proxmox
├── Debian-Monitoring
├── Docker
├── Wartung
└── Fehlerbehebung
```

---

# Backup Strategie

Geplant:

* regelmäßige VM/LXC Backups
* Proxmox Backup Server
* Versionskontrolle über Git
* Offsite Backup wichtiger Daten
* regelmäßige Wiederherstellungstests

---

# Geplante Erweiterungen

## Monitoring

* Alertmanager
* Node Exporter
* Proxmox Exporter
* Blackbox Exporter

## Infrastruktur

* Proxmox Backup Server
* automatisierte Backups
* bessere Alarmierung

## Verwaltung

* automatische Updates
* Ansible Configuration Management

---

# Wartung

Regelmäßig prüfen:

* Docker Container Updates
* Proxmox Updates
* Backup Status
* SMART Werte
* Monitoring Dashboards
* Zertifikate
* Berechtigungen

---

# Verantwortlichkeiten

Repository:

```text
Hoinkkoma Homelab
```

Administration:

```text
Betreiber des Homelabs
```

Änderungen:

* über Git Commits
* Pull Requests
* Dokumentierte Änderungen

---

# Letzte Aktualisierung

```text
2026-08-01
```

Diese Dokumentation ist ein lebendes Dokument und wird mit dem Ausbau des Homelabs erweitert.
