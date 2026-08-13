# 🏠 Hoinkkoma Homelab Dokumentation

**Letzte Aktualisierung:** 2026-08-13

Willkommen zur Dokumentation meines Homelabs! 👋 Hier findest du einen kompletten Überblick über die Infrastruktur, Dienste und Konfigurationen.

---

## 📋 Inhaltsverzeichnis

- [🎯 Überblick](#-überblick)
- [🏗️ Architektur](#️-architektur)
- [🖥️ Server & Hardware](#️-server--hardware)
- [📊 Monitoring & Verwaltung](#-monitoring--verwaltung)
- [🐳 Dienste & Anwendungen](#-dienste--anwendungen)
- [🖧 Netzwerk](#-netzwerk)
- [📚 Dokumentation](#-dokumentation)
- [💾 Backup & Recovery](#-backup--recovery)
- [🚀 Roadmap](#-roadmap)

---

## 🎯 Überblick

Das Homelab ist eine verteilte Infrastruktur mit zwei Hauptkomponenten:

| Komponente | Rolle | OS | Aufgaben |
|-----------|------|----|----|
| **🖥️ Debian Monitoring Server** | Verwaltung & Überwachung | Debian 12 | Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny, Homepage |
| **🔶 Proxmox Server** | Hosting-Plattform | Proxmox VE | VMs, LXC Container, Docker, Dienste |

**Zentrale Zugriffsstelle:** [Homepage Dashboard](http://[LOKAL]:80) - Single Pane of Glass für alle Dienste

---

## 🏗️ Architektur

```
                            🌐 Internet
                                |
                        [Router Fritzbox]
                                |
                   +————————————+————————————+
                   |                        |
           [Debian Monitor]            [Proxmox Server]
           (Management/Watch)          (Production/Services)
                   |                        |
        ┌──────────┼──────────┐   ┌────────┼────────┐
        |          |          |   |        |        |
    Tailscale  Prometheus  Grafana  VMs   LXC   Docker
    (VPN Hub)   Loki      Uptime Kuma    Apps  Container
               Scrutiny   Homepage
```

### Netzwerk-Topologie

- **LAN:** Fritzbox Router (DHCP, DNS via Pi-hole)
- **VPN:** Tailscale für sicheren Remote-Zugriff und Inter-Server-Kommunikation
- **DNS:** Pi-hole (zentral, mit lokalen DNS-Records)
- **Monitoring:** Prometheus → Grafana + Loki (zentrale Metriken & Logs)


### Drucker-Topologie (neu)

- Der Proxmox-Server hostet eine Debian-VM, die als Print-Gateway läuft. Diese VM hat alle nötigen Treiber (HPLIP) und CUPS installiert.
- Ein Digitus Printserver ist per LAN an das zweite Netzwerk-Interface/Port angeschlossen und verbindet sich per USB-Kabel mit dem HP OfficeJet 4558.
- Die Digitus-Box stellt den Drucker im LAN zur Verfügung (z.B. JetDirect/socket:// oder IPP). Die Debian-VM bindet den Netzwerk-Drucker ein (lpadmin / hp-setup) und bietet den Drucker im Netzwerk (CUPS/IPP) für Anwendungen an.
- Auf der Homepage (Dashboard) läuft eine Anwendung, die Druckdateien an die Debian-VM schickt (z. B. via IPP, lpr oder lp)

---

## 🖥️ Server & Hardware

### 🖥️ Server 1: Debian Monitoring Server

**Spezifikation:**
- OS: Debian 12 (Bookworm)
- Aufgaben: Management, Monitoring, zentrale Verwaltung
- Speicher: TBD
- Netzwerk: LAN Kabel (statische IP)

**Verwaltungs-Interfaces:**
| Service | URL | Port | Beschreibung |
|---------|-----|------|-------------|
| Cockpit | `https://[LOKAL]:9090` | 9090 | Webbasierte Serververwaltung |
| Prometheus | `http://[LOKAL]:9091` | 9091 | Metrik-Datenbank |
| Grafana | `http://[LOKAL]:3002` | 3002 | Dashboards & Visualisierung |
| Loki | `http://[LOKAL]:3100` | 3100 | Log-Aggregation |
| Uptime Kuma | `http://[LOKAL]:3001` | 3001 | Service-Monitoring |
| Scrutiny | `http://[LOKAL]:8085` | 8085 | SMART-Festplatten-Überwachung |
| Homepage | `http://[LOKAL]:80` | 80 | Dashboard & Links |

---

### 🔶 Server 2: Proxmox Server

**Spezifikation:**
- OS: Proxmox VE
- Aufgaben: Virtualisierung, Container, Docker-Hosting
- Speicher: ZFS Pool (RAID, Snapshots, Backups)
- Netzwerk: LAN Kabel (statische IP)

**Webinterface:**
| Service | URL | Beschreibung |
|---------|-----|-------------|
| Proxmox Console | `https://[LOKAL]:8006` | VM/LXC Management |
| Portainer | `http://[LOKAL]:9000` | Docker GUI |


#### Drucker-Setup auf Proxmox (kurz)

- Proxmox stellt Bridges für VMs bereit (z.B. vmbr0). Die Print-VM benötigt mindestens ein Bridge-Interface mit Netzwerkzugriff auf das LAN, optional ein zweites Interface, je nach physischer Verkabelung.
- Die Digitus-Printserver-Box ist an einen zweiten LAN-Port des Proxmox-Hosts / Netzwerks angeschlossen. Die Debian-VM greift über das LAN auf die Digitus-Box zu.

---

## 📊 Monitoring & Verwaltung

### Prometheus 📈

**Zweck:** Zeitreihendatenbank für Systemmetriken

**URL:** `http://[LOKAL]:9091`

**Überwachte Systeme:**
- Debian Server (Node Exporter)
- Proxmox Server (Proxmox Exporter)
- Docker Container (cAdvisor)
- Netzwerkkomponenten

**Scrape-Intervall:** 15s (Standard)

---

(der restliche Inhalt bleibt unverändert; für Details siehe die jeweiligen Unterdokumente)

---

## 📚 Dokumentation

Die Dokumentation ist modular aufgebaut:

```
📁 Hoinkkoma/homlap-/
├── README.md ......................... (diese Datei)
├── CHANGELOG.md ...................... Änderungshistorie
├── CONTRIBUTING.md ................... Kontributionsrichtlinien
├── LICENSE ........................... Apache 2.0
│
├── 📁 Debian-Monitoring/
│   ├── README.md ..................... Überblick
│   ├── prometheus-config.md .......... Prometheus Setup
│   ├── grafana-dashboards.md ......... Dashboard-Verwaltung
│   ├── loki-config.md ............... Log-Aggregation
│   └── uptime-kuma-setup.md ......... Service-Monitoring
│
├── 📁 Proxmox/
│   ├── README.md ..................... Überblick
│   ├── vm-templates.md .............. VM-Templates
│   ├── lxc-container.md ............. Container-Setup
│   ├── storage-zfs.md ............... ZFS-Storage
│   ├── backup-strategy.md ........... Backup & Recovery
│   ├── jellyfin.md / forgejo.md ..... Dienst-spezifische Docs
│   └── printer-vm.md ............... Drucker-VM & CUPS Konfiguration (neu)
│
├── 📁 Docker/
│   ├── README.md ..................... Überblick
│   ├── docker-compose.yml ........... Compose-Beispiele
│   ├── portainer-setup.md ........... Portainer-Verwaltung
│   └── container-updates.md ......... Update-Prozesse
│
├── 📁 Netzwerk/
│   ├── README.md ..................... Überblick
│   ├── pihole-config.md ............. DNS-Filter Setup
│   ├── tailscale-setup.md ........... VPN-Konfiguration
│   ├── network-diagram.md ........... Netzwerk-Topologie
│   ├── security.md .................. Sicherheitsrichtlinien
│   └── printserver-digitus.md ...... Digitus Printserver Konfiguration (neu)
│
├── 📁 Wartung/
│   ├── README.md ..................... Überblick
│   ├── checklist.md ................. Regelmäßige Checks
│   ├── update-procedure.md .......... Update-Ablauf
│   ├── certificate-management.md .... SSL/TLS Management
│   └── performance-tuning.md ........ Optimierung
│
└── 📁 Fehlerbehebung/
    ├── README.md ..................... Überblick
    ├── common-issues.md ............. Häufige Probleme
    ├── logging-debug.md ............. Debug-Techniken
    ├── backup-recovery.md ........... Disaster Recovery
    └── troubleshooting-guide.md ..... Systematische Fehlersuche
```

---

## 💾 Backup & Recovery

(gleicher Inhalt wie vorher)

---

## 🚀 Roadmap

(gleicher Inhalt wie vorher)

---

## 📞 Support & Kontakt

### Bei Fragen oder Problemen:

1. Erstelle ein **Issue** im Repository
2. Konsultiere die **Dokumentation** im entsprechenden Verzeichnis
3. Schau in **Fehlerbehebung/** nach bekannten Problemen
4. Führe einen **Pull Request** mit Improvements durch

### Contributing

Beiträge sind willkommen! Siehe [CONTRIBUTING.md](./CONTRIBUTING.md) für Details.

---

## 📝 Lizenz

Dieses Projekt ist unter der [Apache License 2.0](./LICENSE) lizenziert.

---

**Hinweis:** Diese Dokumentation ist ein lebendes Dokument und wird kontinuierlich mit dem Ausbau des Homelabs aktualisiert. Letzte Aktualisierung: **2026-08-13**
