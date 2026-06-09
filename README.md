# Homlap

Dies ist der Anfang meines persönlichen Homlap-Projekts. Das Projekt konzentriert sich auf **Sicherheit** und **Experimentierbarkeit**.

## Übersicht

Mein Homlap ist ein dezentrales Heimnetzwerk-Setup, das aus verschiedenen Geräten und Servern besteht:

### Kernkomponenten

- **Router**: Fritzbox als zentraler Netzwerk-Hub
- **Mobilgeräte**: Nothing Phone 2a, Nothing Buds 2a, Xiaomi Watch
- **Laptops**: ThinkPad T490s
- **Server**:
  - Debian Server (Mini) - Läuft Tailscale, Homepache, Pi-hole, Ukuma
  - TrueNAS Server (Tower) - Speicher- und Backup-Lösung
- **Netzwerk-Infrastruktur**: Switches, Tailscale für sichere Vernetzung

## Architektur-Diagramme

Eine detaillierte Übersicht der Netzwerk-Topologie und Services finden Sie in der `homlao.drawio` Datei (3 Seiten):
1. **Seite 1**: Komplette Netzwerk-Topologie
2. **Seite 2**: Debian Server Mini Services
3. **Seite 3**: Verfügbare Services/Anwendungen

## Design-Prinzipien

### 🔒 Sicherheit
- Tailscale für verschlüsselte Fernzugriffe
- Isolierte Netzwerksegmente
- Pi-hole für DNS-Sicherheit und Ad-blocking
- Sichere Authentifizierung auf allen Ebenen

### 🧪 Experimentierbarkeit
- Modulares Setup für einfaches Testen neuer Technologien
- Docker/Container-basierte Deployments wo möglich
- Einfache Rollback- und Recovery-Mechanismen
- Separate Test- und Produktionsumgebungen

## Laufende Programme nach Server

### 🖥️ Debian Server Mini

| Programm | Beschreibung |
|----------|-------------|
| **Tailscale** | Sicheres VPN-Netzwerk für verschlüsselte Kommunikation zwischen Geräten. Ermöglicht sicheren Fernzugriff ohne Port-Forwarding. |
| **Homepache** | Home Automation & Monitoring-Dashboard. Zentrale Verwaltung von Smart-Home-Geräten und Systemüberwachung. |
| **Pi-hole** | DNS-Filter und Ad-Blocker für das gesamte Netzwerk. Blockiert Werbung und Malware auf DNS-Ebene. |
| **Ukuma** | Monitoring & Management-Tool für Systemressourcen und Dienste. Überwacht CPU, RAM, Festplatte und andere Metriken. |
| **Glance** | Dashboard-Anwendung für schnelle Übersicht über Systeminformationen und Services. |
| **Netdata** | Real-time Performance Monitoring und Visualisierung. Detaillierte Systemanalyse und Alerting. |
| **it-tools** | Sammlung von nützlichen IT-Tools (IP-Rechner, JSON-Validator, etc.). Webbasierte Utility-Sammlung. |
| **Hermes Agent** | Automatisierungs- und Management-Agent. Kommunikation zwischen Services und Automatisierung von Aufgaben. |
| **Immich** | Self-hosted Foto- und Video-Management. Alternative zu Google Photos für private Medienverwaltung. |
| **ClamAV** | Antivirus-Scanner für das System. Malware-Scans und Dateischutz. |

### 💾 TrueNAS Server (Tower)

| Programm | Beschreibung |
|----------|-------------|
| **TrueNAS Core** | Hochperformantes NAS-Betriebssystem. Speicherverwaltung, RAID, Snapshots, Replikation. |
| **ZFS Filesystem** | Zuverlässiges Dateisystem mit Fehlerbehandlung und Datenintegrität. Basis für Datensicherheit. |
| **Backup-Service** | Automatische Backups der kritischen Daten vom Debian Server und anderen Geräten. |
| **Storage Pool** | Verwaltete Speicherkapazität für zentrale Datensicherung und Archivierung. |

### 📱 Mobile Geräte & Peripherie

| Gerät | Beschreibung |
|-------|-------------|
| **Nothing Phone 2a** | Modernes Smartphone. Zugriff auf Services über mobile Apps und VPN (Tailscale). |
| **Nothing Buds 2a** | Kabellose Kopfhörer. Audio-Ausgabe für lokale Benachrichtigungen und Kommunikation. |
| **Xiaomi Watch** | Smartwatch. Monitoring von Systemstatus und Benachrichtigungen unterwegs. |

### 💻 Laptops & Arbeitsstationen

| Gerät | Beschreibung |
|-------|-------------|
| **ThinkPad T490s** | Laptop. Primäre Workstation für Administration, Entwicklung und Zugriff auf alle Services. |

### 🌐 Netzwerk-Infrastruktur

| Komponente | Beschreibung |
|-----------|-------------|
| **AVM FritzBox** | WLAN-Router und Netzwerk-Hub. Zentrale Netzwerk-Verwaltung und Connectivity. |
| **Switches** | Netzwerk-Switches (Switch 1, Switch 2). Ethernet-Verbindungen zwischen Servern und Geräten. |
| **Tailscale Netz** | VPN-Overlay-Netzwerk. Sichere Verbindungen zwischen allen Geräten unabhängig vom physischen Standort. |

## Nächste Schritte

- [ ] Dokumentation der Deployment-Prozesse
- [ ] Automation mit Ansible/Terraform
- [ ] Backup-Strategie definieren und dokumentieren
- [ ] Monitoring und Alerting einrichten
- [ ] Security Hardening durchführen
- [ ] API-Dokumentation für Services erstellen

---

**Status**: Anfang der Entwicklung  
**Zuletzt aktualisiert**: 2026-06-09