# 🌐 Netzwerk-Topologie

Detaillierte Übersicht der logischen Netzwerk-Architektur, Zonen und Datenflüsse. Diese Datei ist bewusst generisch gehalten, damit sie keine internen IP-Adressen oder produktiven Geräte-Details offenlegt.

---

## Überblick

```mermaid
graph TB
    subgraph WAN[Heimnetzwerk / WAN]
        ROUTER[Router / Modem]
    end

    subgraph INFRA[Infrastruktur]
        PROXMOX[Proxmox Host]
        DEBIAN[Debian Server]
    end

    subgraph VLAN_MAIN[Management / Services]
        MONITORING[Monitoring]
        APPS[Apps / Container]
    end

    subgraph VLAN_PRINT[Print]
        PRINTVM[Print-VM]
        DIGITUS[Digitus Printserver]
        PRINTER[HP OfficeJet]
    end

    ROUTER --> PROXMOX
    ROUTER --> DEBIAN
    PROXMOX --> VLAN_MAIN
    PROXMOX --> VLAN_PRINT
    DEBIAN --> MONITORING
    MONITORING --> APPS
    PRINTVM --> DIGITUS
    DIGITUS --> PRINTER
```

---

## Netzwerk-Segmentierung

### Management & Monitoring

- Zweck: Hosts, Monitoring, Administration, Backups
- Regelfocus: Zugriffe streng kontrollieren

### Services

- Zweck: produktive Container, Webdienste, Media-Services
- Regelfocus: nur notwendige Verbindungen zulassen

### Print

- Zweck: CUPS, Digitus, Drucker
- Regelfocus: isoliert halten und nur gezielt zugänglich machen

### Guest / IoT

- Zweck: untrusted Geräte
- Regelfocus: streng begrenzter Zugriff

---

## Datenflüsse

### Monitoring-Flow

```mermaid
graph LR
    A[Proxmox Host] -->|Exporter| B[Prometheus]
    C[Services / VMs] -->|Metrics| B
    B --> E[Grafana]
    B --> F[VictoriaMetrics / Loki]
```

### Print-Flow

```mermaid
graph LR
    A[Homepage / App] -->|PDF / Job| B[Print-VM]
    B --> C[CUPS]
    C --> D[Digitus Printserver]
    D --> E[HP OfficeJet]
```

---

## Bridge- und Firewall-Empfehlungen

- Proxmox-Bridges nur mit klarer Zielsetzung konfigurieren
- Routing nur dort, wo es wirklich benötigt wird
- Firewall-Regeln nach Zonen und Kommunikationsmustern festlegen
- keine vermeintlich „sicheren” Default-Regeln ohne Prüfung übernehmen

---

## Wichtige Hinweise

- Die tatsächliche Topologie kann je nach Hausnetzwerk und eingebauter Hardware variieren.
- Routing, VLANs und Dienstzugriffe müssen lokal validiert werden.
- Produktive Netzwerke niemals ohne Dokumentation und Risikoanalyse ändern.
