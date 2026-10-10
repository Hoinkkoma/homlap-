# Netzwerk-Diagramm & Zonen (Legende)

Diese Datei beschreibt die logische Netzwerk-Segmentierung, empfohlene Firewall-Regeln, Hostnamen und Service-Discovery-Hinweise für das Homelab.

---

## Ziel

Saubere Trennung von:

- Management/Administration
- Services/Produktivbereich
- Print-/Druckbereich
- Gast-/IoT-Bereichen
- VPN-/Remote-Zugriff

---

## Zonen

### Management-Zone

- Zweck: Verwaltungssysteme, Proxmox, Monitoring, Backups
- Beispiel-Hostnamen: `monitoring`, `proxmox`, `backup-host`

### Services-Zone

- Zweck: produktive VMs und Container
- Beispiel-Hostnamen: `vm-jellyfin`, `vm-forgejo`, `filebrowser`, `vaultwarden`

### Print-Zone

- Zweck: Druckinfrastruktur und Printserver
- Beispiel-Hostnamen: `print-vm`, `digitus-print`

### Guest-/IoT-Zone

- Zweck: untrusted Geräte, Gäste-WLAN, Smart-Home
- Zugriff auf interne Services ohne Freigabe vermeiden

### VPN-Zone

- Zweck: Remote-Zugriffe über Tailscale oder vergleichbare sichere Wege

---

## Firewall-Prinzipien

- Default Deny für unzuverlässige Richtungen
- nur notwendige Ports zwischen Zonen erlauben
- Management-Zugriff auf Services restriktiv
- Print-Zone isoliert halten
- VPN-Zugriffe granular steuern

---

## Ports & Dienste (Referenz)

- SSH: TCP 22
- CUPS / IPP: TCP 631
- JetDirect / Raw: TCP 9100
- LPD: TCP 515
- mDNS / Avahi: UDP 5353
- Proxmox WebUI: HTTPS / konfigurierbarer Port
- Monitoring-Endpunkte: je nach Dienst

---

## Hostnamen und Naming

Empfohlene Konvention:

- `monitoring` – Debian Monitoring Server
- `proxmox` – Proxmox Host
- `print-vm` – CUPS-/Print-VM
- `digitus-print` – Digitus Printserver
- `vm-jellyfin`, `vm-forgejo`, `vaultwarden`, `filebrowser` – Service-VMs

---

## Service-Discovery

- mDNS/Avahi nur im vertrauenswürdigen LAN aktivieren
- lokale DNS-Einträge für stabile Hostnamen bevorzugen
- DHCP-Reservierungen für Druck- und Basis-Services nutzen

---

## Sicherheits- und Betriebsregeln

- Backups der CUPS- und Proxmox-Konfiguration regelmäßig machen
- Logs zentral für CUPS, Proxmox und Print-Dienste sammeln
- SSH-Key-Authentifizierung verwenden
- keine Services ins öffentliche Internet ohne VPN oder Authentifizierung

---

## Beispiel-Workflows

- Homepage → Druckauftrag: App schreibt Datei oder ruft Print-VM an → CUPS verarbeitet → Printserver liefert Drucker
- Management → Services: nur gezielte, dokumentierte Verbindungen zulassen

---

## Hinweis

Diese Datei ist eine logische Referenz. Konkrete IP-Adressen und produktive Topologien werden lokal separat und nur mit Freigabe verwaltet.
