# 🧩 Proxmox Server

Virtualisierungsplattform für VM-, Container- und Host-Services im Homelab.

---

## Überblick

Proxmox VE ist der zentrale Hosting-Server für:

- Virtuelle Maschinen (VMs)
- LXC Container
- Docker und Swarm-Services
- Storage-Management mit ZFS
- Netzwerk-Management und Virtualisierung

---

## Hauptaufgaben

- Bereitstellung und Verwaltung von VM-/LXC-Umgebungen
- zentrale Container- und Service-Plattform
- GPU-Workloads, sofern Treiber und Hardware unterstützt werden
- Backup- und Snapshot-Management
- Isolation und Segmentierung verschiedener Betriebsbereiche

---

## Wichtige Betriebselemente

### VM-Templates

- Debian 12 oder vergleichbare Linux-Basis-Images
- separierte, wiederverwendbare Templates für neue VMs
- Snapshots vor größeren Änderungen und Updates

### LXC Container

- kleinere, leichtgewichtige Container für einzelne Dienste
- gute Wahl für leichtgewichtige Helper- oder Systemdienste

### ZFS Storage

- Poolstatus, Snapshots und Backups regelmäßig prüfen
- interne Systemdatenträger schützen
- keine unautorisierten Änderungen an ZFS-Pools und Systemdisks

---

## Docker & Swarm

Proxmox dient als virtueller Host für wenige bis mehrere Container-Umgebungen. Docker Swarm kann auf mehreren Nodes sowie auf separaten Debian-Hosts eingesetzt werden. Die Last- und Dienstverteilung muss durch Constraints, Labels und Ressourcenplanung gesteuert werden.

---

## Netzwerk-Konfiguration

Netzwerk- und Bridge-Konfigurationen müssen vor Änderungen analysiert und dokumentiert werden. Typische Bereiche:

- Management-Netz / Administration
- Service-Netz / interne Container- und VM-Zone
- Print-Zone / Drucker- und Printserver-Umgebung
- Tailscale- und VPN-Verkehr

---

## Wartung & Monitoring

### Tägliche Checks

- [ ] Alle VMs und Container laufen
- [ ] CPU-, RAM- und Storage-Auslastung prüfen
- [ ] ZFS-Status und Snapshots kontrollieren
- [ ] Netzwerk- und Service-Health prüfen

### Wöchentliche Checks

- [ ] ZFS Scrub und Snapshot-Status prüfen
- [ ] Backups und Wiederherstellung testen
- [ ] Updates und Sicherheits-Patches prüfen

---

## Troubleshooting

### VM startet nicht

```bash
qm status <VMID>
qm terminal <VMID>
pve-manager logs
```

### Container funktioniert nicht

```bash
pct status <CTID>
pct logs <CTID>
pct enter <CTID>
```

### Storage-Problem

```bash
zpool status
zfs list
journalctl -k
```

---

## Sicherheit

- SSH nur mit Schlüsseln statt Passwörtern
- Firewall-Regeln mit Absicht und nur nach Prüfung anwenden
- keine produktiven Änderungen an kritischen Netzen ohne Risikoanalyse
- keine sensiblen Informationen in Dokumentation oder Commits übernehmen

---

## Weitere Ressourcen

- [Proxmox VE Docs](https://pve.proxmox.com/pve-docs/)
- [ZFS Guide](https://wiki.proxmox.com/wiki/ZFS_on_Linux)
- [Docker Docs](../Docker/README.md)
- [Netzwerk-Konfiguration](../Netzwerk/README.md)
