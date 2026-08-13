# 🧩 Proxmox Server

Virtualisierungsplattform für alle Dienste und Anwendungen.

---

## 🎯 Überblick

Proxmox VE ist der zentrale Hosting-Server für:
- Virtuelle Maschinen (VMs)
- LXC Container
- Docker Container
- Storage Management (ZFS)
- Netzwerk-Management

**Webinterface:** `https://[LOKAL]:8006`

---

## 🖥️ VM-Templates

### Debian 12 (Base Template)
- OS: Debian 12 (Bookworm)
- Disk: 20 GB (LVM)
- RAM: 2 GB
- CPU: 2 Cores
- Hostname: debian-12-base

**Verwendung:**
- Klone für neue VMs
- Base für Cloud-Init
- Snapshots vor Major Updates

---

## 📦 LXC Container

### Debian 12 Container
- OS: Debian 12
- Disk: 10 GB
- RAM: 1 GB
- CPU: 1 Core
- Storage: Shared

**Verwendete Container:**
- System Utilities
- Monitoring Agents
- Backup Tools

---

## 💾 Storage Management

### ZFS Pool

**Konfiguration:**
- RAID Level: TBD
- Compression: LZ4
- Snapshots: Täglich (Automatisch)
- Backup: Inkrementell täglich

**Volumes:**
- VMs: 50%
- Container: 30%
- Backups: 20%

**Maintenance:**
```bash
# Pool Status
zpool status

# Snapshots auflisten
zfs list -t snapshot

# Scrub durchführen (wöchentlich)
zpool scrub pool-name
```

---

## 🐳 Docker Services

### Über Portainer verwaltet

**Portainer URL:** `http://[LOKAL]:9000`

**Gehostete Dienste:**

| Dienst | Image | Port | RAM | CPU |
|--------|-------|------|-----|-----|
| Jellyfin | jellyfin/jellyfin | 8096 | 2GB | 2 |
| Forgejo | forgejo/forgejo | 3030 | 512MB | 1 |
| FileBrowser | filebrowser/filebrowser | 8082 | 256MB | 1 |
| Vaultwarden | vaultwarden/server | 8000 | 512MB | 1 |
| Immich | ghcr.io/immich-app/immich | 2283 | 1GB | 2 |
| Netdata | netdata/netdata | 19999 | 512MB | 1 |
| IT-Tools | corentinth/it-tools | 80 | 256MB | 1 |
| ClamAV | clamav/clamav | 3310 | 1GB | 2 |

---

## 🔄 Backup-Strategie

### Automatisierte Backups

**Proxmox Backup Server:**
- VMs: Täglich (Full + Incremental)
- Container: Täglich (Full)
- Retention: 14 Tage
- Speicher: External USB / NAS

**Prozedur:**
```bash
# Backup starten
proxmox-backup-client backup backup.pxar /etc /root

# Status prüfen
proxmox-backup-client task log <TASK_ID>

# Restore testen (monatlich)
proxmox-backup-client restore-command
```

### Snapshots

**Policy:**
- Vor jedem Update
- Täglich automatisch
- Aufbewahrung: 7 Tage
- Automatische Bereinigung

```bash
# Snapshot erstellen
pvesh create /nodes/proxmox/qemu/100/status/create -data '{"snapname":"backup-2026-08-13"}'

# Snapshot auflisten
pvesh get /nodes/proxmox/qemu/100/snapshot

# Restore aus Snapshot
pvesh create /nodes/proxmox/qemu/100/status/rollback -data '{"snapname":"backup-2026-08-13"}'
```

---

## 🔐 Netzwerk-Konfiguration

### Interfaces

| Interface | Typ | Aufgabe | IP |
|-----------|-----|--------|-----|
| eth0 | Physical | LAN-Zugang | [LOKAL] |
| vmbr0 | Bridge | VM-Netzwerk | 192.168.X.X/24 |
| vmbr1 | Bridge | Container | 192.168.Y.Y/24 |

### Firewall

- **Inbound:** Nur notwendige Ports
- **Outbound:** Unrestricted (LAN)
- **Default:** DROP für unbekannte

---

## ⚙️ Wartung & Monitoring

### Tägliche Checks
- [ ] Alle VMs/Container laufen
- [ ] CPU-Auslastung < 80%
- [ ] RAM-Nutzung < 85%
- [ ] Disk-Space > 20%
- [ ] Storage-Health OK

### Wöchentliche Checks
- [ ] ZFS Scrub durchführen
- [ ] Snapshots überprüfen
- [ ] Backup-Status prüfen
- [ ] Netzwerk-Performance
- [ ] Updates verfügbar?

### Monatliche Checks
- [ ] Sicherheits-Updates einspielen
- [ ] Performance-Optimierung
- [ ] Dokumentation aktualisieren
- [ ] Disaster Recovery Test

---

## 🆘 Troubleshooting

### VM startet nicht
```bash
# Logs prüfen
journalctl -u pve-manager -xe

# VM Status
qm status <VMID>

# VM Console
qm terminal <VMID>
```

### Container funktioniert nicht
```bash
# Container Status
pct status <CTID>

# Logs anschauen
pct logs <CTID>

# Shell öffnen
pct enter <CTID>
```

### Storage Probleme
```bash
# Pool Status
zpool status

# Disk Errors
dmesg | grep -i error

# iSCSI Status (falls vorhanden)
systemctl status open-iscsi
```

---

## 📖 Weitere Ressourcen

- [Proxmox VE Docs](https://pve.proxmox.com/pve-docs/)
- [Proxmox Wiki](https://wiki.proxmox.com/)
- [ZFS Guide](https://wiki.proxmox.com/wiki/ZFS_on_Linux)
- [Backup Strategy](https://pve.proxmox.com/wiki/Proxmox_Backup_Server)
- [Firewall Rules](https://pve.proxmox.com/wiki/Firewall)

---

## 🔗 Siehe auch

- [Docker-Dokumentation](../Docker/README.md)
- [Backup & Recovery](./backup-strategy.md)
- [Netzwerk-Konfiguration](../Netzwerk/README.md)
