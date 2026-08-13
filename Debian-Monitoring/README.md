# 📊 Debian Monitoring Server

Zentrale Monitoring- und Management-Plattform des Homelabs.

---

## 🎯 Überblick

Der Debian Monitoring Server fungiert als:
- **Monitoring-Zentrale:** Erfasst Metriken aller Systeme
- **Log-Aggregation:** Zentrale Speicherung aller Logs
- **Management-Konsole:** Verwaltung von Systemen & Diensten
- **Status-Dashboard:** Zentrale Übersicht (Homepage)

---

## 📈 Komponenten

### Prometheus
- **Aufgabe:** Metrik-Erfassung & Zeitreihendatenbank
- **URL:** `http://[LOKAL]:9091`
- **Config:** `/etc/prometheus/prometheus.yml`
- **Retention:** TBD (Standard: 15d)

**Jobs:**
- `node-debian`: Debian Server Metriken
- `node-proxmox`: Proxmox Server Metriken
- `docker`: Docker Container Stats
- `proxmox`: Proxmox-spezifische Metriken

### Grafana
- **Aufgabe:** Dashboarding & Visualisierung
- **URL:** `http://[LOKAL]:3002`
- **Datasources:** Prometheus, Loki
- **Dashboards:** System, Proxmox, Docker, Netzwerk

### Loki
- **Aufgabe:** Log-Aggregation & Suche
- **URL:** `http://[LOKAL]:3100`
- **Storage:** `/var/lib/loki`
- **Retention:** TBD

**Log-Quellen:**
- Systemd Logs
- Docker Logs
- Applikations-Logs
- Netzwerk-Logs

### Uptime Kuma
- **Aufgabe:** Service Monitoring
- **URL:** `http://[LOKAL]:3001`
- **Monitore:** HTTP, TCP, ICMP, SSL

### Scrutiny
- **Aufgabe:** SMART-Festplattenüberwachung
- **URL:** `http://[LOKAL]:8085`
- **Datenbank:** SQLite
- **Alerts:** Bei S.M.A.R.T. Warnungen

### Cockpit
- **Aufgabe:** Webbasierte Serververwaltung
- **URL:** `https://[LOKAL]:9090`
- **Features:** System Info, Logs, Dateien, Benutzer

---

## 🔧 Installation & Setup

### Voraussetzungen
- Debian 12 (Bookworm)
- 4+ GB RAM
- 20+ GB Disk
- Netzwerk-Konnektivität

### Installationsschritte

```bash
# Updates
sudo apt update && sudo apt upgrade -y

# Prometheus Installation
sudo apt install -y prometheus prometheus-node-exporter

# Grafana Installation
sudo apt install -y grafana-server

# Loki Installation
# (Download from: https://github.com/grafana/loki/releases)

# Docker & Compose
sudo apt install -y docker.io docker-compose

# Weitere Services
sudo apt install -y cockpit cockpit-docker
```

---

## 📋 Wartung & Checks

### Tägliche Checks
- [ ] Alle Services laufen (systemctl status)
- [ ] Keine kritischen Alerts in Grafana
- [ ] Disk-Space-Nutzung prüfen
- [ ] Netzwerk-Konnektivität

### Wöchentliche Checks
- [ ] Backup-Status prüfen
- [ ] Log-Größe überprüfen
- [ ] Performance-Trends analysieren
- [ ] Updates verfügbar?

### Monatliche Checks
- [ ] Sicherheits-Updates einspielen
- [ ] Zertifikate überprüfen
- [ ] Disaster Recovery testen
- [ ] Dokumentation aktualisieren

---

## 🚀 Tipps & Tricks

### Query-Beispiele (PromQL)

```promql
# CPU-Nutzung
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Speicher-Nutzung
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

# Disk-Space
(1 - (node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"})) * 100
```

### Log-Suche (Loki)

```
{job="systemd"} | grep "error"
{container="jellyfin"} | json | status=`5xx`
```

---

## 📖 Weitere Ressourcen

- [Prometheus Docs](https://prometheus.io/docs/)
- [Grafana Docs](https://grafana.com/docs/)
- [Loki Docs](https://grafana.com/docs/loki/)
- [Uptime Kuma](https://uptime.kuma.pet/)
- [Scrutiny](https://github.com/AnalogJ/scrutiny)
- [Cockpit Project](https://cockpit-project.org/)
