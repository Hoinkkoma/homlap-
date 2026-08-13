# Netzwerk-Diagramm & Zonen (Legende)

Diese Datei beschreibt die logische Netzwerk‑Segmentierung (Zonen/VLANs), empfohlene Firewall‑Regeln, Hostname‑Konventionen und Service‑Discovery‑Hinweise für das Homelab. Es werden absichtlich keine IP‑Adressen eingetragen — verwende Hostnamen or DHCP‑Reservierungen statt harter IPs.

## Übersicht

Ziel: Saubere Trennung von Management, Services, Druck‑Infrastruktur und Gastnetz, sowie klare Zugriffsregeln für Dienste (z. B. CUPS, Proxmox WebUI).

Zonen (Bezeichner)

- Management‑Zone
  - Zweck: Verwaltungssysteme (Debian Monitoring Server, Proxmox WebUI, Backup‑Services)
  - Beispiel-Hostnamen: monitoring, proxmox

- Services‑Zone
  - Zweck: Produktions‑VMs/Sw Dienste (Jellyfin, Forgejo, FileBrowser, Vaultwarden)
  - Beispiel-Hostnamen: vm-jellyfin, vm-forgejo

- Print‑Zone
  - Zweck: Druckinfrastruktur (print‑vm, Digitus Printserver, physische Drucker)
  - Beispiel-Hostnamen: print-vm, digitus-print

- Guest/IoT‑Zone
  - Zweck: untrusted Geräte (IoT, Gäste‑WiFi). Eingeschränkter Zugriff auf interne Services.
  - Beispiel-Hostnamen: none (dynamisch)

- VPN‑Zone
  - Zweck: Remote‑Zugriffe über Tailscale oder andere VPNs. Erlaubte Zugriffe werden granular gesteuert.

## VLAN‑/Switch‑Empfehlung

- Lege pro Zone ein VLAN an (z. B. VLAN IDs intern verwalten). Routing zwischen VLANs über Router/Firewall mit ACLs.
- Management‑Zone: nur Admin‑Switch‑Ports und Management‑Hosts zugelassen.
- Print‑Zone: physische Ports für Drucker & Printserver; Print‑VM in Services‑ oder separater Print‑Zone (je nach Sicherheitswunsch).

## Firewall‑Zonen & Regeln (konzeptionell)

Grundprinzip: Deny by default — nur notwendige Ports zwischen Zonen erlauben.

1. Management → Services
   - Erlaube spezifische Management‑Protokolle (SSH, Proxmox WebUI, Backup‑Ports)
   - Nur vom Management‑Netz auf Services initiieren

2. Services → Management
   - Meist nur Monitoring‑Zugriff (Prometheus scrape endpoints, syslog/forwarding)

3. Print‑Zone ↔ Print‑VM
   - Erlaube IPP (TCP 631) und/oder LPD/JetDirect (TCP 515 / 9100) zwischen Print‑VM und Print‑Zone
   - Restriktiere Zugriff auf die Print‑VM nur aus Management‑Zone und Services‑Zone (falls gesetzt)

4. Guest/IoT → Intern
   - Standard: Blockieren. Falls nötig, nur Zugriff auf Internet/Gateway erlauben.

5. VPN‑Zone → Intern
   - Erlaube Zugriff auf Monitoring und Print‑VM über VPN (z. B. IPP auf print‑vm), sofern Authentifikation stattfindet

## Ports & Dienste (nur Referenz; benutze Hostnames statt IPs)

- CUPS (IPP): TCP 631
- JetDirect/Socket: TCP 9100
- LPD: TCP 515
- mDNS / Avahi: UDP 5353
- SSH: TCP 22
- Proxmox WebUI: HTTPS (custom port) — per Host konfigurieren
- Prometheus scrape endpoints: HTTP / Metrics (custom per service)

## Hostname‑Konventionen

- Verwende kurze, aussagekräftige Hostnamen (kleinbuchstaben, trenne mit `-`):
  - monitoring (Debian Monitoring Server)
  - proxmox (Proxmox Host)
  - print-vm (Debian Print‑VM)
  - digitus-print (Digitus Printserver)
  - vm-jellyfin, vm-forgejo (andere VMs)

- Trage Hostnamen in der lokalen DNS (Pi‑hole / DNS‑Server) oder DHCP‑Reservierungen ein, damit Services stabil unter Hostnamen erreichbar sind.

## Service‑Discovery

- mDNS/Avahi: Nützlich für lokale Discovery (z. B. AirPrint, CUPS automatische Erkennung). Aktiviere nur im vertrauenswürdigen LAN‑Segment.
- DNS: Verwende lokale DNS‑Einträge (Pi‑hole) oder DHCP‑Reservierungen für stabile Auflösung.
- Empfehlung: Service‑Discovery (mDNS) nur im Print‑ oder Management‑Zone aktivieren; nicht im Guest/IoT‑Zone.

## Print‑Spezifische Hinweise

- Digitus Printserver
  - Vergib einen festen Hostnamen (z. B. digitus-print) via DHCP‑Reservierung oder statische Konfiguration.
  - Bevorzuge Raw/JetDirect (socket://<DIGITUS_HOSTNAME>:9100) für Basisdruck; LPD/IPP optional.
  - Erlaube Zugriff auf den Digitus nur aus Print‑VM und Management‑Zone (bzw. VPN), nicht aus Guest/IoT.

- Print‑VM (print-vm)
  - Betreibe CUPS mit Freigabe nur für notwendige Zonen; setze cupsd.conf Zugriffsregeln (Allow from <zone‑networks>).
  - Aktiviere mDNS/Avahi optional zur Erleichterung der Discovery für lokale Clients.
  - Verwende Dateiausgabe‑Pfad (z. B. /var/spool/homepage_prints) und einen Watcher‑Service, um Apps zu integrieren.

## Hardening‑Empfehlungen

- Backups: sichere regelmäßige Backups der CUPS‑Konfiguration, Proxmox VM‑Konfigurationen und wichtige Daten (im Backup‑Verzeichnis).
- Logs: Sammle und überwache Logs zentral (Loki / Grafana) für CUPS/Print‑VM/Proxmox.
- Zugang: Verwende SSH‑Keys, disable root‑passwort SSH, MFA wo möglich.
- Updates: Regelmäßige Sicherheits‑Updates (apt upgrade) mit Wartungsfenstern.
- Netzwerk: Minimale erforderliche Firewall‑Regeln; keine Services ins öffentliche Netz ohne VPN.

## Monitoring & Alerts (Print‑Services)

- Überwache Drucker‑Fehler (CUPS error_log), Drucker‑Status via SNMP (wenn verfügbar) oder Log‑Parsing.
- Erstelle Alerts (Grafana/Uptime) bei Druckfehler‑Rate über Threshold oder CUPS‑Service‑Down.

## Beispiel‑Workflows (Kurz)

- Homepage → Druckauftrag:
  - App schreibt PDF in shared Verzeichnis oder ruft IPP auf print-vm → print‑worker druckt via lp → CUPS sendet an Digitus (socket://digitus‑print:9100) → Digitus liefert an Drucker per USB.

- Verwaltung:
  - Admin in Management‑Zone nutzt Proxmox WebUI und Monitoring; kann Print‑VM per SSH erreichen.

---

Wenn du möchtest, kann ich diese Datei noch mit Diagramm‑Skizzen erweitern (ASCII oder Netplan‑ähnliche Visualisierung) oder einen Ansible‑Playbook‑Entwurf hinzufügen, der die Print‑VM‑Konfiguration automatisiert (ohne IPs, mit Hostname‑Variablen).