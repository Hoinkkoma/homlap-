# Kategorien & Diagramme

Diese Seite fasst die Hauptkategorien im Homelab zusammen und verlinkt zu Detail‑Seiten sowie Schaubildern.

Kategorien

- Proxmox Server (eigene Unterkategorie)
  - Enthält: Proxmox/README.md (bestehende Doku), Proxmox/printer-vm.md, Proxmox/print-worker.service, Proxmox/hp-officejet-4658.md
  - Schaubild: assets/proxmox-zone.mmd

- Debian Server (eigene Unterkategorie)
  - Enthält: Debian‑Monitoring (Debian-Monitoring/README.md), Dienste wie Homepage (Debian/homepage.md) und weitere Dienste
  - Digitus Printserver: Netzwerk/printserver-digitus.md (als Unterkategorie von Debian)
  - Schaubild: assets/network-zones.mmd

- Anwendungen
  - Kurze Beschreibungen aller gehosteten Anwendungen: siehe docs/apps.md


Hinweis: Ich habe auf "moin" als Begrüßung geachtet.

## Diagrameinbettung (Mermaid Quellen)

Proxmox Übersicht:

```mermaid
%% from assets/proxmox-zone.mmd
flowchart LR
  Proxmox[Proxmox Host]\n  Proxmox --> VMs[VMs (print-vm, monitoring, apps)]
  Proxmox --> DigitusHost[2nd LAN Port -> Digitus]
  VMs -->|CUPS| PrintVM[print-vm (CUPS)]
  PrintVM -->|socket:9100| DigitusHost
  DigitusHost -->|USB| HP[HP OfficeJet 4558]
```


Netzwerk / Debian Übersicht:

```mermaid
%% from assets/network-zones.mmd
flowchart TB
  Router[Router / Edge] --> Management[Management Zone\n(Debian Monitoring)]
  Router --> Services[Services Zone\n(Apps)]
  Router --> Print[Print Zone]
  Management --> Proxmox[Proxmox Host]
  Services --> PrintVM[print-vm (auf Proxmox)]
  Print --> Digitus[Digitus Printserver]
  Digitus --> HP[HP OfficeJet 4558]
```
