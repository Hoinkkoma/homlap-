# 📊 Debian Monitoring Server

Zentrale Monitoring- und Management-Plattform des Homelabs.

---

## Überblick

Der Debian Monitoring Server übernimmt folgende Aufgaben:

- Monitoring-Zentrale für Host- und Service-Metriken
- Log-Aggregation und zentrale Sicht auf Fehler und Aktivitäten
- Management-Konsole für Server- und Dienstzustände
- Status-Dashboard für den Betrieb des Homelabs

---

## Komponenten

### Prometheus

- Aufgabe: Metrik-Erfassung und Zeitreihendatenbank
- Konfigurationsdatei: `/etc/prometheus/prometheus.yml`
- Typische Ziele: Host-Metriken, Container-Metriken, Docker- und Service-Endpoints

### Grafana

- Aufgabe: Dashboards und Visualisierung
- Typischer Zugriff: `http://[LOKAL]:3002`
- Datasources: Prometheus, Loki, VictoriaMetrics (je nach Deployment)

### Loki / VictoriaMetrics

- Aufgabe: Log- und Zeitreihenspeicherung
- Einsatz: zentrale Diagnose, Trendanalyse, Service-Status

### Uptime Kuma

- Aufgabe: Service-Monitoring
- Zweck: HTTP-, TCP-, ICMP- und SSL-Checks für kritische Dienste

### Scrutiny

- Aufgabe: SMART-Festplattenüberwachung
- Zweck: Laufwerkszustände und mögliche Fehler früh erkennen

### Cockpit

- Aufgabe: Webbasierte Serververwaltung
- Zweck: Systemstatus, Dienste, Logs und Basisverwaltung zentral

---

## Anforderungen

- Debian-basierter Host mit ausreichender Ressourcenreserve
- Netzwerk-Konnektivität zum Rest des Homelabs
- Speicherplatz für Logs, Metriken und Backups
- stabile, lokale Zugriffskontrolle

---

## Installation & Setup

### Voraussetzungen

- Debian 12 (Bookworm) oder neuer
- 4 GB RAM oder mehr
- 20 GB Festplattenspeicher oder mehr
- funktionierende Netzwerkkonfiguration

### Beispiel-Installation

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y prometheus prometheus-node-exporter
sudo apt install -y grafana-server
sudo apt install -y docker.io docker-compose
sudo apt install -y cockpit cockpit-docker
```

---

## Wartung & Checks

### Tägliche Checks

- [ ] Alle Services laufen
- [ ] Keine kritischen Alerts in Grafana
- [ ] Speicher- und Logspeicher prüfen
- [ ] Netzwerkverbindung und wichtige Services testen

### Wöchentliche Checks

- [ ] Backup-Status prüfen
- [ ] Log-Größe und Retention kontrollieren
- [ ] Performance-Trends analysieren
- [ ] Updates und Sicherheits-Patches prüfen

### Monatliche Checks

- [ ] Sicherheits-Updates einspielen
- [ ] Zertifikate und Auth prüfen
- [ ] Disaster-Recovery und Restore-Prozesse testen
- [ ] Dokumentation aktualisieren

---

## PromQL-Beispiele

```promql
# CPU-Nutzung
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Speicher-Nutzung
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

# Disk-Space
(1 - (node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"})) * 100
```

---

## Hinweise

- Für reale Zielendpunkte sollten nur Hostnames oder interne, freigegebene Endpunkte genutzt werden.
- Anfällige oder fehlerhafte Monitoring-Targets bevorzugt als lokale, dokumentierte Services betreiben.
- Für produktive Installationen sollten Daten und Logs regelmäßig gesichert werden.

---

## Weitere Ressourcen

- [Prometheus Docs](https://prometheus.io/docs/)
- [Grafana Docs](https://grafana.com/docs/)
- [Uptime Kuma](https://uptime.kuma.pet/)
- [Cockpit Project](https://cockpit-project.org/)
