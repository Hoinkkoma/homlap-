# 🏠 Hoinkkoma Homelab

**Letzte Aktualisierung:** 10. Oktober 2026  
**Maintainer:** @Hoinkkoma  
**Zweck:** Zentrale technische Dokumentation und Projektkontext für Hardware, Dienste, Sicherheitsmaßnahmen, Monitoring und zukünftige Erweiterungen.

> Hinweis: Diese Dokumentation ist bewusst auf generische, sichere Konfigurationshinweise reduziert. Konkrete interne IP-Adressen und produktive Netzdaten werden lokal, separat und nur mit Freigabe gepflegt.

---

## 1. Projektübersicht

Das Homelab basiert auf einer selbst betriebenen Infrastruktur aus Proxmox VE, Debian, Docker und Docker Swarm. Ziel ist ein stabiler, dokumentierter Betrieb mit Fokus auf Datensicherheit, Verwaltung und Skalierbarkeit.

Wichtige Bereiche:

- Proxmox-Cluster mit mehreren physischen Hosts
- Docker Swarm zur Verteilung von Containern
- Zentrale Verwaltung mit Portainer und Zusatzwerkzeugen
- Lokale KI-/Agenten-Umgebungen
- Monitoring, Metriken und Logs
- Netzwerkabsicherung und Fernzugriff via Tailscale
- AdGuard Home als DNS-Schutz
- Nextcloud für persönliche Daten und Fotos
- Jellyfin für Medienwiedergabe
- Separates NAS für Backups von VMs, Containern und Konfigurationen
- Automatisierte Wartung, Fehleranalyse und kontrollierte Recovery

### Grundprinzipien

1. Bestehende Systeme nicht unnötig umstellen.
2. Produktive Dienste möglichst ohne Ausfall betreiben.
3. Vor Änderungen Ist-Zustand erfassen und Risiken prüfen.
4. Vor riskanten Änderungen Backups und Rollback-Pläne vorbereiten.
5. Hardware gezielt nach Aufgaben einsetzen.
6. GPU-lastige Arbeiten auf dem stärksten Proxmox-Host priorisieren.
7. Kritische Infrastrukturserver dauerhaft erreichbar halten.
8. Interne Datenträger nur mit ausdrücklicher Freigabe verändern.
9. Keine unkontrollierten Änderungen an Bridges, Firewall, Swarm oder Storage.
10. Sicherheitsrelevante Änderungen müssen nachvollziehbar und reversibel sein.

---

## 2. Infrastruktur

### Hardware-Übersicht

#### Proxmox Server
- **Hardware:** Lenovo ThinkCenter Tiny aus 2016
- **CPU:** Intel Core i7
- **RAM:** 32 GB
- **Storage:** zwei 256 GB SATA-SSDs + eine 500 GB externe SSD
- **Rolle:** leistungsstarker Proxmox-Host, Docker-Swarm-Manager und GPU-Node
- **Hypervisor:** Proxmox VE
- **Speicher:** ZFS-basierter Systempool
- **GPU:** NVIDIA Quadro P400 oder vergleichbare GPU-Klasse
- **Aufgabe:** Virtualisierung, Container, KI-Workloads, Medien- und Monitoring-Services

#### Debian Server
- **Hardware:** Lenovo ThinkCenter Mini aus 2014
- **CPU:** Intel Core i3
- **RAM:** 16 GB
- **Storage:** 256 GB SATA-SSD
- **Rolle:** dauerhaft laufender Infrastrukturserver
- **Aufgabe:** zentrale Dienste, Monitoring, Netzwerkinfrastruktur, Verwaltung
- **Anforderungen:** hoher Verfügbarkeitsanspruch, Stromsparfunktionen kontrolliert, kein ungewollter Shutdown

#### Debian IT (iMac)
- **Hardware:** iMac aus 2006
- **CPU:** Intel Pentium
- **RAM:** 2 GB
- **Rolle:** zusätzlicher Swarm-Worker / leichtere Testaufgaben
- **Aufgabe:** leichtere Container, Tests und einfache Monitoring-Agenten

#### HP ProDesk 400 G5 SFF
- **Status:** als späterer Ausbaukandidat für weitere Infra-Kapazität vorgesehen
- **Aufgaben:** zusätzlicher Proxmox-Node, Erweiterung des Clusters, Backup-/Testumgebung

### Netzwerk-Hardware

