# Debian — Kategorie Übersicht

moin — Diese Unterseite fasst alles zusammen, was mit Debian‑Servern im Homelab zu tun hat (Monitoring & Dienste).

Kurz:
- Debian‑Monitoring ist zentrale Überwachungsinstanz (Prometheus, Grafana, Loki, Uptime Kuma, Scrutiny).
- Einige Dienste (z. B. Homepage) laufen als Debian‑VMs/Containern und bieten Endpunkte, die gedruckt werden können.

Wichtige Dateien (Unterkategorien)
- Debian-Monitoring/README.md — Monitoring Server Doku
- Debian/homepage.md — kurze Beschreibung der Homepage‑App (neu)
- Netzwerk/printserver-digitus.md — Digitus Printserver (als Unterkategorie / Integration)

Kurzbeschreibungen
- Homepage (App): Empfängt Uploads/PDFs oder sendet IPP‑Jobs an print‑vm; kann lokal als Service auf Debian laufen.
- Monitoring: Prometheus (Metriken), Grafana (Dashboards), Loki (Logs), Uptime Kuma (Service Checks), Scrutiny (SMART)

Schaubild (Mermaid)

```mermaid
%% assets/network-zones.mmd
flowchart TB
  Router --> Management[Debian Monitoring]
  Management --> Prometheus[Prometheus]\n  Management --> Grafana[Grafana]\n  Prometheus -->|scrape| VMs[VMs/Services]
  VMs --> Homepage[Homepage App]
  Homepage --> PrintVM[print-vm (CUPS) on Proxmox]
  PrintVM --> Digitus[Digitus Printserver]
```
