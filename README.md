# 🏠 Hoinkkoma Homelab — Übersicht (locker & kurz)

**Letzte Aktualisierung:** 2026-08-13

moin — willkommen zur Homelab-Doku. Hier steht kurz und klar, was läuft, wie die Sachen verbunden sind und wie du das Druck-Setup (HP OfficeJet 4558 über Digitus) betreibst.

Kurzüberblick

- Monitoring & Management: Debian‑Server (Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny)
- Virtualisierung: Proxmox Host (VMs, LXC, Docker)
- Druck: Print‑Gateway (Debian VM mit CUPS/HPLIP) → Digitus Printserver (direkt an Proxmox Host) → HP OfficeJet 4558

Schnellstart (wenn du nur drucken willst)

1. Die Homepage‑App schickt PDFs an das Print‑Gateway (print‑vm) via IPP oder schreibt sie in ein Verzeichnis, das vom Print‑Watcher überwacht wird.
2. print‑vm (CUPS) übernimmt den Job und leitet ihn an den Digitus Printserver weiter (oder verwaltet den Drucker direkt, wenn USB‑durchgereicht).
3. Digitus liefert die Daten per USB an den HP OfficeJet 4558 — fertig.

Hinweis zur Verkabelung (aktualisiert)

- Der Digitus Printserver ist nicht über einen separaten Switch mit dem Netzwerk verbunden — er sitzt direkt an einem zweiten LAN‑Port des Proxmox Hosts. Der Host führt das Traffic‑Routing[...]

Wichtige Links in diesem Repo

- Architektur (visuell): assets/architecture.svg
- Proxmox: Proxmox/printer-vm.md — Setup & Tipps für die Debian‑Print‑VM (inkl. Hinweise zum Bridge/Second‑NIC)
- Netzwerk: Netzwerk/printserver-digitus.md — Digitus‑Konfiguration & Tests (Direktverbindung an Proxmox Host) 
- Netzwerk: Netzwerk/network-diagram.md — Zonen, Firewall‑Legende & Hardening (aktualisiert)
- Skripte: Proxmox/print-worker.sh, Proxmox/print-worker-watcher.sh, Proxmox/print-worker.service

Was du als Admin wissen solltest

- Wenn der Digitus an einen zweiten NIC am Proxmox Host angeschlossen ist, muss dieser NIC in eine Bridge (z. B. vmbr1) eingebunden werden oder geroutet sein, damit die print‑vm den Digitus erre[...]
- Alternativ kann der Proxmox Host IP‑Forwarding übernehmen und lokale Firewall‑Regeln setzen; in der Doku findest du Beispiele und Empfehlungen.

Wenn du willst, passe ich die kurzen Konfig‑Snippets in Proxmox/printer-vm.md an (Beispiel: wie man vmbr1 anlegt und eine VM an diese Bridge hängt). Sag kurz „Snippets anpassen“, dann über...