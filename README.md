# 🏠 Hoinkkoma Homelab — Übersicht

**Letzte Aktualisierung:** 2026-08-13

Zweck

Kurzüberblick über die eingesetzte Infrastruktur, deren Komponenten und die Druck‑Pipeline. Reine Referenz‑/Übersichtsseite, keine Anleitungsschritte im Detail.

Architektur (hoch‑level)

- Virtualisierung: Proxmox VE (Host für VMs, LXC, Docker, Portainer)
- Monitoring / Management: Debian‑Server (Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny)
- Druckinfrastruktur: print‑vm (Debian/CUPS) → Digitus Printserver → HP OfficeJet 4558

Wichtige Komponenten

- Proxmox Host
  - Rollen: VM/Container‑Hosting, Netzwerk‑Bridging, USB/Port‑Anbindung
  - Relevante Dateien: Proxmox/README.md, Proxmox/printer-vm.md, Proxmox/print-worker.service

- Debian Monitoring Server
  - Rollen: Metriken (Prometheus), Dashboards (Grafana), Logs (Loki), Service‑Checks (Uptime Kuma)
  - Relevante Dateien: Debian-Monitoring/README.md

- Print‑Gateway (print‑vm)
  - Rollen: CUPS‑Server zur Annahme und Weiterleitung von Druckjobs (IPP/LPD/JetDirect)
  - Integration: nimmt Jobs von der Homepage‑App entgegen oder verarbeitet Drop‑Verzeichnisse
  - Relevante Dateien: Proxmox/printer-vm.md, Proxmox/print-worker.sh

- Digitus Printserver
  - Rolle: Netztwendiger USB‑Printserver; stellt den HP OfficeJet im LAN bereit (Socket/LPD/IPP)
  - Relevante Dateien: Netzwerk/printserver-digitus.md

- Anwendungen (Auswahl)
  - Jellyfin — Medienserver
  - Forgejo — Git‑Hosting
  - FileBrowser — Web‑Dateimanager
  - Vaultwarden — Passwortmanager
  - Immich — Foto‑Management
  - Netdata — Performance‑Monitoring
  - ClamAV — Antiviren‑Scanner
  - Weitere Dienste: siehe Docker/ und Proxmox/ Verzeichnisse

Schnellübersicht: Druck‑Workflow (hoch‑level)

1. Homepage‑App erzeugt PDF und übergibt per IPP oder Drop‑Verzeichnis an print‑vm
2. print‑vm (CUPS) empfängt Job und leitet ihn an Digitus (socket://<DIGITUS_IP>:9100) oder nutzt lokalen USB‑Treiber
3. Digitus liefert Druckdaten per USB an HP OfficeJet 4558

Netzwerk‑Hinweise (kurz)

- Empfohlene Segmentierung: Management, Services, Print, Guest/IoT
- Wichtige Ports: IPP 631/TCP, JetDirect 9100/TCP, LPD 515/TCP, mDNS/Avahi 5353/UDP, SSH 22/TCP
- Print‑Netzwerk: Digitus sollte eine feste IP oder DHCP‑Reservierung erhalten; wenn Digitus an Proxmox‑Host hängt, muss der entsprechende Host‑NIC in eine Bridge (z. B. vmbr1) eingebunden oder geroutet sein

Logs & Wartung (Kurz)

- CUPS‑Logs: /var/log/cups/error_log
- Prometheus/Grafana/Loki: siehe Debian‑Monitoring/README.md
- Tägliche Checks: Dienste up, keine kritischen Alerts, ausreichende Disk‑Kapazität

Verweise

- Architektur (grafisch): assets/architecture.svg
- Proxmox: Proxmox/README.md, Proxmox/printer-vm.md
- Debian Monitoring: Debian-Monitoring/README.md
- Netzwerk: Netzwerk/printserver-digitus.md, Netzwerk/network-diagram.md
- Anwendungen: docs/apps.md

Lizenz

- LICENSE (Repository‑Root)