- **8-Port Switch:** verbunden mit Internet und beiden Debian-Servern (direkt)
- **4-Port Switch:** verbunden mit dem 8-Port Switch, dient als Erweiterung für Proxmox-Server und zukünftige Proxmox-Cluster-Verbindungen und drei Think Clients
- **Überspannungsschutz:** 8-fach Steckdosenleiste
- **Weitere Komponenten:**
  - zwei unmenaged Switches
  - Hewlett Packard Jetdirect 400 Drucker
  - Digitus Print Server

---

## 3. Netzwerk und Fernzugriff

Das Homelab nutzt ein internes LAN sowie Tailscale für Fernzugriff und sichere Verknüpfung zwischen Hosts.

Wichtige Punkte:

- Lokales Heimnetz für interne Dienste
- Tailscale für Fernzugriff und Knotenkommunikation
- Trennung von Management-, Services- und Print-Zonen nach Möglichkeit
- Historische Konfigurationen nicht blind übernehmen

### Sicherheitsprinzipien

- Brücken, Routing und Firewall vor Änderungen vollständig prüfen
- Keine ungetesteten Änderungen an produktiven Netzen
- Tailscale- und DNS-Konfigurationen separat dokumentieren
- Drucker- und Printserver stets in eigener, begrenzter Zone betreiben

---

## 4. Storage und Backups

### Proxmox-Storage

- Interne ZFS-Systemdatenträger sind geschützt und dürfen nicht ohne Freigabe formatiert oder verändert werden.
- Backup-Laufwerke müssen vor jeder Nutzung neu verifiziert werden.

### Backup-Strategie

Ziel ist eine klare Trennung zwischen produktiven Systemen und Backups:

- Proxmox-VMs sichern
- Docker-Stacks und Compose-Dateien sichern
- Konfigurationen zentraler Dienste dokumentieren
- Separates NAS oder TrueNAS für langfristige Sicherung planen
- Nextcloud für persönliche Daten und Fotos getrennt betreiben

---

## 5. Docker Swarm

Docker Swarm dient als Orchestrierungsschicht für Container über mehrere Nodes.

Zielarchitektur:

- `pve`: CPU-/GPU-intensive Container und Kern-Services
- `debianserver`: dauerhaft verfügbare Infrastruktur- und Netzwerkdienste
- `debian-it`: leichtere Container, Tests und kleine Worker

Wichtige Regeln:

- Node-Status vor Eingriffen prüfen
- Ressourcenlimits, Constraints und Affinitäten dokumentieren
- GPU-Dienste gezielt auf passende Hosts platzieren
- Persistent Volumes, Netzwerke und Abhängigkeiten separat sichern

### Migrationsskript

`/usr/local/sbin/swarm-migrate-all.py` darf nicht als funktionsfähig vorausgesetzt werden. Vor erneutem Einsatz:

1. Datei lesen und Syntax prüfen
2. Abhängigkeiten und verwendete Docker-Befehle prüfen
3. Bestehende Container, Volumes, Netzwerke und Portbindungen inventarisieren
4. Dry-Run oder Testlauf durchführen
5. Backup- und Rollback-Verfahren festlegen
6. Erst unkritische Testdienste migrieren

---

## 6. Dienste und Anwendungen

### Homepage

- Zweck: zentrale Startseite / Dashboard des Homelab
- Host: Debian-Server
- Port: `3000` (Beispielwert, je nach Deployment variabel)
- Verwendung: Übersicht über wichtige Dienste, Monitoring, Portainer, AdGuard, Resilio usw.

### AdGuard Home

- Zweck: DNS-Filtern, Werbeblocker, interne Qualitätskontrolle
- Typische Ports: `53` (DNS), `8080` (Adminoberfläche)
- Wichtige Prüfung: Tailscale-DNS, Upstream-DNS und Fehlerzustände wie `SERVFAIL`

### Portainer

- Zweck: Docker-/Container-Verwaltung und Überblick über Swarm und Services
- Typischer Port: `9000`

### Monitoring

- Prometheus, Grafana, VictoriaMetrics, Node Exporter und vergleichbare Tools
- Ziel: Host-Metriken, Service-Status, Backup-Ergebnisse und Netzwerkzustand überwachen

### Jellyfin und Medien

- Zweck: Medien-Wiedergabe am Fernseher oder im LAN
- GPU-Workloads bevorzugt auf Proxmox-Hardware
- Hardwarebeschleunigung nur mit passenden Codecs, Treibern und VRAM-Anforderungen

---

## 7. KI und Agenten

Eine lokale KI-Umgebung mit Ollama war Teil der bisherigen Infrastruktur. Die langfristige Richtung ist eine agentenorientierte, lokal betriebene Architektur mit minimalen Berechtigungen.

