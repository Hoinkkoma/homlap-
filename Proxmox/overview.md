# Proxmox — Kategorie Übersicht

moin — Diese Unterseite fasst alles zusammen, was mit dem Proxmox Host zu tun hat.

Kurz:
- Proxmox ist der zentrale Host für VMs, LXC und Docker.
- Druck‑VMs (print-vm) laufen auf Proxmox; einige Hardware‑Ports (Digitus) sind physisch am Host angeschlossen.

Wichtige Dateien (Unterkategorien)
- Proxmox/README.md — ausführliche Proxmox‑Doku (bestehend)
- Proxmox/printer-vm.md — Einrichtung der Debian Print‑VM (CUPS)
- Proxmox/print-worker.service — Systemd Service für Print‑Worker
- Proxmox/hp-officejet-4658.md — Drucker‑spezifische Hinweise

Kurze Beschreibung der Proxmox‑bezogenen Anwendungen
- print-vm (CUPS): Print‑Gateway VM, nimmt Druckaufträge entgegen und leitet an Digitus weiter.
- print-worker (Script + Service): Kleine Wrapper/Watcher, der PDFs aus einem Drop‑Folder an lp übergibt.

Schaubild (Mermaid)

```mermaid
%% assets/proxmox-zone.mmd
flowchart LR
  Proxmox[Proxmox Host]\n  Proxmox --> VM_PRINT[print-vm (Debian/CUPS)]
  Proxmox --> VM_MON[Debian Monitoring]
  Proxmox --> Docker[Docker / Portainer (Apps)]
  VM_PRINT --> Digitus[switch / Digitus on Host]
  Digitus --> HP[HP OfficeJet 4558]
```
