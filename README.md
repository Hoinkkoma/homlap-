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

### Grafana 📊

**Zweck:** Visualisierung & Dashboarding

**URL:** `http://[LOKAL]:3002`

**Dashboards:**
- System Overview (CPU, RAM, Disk, Network)
- Proxmox Resources (VMs, Container, Storage)
- Docker Container Stats
- Netzwerk-Performance

---

### Loki 📝

**Zweck:** Zentrale Log-Aggregation

**URL:** `http://[LOKAL]:3100`

**Log-Quellen:**
- Systemd Logs (alle Server)
- Docker Container Logs
- Applikations-Logs
- Reverse Proxy Logs

**Retention:** TBD (konfigurierbar)

---

### Uptime Kuma 💚

**Zweck:** Service-Monitoring & Status-Page

**URL:** `http://[LOKAL]:3001`

**Monitore:**
- HTTP(S) Endpoints
- TCP Ports
- ICMP Pings
- SSL Certificates

---

### Scrutiny 🔧

**Zweck:** SMART-Festplattenüberwachung

**URL:** `http://[LOKAL]:8085`

**Überwacht:**
- SSD Zustand & Lebensdauer
- HDD Health & Fehler
- SMART Alerts
- Predictive Failure

---

## 🐳 Dienste & Anwendungen

### Auf Proxmox gehostet:

| Dienst | Typ | URL | Beschreibung |
|--------|-----|-----|-------------|
| **Jellyfin** | Media Server | `http://[LOKAL]:8096` | Filme, Serien, Musik |
| **Forgejo** | Git Server | `http://[LOKAL]:3030` | Self-hosted Git (Repos, Issues) |
| **FileBrowser** | File Manager | `http://[LOKAL]:8082` | Webbasierte Dateiverwaltung |
| **Vaultwarden** | Password Manager | `https://[LOKAL]:8000` | Self-hosted Passwort-Manager |
| **Immich** | Photo Management | Private IP | Foto/Video-Backup & Galerie |
| **Netdata** | Monitoring | Private IP | Real-time System Monitoring |
| **IT-Tools** | Toolbox | Private IP | Utility-Suite |
| **ClamAV** | Antivirus | Private IP | Malware-Scanning |

---

## 🖧 Netzwerk

### Pi-hole 🚫

**Zweck:** DNS Filter & Werbeblocker

**Features:**
- Zentrale DNS-Verwaltung
- Werbeblocker (Block-Listen)
- Lokale DNS-Records
- DHCP Server

---

### Tailscale 🔒

**Zweck:** Sicheres VPN-Netzwerk

**Topologie:** Homelab als VPN-Hub
- Zentrale Verbindung aller Systeme
- Sicherer Remote-Zugriff (Mobile/Extern)
- Zero-Trust Networking
- Verschlüsselte Kommunikation

**Clients:**
- Debian Server (Hub)
- Proxmox Server
- Mobile Geräte (Phone, Tablet)
- Laptops (Home/Work)

---

### Netzwerk-Hardware

| Gerät | Modell | Aufgabe |
|-------|--------|--------|
| Router | Fritzbox | Internet-Gateway, DHCP, WiFi |
| Switches | (TBD) | Netzwerk-Segmentierung |
| Access Points | (TBD) | WiFi-Coverage |

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
│   └── jellyfin.md / forgejo.md ..... Dienst-spezifische Docs
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
│   └── security.md .................. Sicherheitsrichtlinien
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

### Backup-Strategie

| Komponente | Typ | Häufigkeit | Ziel | Aufbewahrung |
|-----------|-----|-----------|------|-------------|
| Debian Server | Full Backup | Täglich | External USB | 7 Tage |
| Proxmox VMs | Snapshot | Täglich | Proxmox Backup Server | 14 Tage |
| LXC Container | Snapshot | Täglich | Proxmox Backup Server | 7 Tage |
| Konfigurationen | Git | Alle Änderungen | GitHub | Unbegrenzt |
| Wichtige Daten | Incremental | Täglich | NAS / Cloud | 30 Tage |

### Recovery-Prozedur

1. **VM-Recovery:** Proxmox Backup Server → Restore
2. **Config-Recovery:** Git Commit → Rollback
3. **Daten-Recovery:** Snapshots oder Backups
4. **Automatisierte Tests:** Monatliche Restore-Tests

---

## 🚀 Roadmap

### Phase 1 ✅ (Aktuell)
- [x] Basis-Infrastruktur (Debian + Proxmox)
- [x] Monitoring-Stack (Prometheus, Grafana, Loki)
- [x] Zentrale Verwaltung (Cockpit, Homepage)
- [x] Dokumentation

### Phase 2 (Geplant)
- [ ] Alertmanager Integration
- [ ] Ansible Configuration Management
- [ ] Automatisierte Deploys
- [ ] CI/CD Pipeline
- [ ] Advanced Monitoring (Node Exporter, Proxmox Exporter)
- [ ] Blackbox Monitoring
- [ ] Metrics Export für externe Tools

### Phase 3 (Zukünftig)
- [ ] Kubernetes Setup (Optional)
- [ ] Service Mesh (Optional)
- [ ] Multi-Node Proxmox Cluster
- [ ] Hochverfügbarkeit (HA)
- [ ] Geo-Redundante Backups
- [ ] Advanced Security (Vault, Secrets Management)

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
