# 🖧 Netzwerk & Verbindungen

Netzwerk-Infrastruktur, Sicherheit und Zugriffsverwaltung.

---

## 🎯 Überblick

**Netzwerk-Architektur:**
- **LAN:** Fritzbox Router mit DHCP/DNS
- **VPN:** Tailscale für sichere Remote-Verbindungen
- **DNS:** Pi-hole für zentrale DNS-Verwaltung
- **DHCP:** Fritzbox + Pi-hole
- **Segmentierung:** VLANs (geplant)

---

## 🏠 LAN-Topologie

```
                    📶 Internet
                        |
                  [Router Fritzbox]
                        |
        +———————————————+———————————————+
        |               |               |
   [Debian Monitor]  [Proxmox]      [Clients]
   (eth0: [LOKAL])  (eth0: [LOKAL]) (WiFi/LAN)
```

**IP-Ranges:**
- Homelab: 192.168.X.0/24
- Geräte: 192.168.Y.0/24
- Reserviert: 192.168.Z.0/24

---

## 🚫 Pi-hole (DNS & Werbeblocker)

### Aufgaben
- Zentrale DNS-Verwaltung
- Werbeblocker (Block-Listen)
- Lokale DNS-Records
- DHCP-Server (optional)

### Konfiguration

**Admin-Panel:** `http://[LOKAL]:80/admin`

**Wichtige Einstellungen:**
- Upstream DNS: 1.1.1.1 (Cloudflare)
- Block-Listen: Standard (uBlock, Adguard, etc.)
- Local DNS Records: Homelab-Dienste

**Lokale DNS-Records:**
```
debian.local    → [LOKAL] (Debian Server)
proxmox.local   → [LOKAL] (Proxmox)
jellyfin.local  → [LOKAL]:8096
forgejo.local   → [LOKAL]:3030
grafana.local   → [LOKAL]:3002
```

### Whitelist/Blacklist

**Whitelist:**
- analytics.google.com (notwendig)
- other-necessary-domains.com

**Blacklist:**
- ads.example.com
- tracking.example.com

---

## 🔒 Tailscale (VPN & Zero-Trust)

### Zweck

Sicheres Mesh-VPN-Netzwerk für:
- Remote-Zugriff auf Homelab
- Inter-Server-Kommunikation
- Sichere Verbindung für Mobile Geräte
- Zero-Trust Networking

### Architektur

```
           [Tailscale Network]
                    |
    +———————————————+———————————————+
    |               |               |
[Debian Hub]    [Proxmox]       [Client]
(Relay/Exit)    (Node)          (Node)
```

### Konfiguration

**Debian Server (VPN Hub):**
```bash
# Installation
curl -fsSL https://tailscale.com/install.sh | sh

# Aktivieren
sudo tailscale up --advertise-routes=192.168.X.0/24 --exit-node

# Status
tailscale status
```

**Proxmox Server:**
```bash
sudo tailscale up --advertise-routes=192.168.Y.0/24
```

**Clients (Mobile/Laptop):**
- App installieren
- Mit Tailscale-Account anmelden
- Netzwerk verbinden

### Firewall-Regeln (geplant)

```
Inbound:
- Allow from Tailscale: All traffic

Outbound:
- Allow to Tailscale: All traffic
- Allow to LAN: 192.168.*.0/24
```

---

## 🔐 Sicherheit

### Allgemeine Maßnahmen

- [x] Firewall aktiv (Proxmox, Debian)
- [x] SSH-Keysonly (Passwort deaktiviert)
- [x] Fail2ban für Brute-Force Protection
- [x] Regelmäßige Sicherheits-Updates
- [ ] 2FA für kritische Services (geplant)
- [ ] Network Segmentation/VLANs (geplant)
- [ ] Intrusion Detection (geplant)

### SSH-Sicherheit

```bash
# SSH Keys einrichten
ssh-keygen -t ed25519

# Pubkey kopieren
ssh-copy-id user@[LOKAL]

# SSH Config (/etc/ssh/sshd_config)
PasswordAuthentication no
PermitRootLogin no
AllowUsers username
Port 22
```

### Zertifikate

- **LetsEncrypt:** Für öffentliche Dienste
- **Self-Signed:** Für interne Services
- **Renewal:** Automatisch (Certbot)

---

## 📊 Monitoring & Diagnose

### Network Tools

```bash
# Verbindungen testen
ping [LOKAL]
traceroute [LOKAL]
nslookup debian.local

# Port-Scanning
nmap -p- [LOKAL]

# Bandbreitennutzung
iftop -i eth0
nethogs

# DNS-Queries prüfen
dig @[LOKAL] debian.local
```

### Performance-Metriken

**Prometheus Queries:**
```promql
# Network Traffic
rate(node_network_transmit_bytes_total[5m])

# Connection Count
node_sockstat_TCP_inuse

# DNS Queries
rate(container_network_tcp_usage_total[5m])
```

---

## 🚀 Geplante Verbesserungen

- [ ] VLAN-Segmentierung (LAN/Gast/IoT)
- [ ] Advanced Firewall Rules
- [ ] Intrusion Detection (Suricata)
- [ ] Network Redundancy / Load Balancing
- [ ] Monitoring Dashboard für Netzwerk
- [ ] VPN-Exit Node für anonym Browsing
- [ ] Dynamic DNS (für externe Zugriffe)

---

## 🆘 Troubleshooting

### Kein Internet
```bash
# Gateway überprüfen
ip route show

# DNS testen
nslookup google.com

# Ping testen
ping 8.8.8.8
```

### Tailscale verbindet nicht
```bash
# Status prüfen
sudo systemctl status tailscaled

# Logs anschauen
sudo journalctl -u tailscaled -xe

# Reconnect
sudo tailscale logout
sudo tailscale up
```

### Hohe Latenz
```bash
# Ping-Zeiten prüfen
ping -c 10 [LOKAL]

# Throughput testen
iperf3 -c [LOKAL]

# Interface-Stats
ethtool -S eth0
```

---

## 📖 Weitere Ressourcen

- [Tailscale Documentation](https://tailscale.com/kb/)
- [Pi-hole Documentation](https://docs.pi-hole.net/)
- [Linux Networking](https://wiki.archlinux.org/title/Network_configuration)
- [Firewall Best Practices](https://wiki.debian.org/iptables)
- [DNS Best Practices](https://docs.pi-hole.net/guides/regex/overview/)

---

## 🔗 Siehe auch

- [Debian Monitoring](../Debian-Monitoring/README.md)
- [Sicherheitsrichtlinien](./security.md) (TBD)
- [Hauptübersicht](../README.md)
