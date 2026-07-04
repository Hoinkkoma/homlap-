# 🏠 Homlap - Dezentrales Heimnetzwerk-Projekt

**Status:** ✅ Aktive Entwicklung (Phase 7+)

Ein sicheres, experimentelles und skalierbares Heimnetzwerk-Setup mit Fokus auf **Sicherheit**, **Selbstbestimmung** und **kontinuierliche Optimierung**.

---

## 📋 Inhaltsverzeichnis

- [Übersicht](#übersicht)
- [Architektur-Übersicht](#architektur-übersicht)
- [Kernkomponenten](#kernkomponenten)
- [Server & Services](#server--services)
- [Design-Prinzipien](#design-prinzipien)
- [Deployment & Betrieb](#deployment--betrieb)
- [Sicherheitskonzept](#sicherheitskonzept)
- [Lessons Learned](#lessons-learned)
- [Nächste Schritte](#nächste-schritte)
- [Ressourcen](#ressourcen)

---

## 🎯 Übersicht

**Homlap** ist eine Two-Server-Architektur mit:

- ✅ **Zwei physische Server** (Mini-PC + Tower für Storage)
- ✅ **Sichere Netzwerk-Infrastruktur** (Tailscale VPN-Overlay)
- ✅ **Self-hosted Services** (Docker, LXC-Container, VMs)
- ✅ **Vollständiges Monitoring** (Prometheus, Grafana, Loki)
- ✅ **Automatisierte Backups** (ZFS Snapshots & Replikation)
- ✅ **Exit-Node-Routing** für mobile Geräte

**Kernziele:**

✅ Datenschutz durch Self-Hosting (keine Cloud-Abhängigkeit)  
✅ Sicherheit durch Verschlüsselung & Segmentierung  
✅ Experimentierplattform für neue Technologien  
✅ Höchste Verfügbarkeit kritischer Services  
✅ Vollständige Kontrolle über alle Komponenten

---

## 🏗️ Architektur-Übersicht

Siehe auch: [ARCHITECTURE.md](ARCHITECTURE.md) für detaillierte Dokumentation

### Two-Server-Setup

| Server | Rolle | OS | Services |
|--------|-------|-----|----------|
| **Debian Mini-PC** | Zentrale Service-Plattform | Debian 12 | Tailscale, Caddy, Pi-hole, Homepage, Monitoring |
| **Proxmox Tower** | Hypervisor, Storage, Backup | Proxmox VE | LXC-Container (Vaultwarden, Caddy, PMG), VMs |

### Netzwerk-Topologie

```
┌─────────────────────────────────────────────────┐
│                 Router (FritzBox)               │
│         DNS/DHCP, LAN-Hub (192.168.178.x)       │
└──┬────────────────────────────────────┬─────────┘
   │                                    │
   ├─→ Debian Server                    ├─→ Proxmox VE Host
   │   (Mini-PC)                        │   (Tower)
   │   ├─ Tailscale Hub                 │   ├─ LXC 103: Caddy (Reverse Proxy)
   │   ├─ Docker Services               │   ├─ LXC 102: Vaultwarden
   │   └─ Pi-hole                       │   ├─ CT105: PMG
   │                                    │   └─ ZFS Storage
   └────────────────────────────────────┘
            │
            ↓ Tailscale Overlay Network
        (P2P Routing via WireGuard)
            │
    ┌───────┴────────────┬─────────┐
    │                    │         │
   Mobile Devices    Laptops   Exit-Node Clients
  (Fedora, Phone)   (ThinkPad) (via debianserver)
```

---

## 🔧 Kernkomponenten

### Hardware

| Komponente | Modell | Funktion | Status |
|-----------|--------|---------|--------|
| **Debian Server** | Lenovo ThinkCentre Mini | Zentrale Services | ✅ Aktiv |
| **Proxmox Host** | Lenovo ThinkCentre Tower | Storage, VMs, Virtualisierung | ✅ Aktiv |
| **Router** | AVM FritzBox | DHCP, DNS, LAN-Hub | ✅ Aktiv |
| **Admin-Laptop** | Lenovo ThinkPad | Verwaltung, Entwicklung | ✅ Aktiv |

### Netzwerk-Services

| Service | Funktion | Zugriff |
|---------|---------|--------|
| **Tailscale** | VPN-Overlay (P2P WireGuard) | Verschlüsselt, Cloud-Koordination |
| **Pi-hole** | DNS-Filter, Ad-Blocking | Local + Tailscale |
| **Caddy** | Reverse Proxy (LXC 103) | HTTPS, automatische Zertifikate |
| **Vaultwarden** | Self-hosted Passwortmanager | Tailscale-only |

---

## 🚀 Server & Services

### 📱 Debian Server (Mini-PC) – Zentrale Plattform

**System:** Debian 12, ~10+ Docker-Container

**Installierte Services:**

| Service | Port | Funktion | Status |
|---------|------|---------|--------|
| Tailscale | vpn | VPN-Netzwerk, P2P-Routing | ✅ |
| Homepage Dashboard | 3000 | Zentrale Service-Übersicht | ✅ |
| Pi-hole | 80/443 | DNS-Filter, Ad-Blocker | ✅ |
| Prometheus | 9091 | Metrik-Collection | ✅ |
| Grafana | 3002 | Monitoring-Visualisierung | ✅ |
| Loki | - | Log-Aggregation | ✅ |
| Portainer | - | Docker-Management | ✅ |
| Uptime Kuma | 5081 | Service-Verfügbarkeitsprüfung | ✅ |
| Filebrowser | - | Web-basierter Datei-Manager | ✅ |
| Forgejo | - | Self-hosted Git | ✅ |

**Key Features:**
- Exit-Node für mobile Clients konfiguriert
- Tailscale Subnet-Routing aktiv (LAN-Geräte erreichbar)
- Security: iptables-Regeln auf Tailscale-only-Zugriff beschränkt
- Nightly Backups zu Proxmox

---

### 🖥️ Proxmox VE Host (Tower) – Hypervisor & Storage

**System:** Proxmox VE, ZFS RAID-Pool

**LXC-Container:**

| Container | Service | Zweck |
|-----------|---------|-------|
| LXC 102 | Vaultwarden | Passwortmanager |
| LXC 103 | Caddy | Reverse Proxy + HTTPS-Termination |
| CT105 | Proxmox Mail Gateway | Mail-Filterung |

**Storage-Infrastruktur:**
- ZFS Pool mit automatischen Snapshots
- Tägliche inkrementelle Backups
- Point-in-Time Recovery möglich

**VMs:**
- Debian 12 (Testing)
- Windows 10/11 (Experimentierung)
- FreeBSD (Netzwerk-Experimente)
- Diverse Testsysteme

---

## 🎯 Design-Prinzipien

### 🔒 Security First

- ✅ **Verschlüsselung:** Ende-zu-Ende via Tailscale WireGuard
- ✅ **Netzwerk-Segmentierung:** Services per iptables isoliert
- ✅ **DNS-Sicherheit:** Pi-hole blockiert Malware-Domains
- ✅ **VPN-Only Access:** Externe Zugriffe ausschließlich über Tailscale
- ✅ **Monitoring:** Alle Aktivitäten geloggt & überwacht

### 🧪 Experimentierbarkeit

- ✅ Modulares Setup → schnelle Iteration
- ✅ Docker-Container → einfache Deployment-Updates
- ✅ ZFS Snapshots → Zero-Risk Rollbacks
- ✅ Sandbox-VMs auf Proxmox → sichere Experimente

### 📈 Skalierbarkeit

- ✅ Microservice-Architektur (Docker)
- ✅ Load-Balancing via Caddy
- ✅ Horizontale Erweiterung möglich (mehr Container/VMs)
- ✅ Resource-Limits pro Container

### 🔄 Ausfallsicherheit

- ✅ RAID-Storage (Redundanz)
- ✅ Automatische Backups (ZFS)
- ✅ Disaster-Recovery Plan
- ✅ Health-Checks auf kritischen Services
- ✅ Auto-Restart Policies

---

## 🚀 Deployment & Betrieb

### Docker-Deployment

```bash
# Service starten
docker-compose up -d service-name

# Logs anschauen
docker-compose logs -f service-name

# Service aktualisieren
docker-compose pull
docker-compose up -d --force-recreate
```

### Backup & Restore

```bash
# Tägliches ZFS-Snapshot (automatisch)
# Manuelles Backup
zfs snapshot tank/backup@manual-$(date +%Y%m%d)

# Restore von Snapshot
zfs rollback tank/backup@snapshot-name
```

### Wartungs-Fenster

| System | Wartung | Frequenz |
|--------|---------|----------|
| Debian Server | Updates & Patches | Wöchentlich (Sonntag 03:00 UTC) |
| Proxmox | Scrub & Maintenance | Monatlich (Samstag 02:00 UTC) |
| Network | Firmware-Updates | Nach Bedarf |

---

## 🔐 Sicherheitskonzept

### 4-Layer-Ansatz

**Layer 1: Netzwerk-Sicherheit**
- Firewall-Regeln (UFW auf Debian, FritzBox)
- VPN-Only Zugriff von außen
- Port-Whitelisting
- Tailscale ACLs

**Layer 2: Service-Sicherheit**
- Service-Isolation via Docker/Container
- Resource-Limits
- Security-Scanning (ClamAV)
- Regelmäßige Patches

**Layer 3: Daten-Sicherheit**
- Verschlüsselte Backups
- ZFS Checksums (Bit-Rot-Detection)
- RAID-Redundanz
- Snapshot-Replikation

**Layer 4: Zugriffs-Kontrolle**
- Tailscale Device Authorization
- Local SSH-Keys
- Audit-Logging
- Sudo-Password-Schutz

---

## 📚 Lessons Learned

Siehe auch: [LESSONS_LEARNED.md](LESSONS_LEARNED.md)

### Top 5 Erkenntnisse

1. **GitHub-Releases & Versionierung**
   - ❌ Nie Versionsnummern hartkodieren
   - ✅ Immer dynamisch per GitHub API ermitteln
   - ⚠️ Downloads mit Dateigröße validieren (404-Fehler erkennen)

2. **Tailscale Exit-Node**
   - ❌ Sinnlos für Geräte im gleichen Heimnetz
   - ✅ Nur für mobile/externe Clients relevant
   - 💡 IPv6-Präferenz beachten (`curl -4 ifconfig.me`)

3. **Port-Konflikte**
   - ✅ Immer zuerst `ss -tulpn` prüfen
   - ✅ Config-Debugging ist Zeitverschwendung, wenn Port besetzt ist

4. **Headscale Self-Hosting**
   - ⏸️ Nice-to-have, aber optional
   - ✅ Cloud-Koordination (tailscale.com) reicht aus
   - 💡 Datenverkehr läuft ohnehin P2P lokal

5. **LAN-Konnektivität zwischen Servern**
   - ⚠️ Hardware-Probleme → vorab testen mit `ping`
   - ✅ Fallback auf Tailscale-IPs immer haben
   - 📝 Netzwerk-Defekte dokumentieren

---

## ✅ Nächste Schritte

### 🔄 Kurzfristig (nächste 1-3 Monate)

- [ ] Headscale-Versuch wieder aufgreifen (Port-Konflikt lösen)
- [ ] Ansible Playbooks für Infrastruktur-Automation
- [ ] Disaster-Recovery Test durchführen
- [ ] IPv6-Support optimieren
- [ ] CI/CD Pipeline für Service-Deployments

### 🚀 Mittelfristig (3-6 Monate)

- [ ] Kubernetes Migration evaluieren
- [ ] Geo-redundante Backups (Cloud-Provider)
- [ ] Service-Mesh (Istio/Linkerd)
- [ ] Advanced Monitoring (Prometheus + Grafana erweitern)
- [ ] Zero-Trust Model vollständig implementieren

### 🌟 Langfristig (6+ Monate)

- [ ] Multi-Node Kubernetes Cluster
- [ ] GitOps Workflow (ArgoCD)
- [ ] Enterprise-Grade Compliance (GDPR)
- [ ] ML-basierte Anomalie-Detection

---

## 📚 Ressourcen

### 📖 Dokumentation

- [Tailscale Docs](https://tailscale.com/kb/)
- [Proxmox VE Documentation](https://pve.proxmox.com/wiki/)
- [Debian Documentation](https://www.debian.org/doc/)
- [Pi-hole Docs](https://docs.pi-hole.net/)
- [Grafana Documentation](https://grafana.com/docs/)

### 📄 Interne Docs

- [ARCHITECTURE.md](ARCHITECTURE.md) – Detaillierte Architektur
- [SERVICES.md](SERVICES.md) – Service-Übersicht & Konfiguration
- [LESSONS_LEARNED.md](LESSONS_LEARNED.md) – Lessons aus 7 Phasen
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) – Häufige Probleme

### 🔗 Links

- **Repository:** https://github.com/Hoinkkoma/homlap-
- **Netzwerk-Diagramme:** `homlao.drawio`
- **Lizenz:** Apache License 2.0

---

## 📝 Lizenz

Dieses Projekt ist unter der **Apache License 2.0** lizenziert. Siehe [LICENSE](LICENSE) für Details.

---

**Made with ❤️ for self-hosting, privacy & continuous learning**

Zuletzt aktualisiert: Juli 2026