Ziel:

- Status und Logs lesen
- Container und VMs überwachen
- Fehler analysieren
- Vorschläge dokumentieren
- Nur klar freigegebene, begrenzte Aktionen ausführen

Wichtige Sicherheitsprinzipien:

- Read-only als Standard
- Keine unkontrollierte Verbindung zu externen APIs
- Keine Berechtigungen über das erforderliche Maß hinaus
- Kritische Aktionen nur nach ausdrücklicher Freigabe

---

## 8. Firewall und Sicherheit

- Proxmox- und Debian-Firewall müssen regelmäßig überprüft werden
- Docker-Regeln und Tailscale-Verkehr sind mit der Host-Firewall zu koordinieren
- SSH, Web-UIs und wichtige Dienste nur gezielt freigeben
- Keine Firewall-Regeln pauschal entfernen, ohne Auswirkungen zu prüfen

Empfohlene Politik:

- Standard: nur notwendige Zugriffe erlauben
- Keine öffentlich exponierten Services ohne Authentifizierung oder VPN
- Keine Passwörter, Tokens oder Private Keys in Git-Commits aufnehmen

---

## 9. Ausbau und Cluster

Langfristig ist ein Proxmox-Cluster angestrebt. Geplante Erweiterungen:

- HP ProDesk 400 G5 SFF als zusätzlicher Knoten
- weitere DDR4-Mini-PCs mit 2 RAM-Steckplätzen
- Cluster-Schritte schrittweise und mit Prüfung
- separates NAS für Backups und Wiederherstellung

Prioritäten beim Hardwarekauf:

- Zuverlässige gebrauchte Hardware bevorzugen
- mindestens Core-i3-/Core-i5-Klasse für Knoten
- 32 GB RAM aufrüstbar
- genug Storage für VMs und Backups
- Stromverbrauch und Dauerbetrieb beachten

---

## 10. Offene Aufgaben

### Priorität 1 – Netzwerk und DNS

- [ ] AdGuard-Blockierung stabilisieren
- [ ] DNS-Fehler anhand aktueller Logs prüfen
- [ ] Tailscale- und Client-Konfiguration validieren
- [ ] Interfaces, Bridges und Routing aktualisieren
- [ ] Konflikte zwischen Printer-, LAN- und Management-Netz prüfen
- [ ] Debian-Server-Dauerbetrieb und Stromsparfunktionen absichern

### Priorität 2 – Docker Swarm

- [ ] Node- und Managerstatus aktuell erfassen
- [ ] Container, Images, Volumes und Netzwerke inventarisieren
- [ ] Services und Stacks dokumentieren
- [ ] Ressourcenlimits und Placement Constraints prüfen
- [ ] GPU-Zugriff für passende Dienste validieren
- [ ] Migrationsskript mit Dry-Run testen

### Priorität 3 – Monitoring

- [ ] Prometheus-Targets prüfen
- [ ] Exporter auf allen Hosts validieren
- [ ] Grafana-Dashboards ergänzen
- [ ] AdGuard-, Tailscale- und Backup-Zustände überwachen

### Priorität 4 – Backup und Storage

- [ ] USB-Backup-Datenträger prüfen
- [ ] Backup-Konzept für VMs, Container und Konfigurationen festlegen
- [ ] NAS-Anforderungen konkretisieren
- [ ] Wiederherstellungstests etablieren

### Priorität 5 – KI

- [ ] KI-Stack inventarisieren
- [ ] Entfernen/Ersetzen definieren
- [ ] Agentenarchitektur festlegen
- [ ] Berechtigungen und Datenfluss kontrollieren

### Priorität 6 – Ausbau

- [ ] HP ProDesk inventarisieren
- [ ] RAM-/Storage-Ausbau prüfen
- [ ] Proxmox-Cluster erweitern
- [ ] NAS und Backup-Netzwerk umsetzen

---

## 11. Repo-Übersicht

- `README.md` – zentrale Übersicht und Projektkontext
- `Debian-Monitoring/` – Monitoring und Management
- `Debian/` – Debian-basierte Dienste und Betrieb
- `Docker/` – Container, Stacks und Portainer
- `Proxmox/` – Hypervisor- und VM-Konfigurationen
- `Netzwerk/` – Topologie, Firewall, Drucker und Zugänge
- `Fehlerbehebung/` – bekannte Fehler und Recovery-Schritte
- `Wartung/` – Checklisten und Wartungspläne

**Status:** Aktiv
