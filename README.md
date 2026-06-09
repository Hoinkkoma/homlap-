# 🏠 Homlap - Mein dezentrales Heimnetzwerk-Projekt 
 ***Ist  Aktuell in  Arbeit*** 

Dies ist mein persönliches Homlap-Projekt (Home Lab). Ein sicheres, experimentelles und skalierbares Heimnetzwerk-Setup mit Fokus auf **Sicherheit**, **Ausfallsicherheit** und **Kontinuierliche Verbesserung**.

---

## 📋 Inhaltsverzeichnis

- [Übersicht](#übersicht)
- [Netzwerk-Architektur](#netzwerk-architektur)
- [Kernkomponenten](#kernkomponenten)
- [Server & Services](#server--services)
- [Design-Prinzipien](#design-prinzipien)
- [Deployment & Betrieb](#deployment--betrieb)
- [Sicherheitskonzept](#sicherheitskonzept)
- [Monitoring & Wartung](#monitoring--wartung)
- [Nächste Schritte](#nächste-schritte)
- [Ressourcen](#ressourcen)

---

## 🎯 Übersicht

**Homlap** ist ein zentrales  Heimnetzwerk-Setup bestehend aus:
- Mehren   physische  Servern 
- Sichere Netzwerk-Infrastruktur mit VPN-Overlay
- Self-hosted Services und Anwendungen
- Automatisierte Monitoring- und Backup-Lösungen
- Mobile und Desktop-Clients

**Kernziele:**
✅ Sicherheit durch Verschlüsselung und Segmentierung  
✅ Datenschutz durch Self-Hosting  
✅ Experimentierplattform für neue Technologien  
✅ Höchste Verfügbarkeit kritischer Services  
✅ Vollständige Kontrollierbarkeit aller Infrastruktur-Komponenten

---

## 🏗️ Netzwerk-Architektur

Die detaillierte Architektur ist in der Datei `homlao.drawio` dokumentiert (3 Seiten):

### 📄 **Seite 1: Komplette Netzwerk-Topologie**
Zeigt die Verbindungen zwischen:
- Zentralem Router (FritzBox)
- Servern und Speichersystemen
- Mobilen Geräten und Workstations
- VPN-Overlay-Netzwerk (Tailscale)
- Externen Netzwerk-Switches

### 📄 **Seite 2: Debian Server Mini - Services**
Detaillierte Übersicht aller Services auf dem Debian Mini Server:
- Netzwerk-Services (Tailscale, Pi-hole)
- Monitoring & Automation (Homepache, Ukuma, Netdata)
- Dashboard & Visualisierung (Glance, it-tools)
- Datenschutz (ClamAV, Immich)

### 📄 **Seite 3: Verfügbare Services & Anwendungen**
Vollständige Liste aller verfügbaren Services mit:
- Service-Kategorien (Netzwerk, Storage, Automation, etc.)
- Abhängigkeiten zwischen Services
- Verfügbarkeitsstatus

---

## 🔧 Kernkomponenten

### 🖥️ **Server & Storage**

| Komponente | Modell | Rolle | Status |
|-----------|--------|-------|--------|
| **Debian Server  | Lenovo Tinkcenter Mini | Zentrale Verwaltung, Services | ✅ Aktiv |
| **TrueNAS Server  | Lenovo Tinkcenter Tower | Storage, Backup, Archivierung | ✅ Aktiv |
| **Router** | AVM FritzBox | Netzwerk-Hub, DHCP, DNS | ✅ Aktiv |

### 📱 **Mobilgeräte & Peripherie**

| Gerät | Modell | Funktion | Verbindung |
|------|--------|---------|-----------|
| **Smartphone** | Nothing Phone 2a | Primäres mobiles Gerät | WiFi + Tailscale VPN |
| **Kopfhörer** | Nothing Buds 2a | Audio  | Bluetooth |
| **Smartwatch** | Xiaomi Watch | Monitoring & Alerts |   Bluetooth |

### 💻 **Laptops & Workstationen**

| Gerät | Modell | Zweck |
|------|--------|-------|
| **ThinkPad T490s** | Lenovo | Admin, Entwicklung, Management |

### 🌐 **Netzwerk-Infrastruktur**

| Komponente | Beschreibung |
|-----------|------------|
| **FritzBox 7530+** | WLAN-Router, Zentrale Netzwerkverwaltung |
| **Netzwerk-Switches** | Switch 1 & 2 für physische Konnektivität |
| **Tailscale Netz** | VPN-Overlay für sichere Fernverbindungen |
| **DNS/DHCP** | Pi-hole + FritzBox hybrid Setup |

---

## 🚀 Server & Services

### 🖥️ **Debian Server Mini** - Zentrale Verwaltung

#### System-Informationen
- **OS**: Debian 12 (Bookworm)
- **Rolle**: Zentrale Service-Plattform
- **Services**: 10+ containerisierte Anwendungen
- **Uptime-Ziel**: 99.5%

#### Installierte Services

| Service | Version | Funktion | Port | Status |
|---------|---------|---------|------|--------|
| **Tailscale** | Latest | VPN-Netzwerk | vpn | ✅ |
| **Homepache** | Latest | Home Automation Dashboard | 3000 | ✅ |
| **Pi-hole** | Latest | DNS-Filter & Ad-Blocker | 80/443 | ✅ |
| **Ukuma** | Latest | System Monitoring | 5081 | ✅ |
| **Glance** | Latest | Dashboard | 8080 | ✅ |
| **Netdata** | Latest | Real-time Monitoring | 19999 | ✅ |
| **it-tools** | Latest | IT-Utilities | 8081 | ✅ |
| **Hermes Agent** | Latest | Automation Agent | 5000 | ✅ |
| **Immich** | Latest | Foto/Video Management | 2283 | ✅ |
| **ClamAV** | Latest | Antivirus Scanner | daemon | ✅ |

#### Service-Beschreibungen

**🔐 Tailscale** (VPN-Netzwerk)
- Sichere verschlüsselte Kommunikation zwischen Geräten
- Ermöglicht Fernzugriff ohne Port-Forwarding
- Zero-Trust-Netzwerk-Modell
- Unterstützt 2FA und Device Authorization

**🏠 Homepache** (Home Automation)
- Zentrale Verwaltung von Smart-Home-Geräten
- Systemüberwachung und Status-Dashboard
- Automation und Regelwerk
- Benachrichtigungen und Alerts

**🚫 Pi-hole** (DNS & Security)
- DNS-Filter und Ad-Blocker für alle Netzwerk-Geräte
- Blockiert Malware auf DNS-Ebene
- Zentrale DNS-Verwaltung
- Query-Logging und Statistiken

**📊 Ukuma** (System Monitoring)
- Überwachung von CPU, RAM, Festplatte, Netzwerk
- Systemressourcen-Management
- Performance-Metriken in Echtzeit
- Alerts bei Ressourcen-Engpässen

**📈 Netdata** (Performance Monitoring)
- Real-time Systemanalyse
- Detaillierte Performance-Metriken
- Trend-Analyse und Vorhersagen
- Custom Alerts und Webhooks

**🎨 Glance** (Dashboard)
- Schnelle Übersicht über Systemstatus
- Aggregate Services-Dashboard
- Einfache Web-UI
- Mobile-responsive Design

**🛠️ it-tools** (IT-Utilities)
- IP-Rechner und Netzwerk-Tools
- JSON/YAML-Validator
- Base64 Encoder/Decoder
- Verschiedene nützliche Conversion-Tools

**🤖 Hermes Agent** (Automation)
- Automatisierung von wiederkehrenden Aufgaben
- Inter-Service-Kommunikation
- Task-Scheduling
- Event-basierte Automatisierung

**📸 Immich** (Media Management)
- Self-hosted Foto- und Video-Verwaltung
- Alternative zu Google Photos
- Private Medienverwaltung
- Automatische Organisation und Tagging

**🦠 ClamAV** (Antivirus)
- Malware-Scanner für das System
- Regelmäßige Systemscans
- Echtzeitüberwachung
- Integr mit anderen Services

---

### 💾 **TrueNAS Server (Tower)** - Storage & Backup

#### System-Informationen
- **OS**: TrueNAS Core
- **Rolle**: Zentrale Storage- & Backup-Lösung
- **Filesystem**: ZFS mit RAID-Redundanz
- **Kapazität**: Enterprise-Skalierbarkeit
- **Uptime-Ziel**: 99.9%

#### Kernfunktionen

| Funktion | Beschreibung |
|---------|------------|
| **ZFS Filesystem** | Zuverlässiges FS mit Fehlertoleranz und Datenkonsistenz-Checks |
| **RAID-Konfiguration** | Redundante Speicherung für Ausfallsicherheit |
| **Snapshots** | Point-in-Time Backups für schnelle Recovery |
| **Replikation** | Automatische Datenreplikation zu Backup-Zielen |
| **Backup-Service** | Zentrale Backup-Verwaltung für alle Geräte |
| **Storage Pool** | Verwaltete Speicherkapazität für Archivierung |

#### Backup-Strategie

```
Debian Server Daten
    ↓
TrueNAS Backup Pool (tägliche Snapshots)
    ↓
Archivierung & Offline-Backup (wöchentlich)
```

---

## 🎯 Design-Prinzipien

### 🔒 **Sicherheit (Security First)**

**Implementierte Maßnahmen:**
- ✅ Ende-zu-Ende Verschlüsselung mit Tailscale
- ✅ Netzwerk-Segmentierung durch VLANs
- ✅ Pi-hole für DNS-Sicherheit und Malware-Blocking
- ✅ Sichere Authentifizierung (2FA/MFA wo möglich)
- ✅ ClamAV für regelmäßige Malware-Scans
- ✅ Firewall-Regeln auf allen Ebenen
- ✅ Regelmäßige Security-Updates
- ✅ Audit-Logging für kritische Operationen

### 🧪 **Experimentierbarkeit (Innovation)**

**Unterstützende Infrastruktur:**
- ✅ Modulares Setup für einfaches Testen
- ✅ Docker/Container-Deployment
- ✅ Entwicklungs- und Produktions-Umgebungen
- ✅ Schnelle Rollback-Mechanismen
- ✅ Sandbox-Umgebungen für Tests
- ✅ Version-Control für Konfigurationen

### 📈 **Skalierbarkeit (Scale Ready)**

**Architektur-Features:**
- ✅ Microservice-basierte Architektur
- ✅ Containerisierung für leichte Portabilität
- ✅ Load-Balancing für kritische Services
- ✅ Horizontale Skalierbarkeit
- ✅ Resource-Limits und Management

### 🔄 **Ausfallsicherheit (Resilience)**

**Implementierung:**
- ✅ Redundante Storage-Systeme (RAID/ZFS)
- ✅ Automatische Backups
- ✅ Disaster-Recovery-Plan
- ✅ Health-Checks auf kritischen Services
- ✅ Automatische Restart-Policies
- ✅ Monitoring & Alerting

---

## 🚀 Deployment & Betrieb

### Deployment-Prozesse

#### Docker-basiertes Deployment

```bash
# Service starten
docker-compose up -d service-name

# Logs anschauen
docker-compose logs -f service-name

# Service aktualisieren
docker-compose pull
docker-compose up -d --force-recreate
```

#### Konfigurationsmanagement

```bash
# Alle Configs sind versioniert in:
/etc/homlap/configs/
/etc/homlap/secrets/ (encrypted)
```

#### Backup & Restore

```bash
# Automatisches Backup (täglich um 2 Uhr nachts)
/opt/homlap/backup/daily-backup.sh

# Manuelles Backup
/opt/homlap/backup/manual-backup.sh

# Restore von Snapshot
zfs rollback tank/backup@snapshot-name
```

### Wartungs-Fenster

| System | Wartung | Fenster | Häufigkeit |
|--------|---------|--------|-----------|
| Debian Server | Updates & Patches | Sonntag 03:00 UTC | Wöchentlich |
| TrueNAS | Scrub & Maintenance | Samstag 02:00 UTC | Monatlich |
| Network | Firmware-Updates | Nach Bedarf | Ad-hoc |

---

## 🔐 Sicherheitskonzept

### Sicherheits-Layer

#### Layer 1: Netzwerk-Sicherheit
- Firewall-Regeln (UFW auf Debian, FritzBox)
- VPN-Only Zugriff von außen
- Port-Whitelisting
- DDoS-Protection

#### Layer 2: Service-Sicherheit
- Service-Isolation (Docker/Container)
- Resource-Limits
- Security-Scanning (ClamAV)
- Regelmäßige Patching

#### Layer 3: Daten-Sicherheit
- Verschlüsselte Backups
- ZFS Checksums
- RAID-Redundanz
- Geo-redundante Backups (geplant)

#### Layer 4: Zugriffs-Kontrolle
- 2FA/MFA für kritische Services
- Role-Based Access Control (RBAC)
- API-Keys mit Expiration
- Audit-Logging

### Threat-Model

| Threat | Mitigation | Status |
|--------|-----------|--------|
| Unbefugter Zugriff | Tailscale VPN + 2FA | ✅ Implementiert |
| Malware-Infizierung | ClamAV + Netzwerk-Segmentierung | ✅ Implementiert |
| Datenverlust | RAID/ZFS + Backups | ✅ Implementiert |
| Netzwerk-Angriffe | Firewall + DoS-Protection | ✅ Implementiert |
| Service-Ausfälle | Health-Checks + Auto-Restart | ✅ Implementiert |

---

## 📊 Monitoring & Wartung

### Monitoring-Stack

```
Netdata (Real-time Metrics)
    ↓
Ukuma (Visualization)
    ↓
Glance (Dashboard)
    ↓
Alerts (Hermes Agent)
```

### Key Metrics

| Metrik | Target | Alert-Level | Status |
|--------|--------|-------------|--------|
| CPU Usage | < 80% | > 90% | 📊 Tracked |
| Memory Usage | < 80% | > 90% | 📊 Tracked |
| Disk Space | < 80% | > 90% | 📊 Tracked |
| Network Latency | < 50ms | > 100ms | 📊 Tracked |
| Service Health | 100% | < 95% | 📊 Tracked |

### Backups & Recovery

**Backup-Zeitplan:**
- Tägliche inkrementelle Backups (02:00 UTC)
- Wöchentliche vollständige Backups (Samstag 02:00 UTC)
- Monatliche Archive (1. des Monats 03:00 UTC)

**Recovery-Zeiten (RTO/RPO):**
- RTO (Recovery Time Objective): < 1 Stunde
- RPO (Recovery Point Objective): < 24 Stunden

---

## ✅ Nächste Schritte

### 🔄 In Planung

- [ ] Ansible Playbooks für vollständige Infrastruktur-Automation
- [ ] Terraform Code für IaC (Infrastructure as Code)
- [ ] CI/CD Pipeline für Service-Deployments
- [ ] Kubernetes Migration (längerfristig)
- [ ] Geo-redundante Backups zu Cloud-Provider
- [ ] API-Dokumentation für alle Services
- [ ] Runbook-Dokumentation für häufige Probleme
- [ ] Load-Balancing für redundante Services
- [ ] Container-Registry für Custom-Images
- [ ] Incident-Response-Plan

### 🚀 Mittelfristig

- [ ] Multi-Node Kubernetes Cluster
- [ ] Service-Mesh (Istio/Linkerd)
- [ ] Advanced Monitoring & Alerting (Prometheus + Grafana)
- [ ] GitOps Workflow (ArgoCD)
- [ ] Zero-Trust Security Model vollständig
- [ ] DR-Site für Disaster-Recovery

### 🌟 Langfristig

- [ ] Vollständige Cloud-Integration
- [ ] Multi-Region Setup
- [ ] Enterprise-Grade Compliance (GDPR, etc.)
- [ ] Advanced AI/ML Monitoring

---

## 📚 Ressourcen

### 📖 Dokumentation
- [Tailscale Docs](https://tailscale.com/kb/)
- [TrueNAS Documentation](https://www.truenas.com/docs/)
- [Pi-hole Documentation](https://docs.pi-hole.net/)
- [Netdata Documentation](https://learn.netdata.cloud/)

### 🔗 Links
- **Repository**: https://github.com/Hoinkkoma/homlap-
- **Netzwerk-Diagramme**: `homlao.drawio`
- **License**: Apache License 2.0

### 👤 Kontakt & Support
- **Maintainer**: Hoinkkoma
- **Status**: Aktive Entwicklung
- **Letztes Update**: 2026-06-09

---

## 📝 Lizenz

Dieses Projekt ist unter der **Apache License 2.0** lizenziert. Siehe [LICENSE](LICENSE) für Details.

---

**Made with ❤️ for home automation and self-hosting**
