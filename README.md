# 🏠 Hoinkkoma Homelab — Übersicht (locker & kurz)

**Letzte Aktualisierung:** 2026-08-13

Hey — willkommen zur Homelab-Doku. Hier steht kurz und klar, was läuft, wie die Sachen verbunden sind und wie du das Druck-Setup (HP OfficeJet 4558 über Digitus) betreibst.

Kurzüberblick

- Monitoring & Management: Debian‑Server (Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny)
- Virtualisierung: Proxmox Host (VMs, LXC, Docker)
- Druck: Print‑Gateway (Debian VM mit CUPS/HPLIP) → Digitus Printserver → HP OfficeJet 4558

Schnellstart (wenn du nur drucken willst)

1. Die Homepage‑App schickt PDFs an das Print‑Gateway (print‑vm) via IPP oder schreibt sie in ein Verzeichnis, das vom Print‑Watcher überwacht wird.
2. print‑vm (CUPS) übernimmt den Job und leitet ihn an den Digitus Printserver weiter (oder verwaltet den Drucker direkt, wenn USB‑durchgereicht).
3. Digitus liefert die Daten per USB an den HP OfficeJet 4558 — fertig.

Wichtige Links in diesem Repo

- Architektur (visuell): assets/architecture.svg
- Proxmox: Proxmox/printer-vm.md — Setup & Tipps für die Debian‑Print‑VM
- Netzwerk: Netzwerk/printserver-digitus.md — Digitus‑Konfiguration & Tests
- Netzwerk: Netzwerk/network-diagram.md — Zonen, Firewall‑Legende & Hardening
- Automation: ansible/playbook.yml — Playbook zur Einrichtung der Print‑VM
- Skripte: Proxmox/print-worker.sh, Proxmox/print-worker-watcher.sh, Proxmox/print-worker.service

Was ich dir empfehle

- Verwende Hostnamen / DHCP‑Reservierungen (keine harten IPs in der Doku)
- Halte CUPS nur im LAN oder erreichbar via VPN (Tailscale)
- Teste mit `lp` und `lpstat` bevor du die Homepage produktiv anschließt

Wenn du willst, mache ich dir ein kurzes Ansible‑Playbook (fertig im Repo) und ein Beispiel, wie du die Homepage‑App mit dem Print‑Gateway verbindest. Oder ich passe die Sprache noch lockerer an — sag Bescheid.
