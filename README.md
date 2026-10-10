# 🏠 Hoinkkoma Homelab

**Letzte Aktualisierung:** 10. Oktober 2026  
**Maintainer:** @Hoinkkoma  
**Zweck:** Zentrale technische Dokumentation und Kontext für Hardware, Dienste, Sicherheit, Monitoring und zukünftige Erweiterungen.

> Hinweis: Diese öffentlich zugängliche Dokumentation enthält keine konkreten IP-Adressen. Für den betrieblichen Einsatz werden nur interne, freigegebene Konfigurationen verwendet. Relevante Netzwerkdaten werden lokal und separat verwaltet.

---

## 1. Projektübersicht

Das Homelab basiert auf einer selbst betriebenen Infrastruktur aus Proxmox VE, Debian, Docker und Docker Swarm. Die Umgebung dient als langlebiger, dokumentierter Betriebskontext für Verwaltung, Medien, Monitoring, KI- und Backup-Aufgaben.

Langfristige Zielbereiche:

- Proxmox-Cluster mit mehreren physischen Hosts
- Docker Swarm zur Verteilung von Containern über mehrere Rechner
- Zentrale Verwaltung mit Portainer und ergänzenden Tools
- Lokale KI- und Agenten-Umgebungen
- Monitoring, Metriken und Logs
- Netzwerkabsicherung und Fernzugriff über Tailscale
- AdGuard Home als DNS-Schutz
- Nextcloud für persönliche Daten und Fotos
- Jellyfin für Medienwiedergabe
- Separates NAS für Backups von VMs, Containern und Konfigurationen
- Automatisierte Wartung, Fehlersuche und kontrollierte Wiederherstellung

### Grundprinzipien

1. Bestehende Systeme nicht unnötig neu installieren.
2. Produktive Dienste möglichst ohne Unterbrechung betreiben.
3. Änderungen zuerst prüfen, dann planen und erst nach Bewertung ausführen.
4. Vor riskanten Änderungen Backups und Rollback-Möglichkeiten vorbereiten.
5. Hardware gezielt nach Aufgaben einsetzen.
6. GPU-intensive Anwendungen auf dem leistungsstärksten Proxmox-Host priorisieren.
7. Der Debian-Server muss dauerhaft erreichbar bleiben.
8. Interne Datenträger nicht ohne Zustimmung ändern.
9. Keine unkontrollierten Änderungen an Netzwerkbrücken, Firewallregeln, Swarm-Managern oder Storage vornehmen.
10. Sicherheitsrelevante Änderungen müssen nachvollziehbar und möglichst reversibel sein.

---

## 2. Physische Hardware

### 2.1 Proxmox-Tower `pve`

**Rolle:** Hauptserver, leistungsstarker Proxmox-Host, Docker-Swarm-Manager und GPU-Node.

- CPU: Intel Core i7-4770, 4 Kerne / 8 Threads
- RAM: ca. 32 GB
- GPU: NVIDIA Quadro P400
- Hypervisor: Proxmox VE
- Docker Engine: dokumentierter Stand mit aktiver Swarm-Konfiguration
- Dateisystem: ZFS

**Bekannte Eigenschaften**

- Der Tower ist der leistungsstärkste Rechner im Verbund.
- GPU-Workloads sollen bevorzugt hier laufen.
- Die Quadro P400 ist für bestimmte Jellyfin-Transkodierungsaufgaben vorgesehen.
- Nicht alle Container sollen dauerhaft auf diesem Host laufen.
- Das System soll langfristig Teil eines größeren Proxmox-Verbunds werden.

### 2.2 Debian-Server `debianserver`

**Rolle:** Dauerhaft laufender Infrastrukturserver, Netzwerkdienste und Docker-Node.

- Hardware: Mini-PC / Serverklasse
- Betriebssystem: Debian
- RAM: etwa 16 GB
- Tailscale: aktiv, für Fernzugriff und interne Kommunikation
- Docker Engine: dokumentierter Stand
- Docker Swarm: Node aktiv

**Wichtige Anforderung:** Dieser Server soll dauerhaft eingeschaltet bleiben. Er ist für zentrale Dienste und den Fernzugriff wichtig.

### 2.3 Debian-Node `debian-it`

**Rolle:** Zusätzlicher Swarm-Worker, leichte Container und mögliche Dashboard-Aufgaben.

- Hardware: älterer iMac / klassischer Rechner
- RAM: etwa 2 GB
- Betriebssystem: Debian
- Docker Engine: dokumentierter Stand
- Docker Swarm: Node aktiv

**Geplante Aufgaben**

- Leichte Dienste und Testcontainer
- Monitoring-Agenten
- Gegebenenfalls Dashboard- oder Anzeigeaufgaben
- Keine speicherintensiven KI-Modelle oder großen produktiven Workloads

### 2.4 HP ProDesk 400 G5 SFF

**Status:** Angeschafft; als Erweiterung der Infrastruktur vorgesehen.

- Formfaktor: Small Form Factor
- CPU: Intel Core i5-8500
- Ursprüngliche Ausstattung: 8 GB RAM, 128 GB Speicher
- Ausbaubedarf: potenziell auf 32 GB RAM

**Planung:**

- Verwendung als zusätzlicher Proxmox-Node
- Erweiterung für weitere Infrastrukturaufgaben
- Vor einer Installation müssen Hardwarezustand, RAM, Datenträger und aktuelle Betriebssysteminstallation geprüft werden

### 2.5 Weitere Netzwerkhardware

Bekannt sind:

- Unmanaged Switches
- Überspannungsschutz-Steckdosenleiste
- Digitus Printserver
- HP OfficeJet Drucker
- Weitere Strom- und Anschlusskabel

---

## 3. Netzwerk und Fernzugriff

### 3.1 Netzwerkübersicht

Das Homelab nutzt ein internes LAN sowie Tailscale für Fernzugriff und sichere Verknüpfung zwischen Hosts. Konkrete Adressen werden nur lokal in den Einsatzkonfigurationen verwaltet.

Wichtige Punkte:

- Interner Home-LAN mit separaten Gerätegruppen
- Tailscale für Fernzugriff und Knotenverbindung
- Zentrale Dienste auf stabilen, dauerhaft laufenden Hosts
- Historische Konfigurationen werden nicht blind übernommen

### 3.2 Proxmox-Netzwerkbesonderheiten

Es wurde eine problematische Netzwerkkonfiguration dokumentiert, darunter:

- Mehrere Bridges mit überlappenden oder duplizierten Subnet-Konfigurationen
- Tailscale-Interface mit lokaler Route
- Aktivierte physische Schnittstelle mit eingeschränkter Bandbreite
- Verknüpfung mit einem Nebenhost im internen LAN

**Wichtige Einschränkung:** Die Netzwerkkonfiguration darf nicht spontan geändert werden. Vor Änderungen sind Bridge-Zuordnungen, physische Schnittstellen, Routing, ARP/Nachbartabellen und VM-Netze vollständig zu erfassen.

### 3.3 Tailscale

Tailscale wird für Fernzugriff und Verbindungen zwischen Hosts verwendet.

Ziel:

- Sicherer Fernzugriff ohne öffentliche Portfreigaben
- Verbindungen zwischen Proxmox, Debian-Server und weiteren Knoten
- Nutzung von Subnet-Routing nur mit sauberer, dokumentierter Konfiguration

**Kritische Anforderung:** Der Debian-Server soll dauerhaft online bleiben, damit Fernzugriff und abhängige Netzwerkfunktionen nicht ausfallen.

---

## 4. Proxmox und Storage

### 4.1 Proxmox-Systempool

Der Proxmox-Host verwendet einen ZFS-Pool `rpool` mit internen Systemlaufwerken.

**Unverhandelbare Schutzregel:** Die internen Systemdatenträger dürfen nicht formatiert, neu partitioniert, überschrieben oder für Experimente verwendet werden, solange dies nicht ausdrücklich genehmigt wurde.

### 4.2 USB-Backup-Datenträger

Ein externer USB-Backup-Datenträger ist dokumentiert. Vor jeder Speicheroperation müssen Gerät, UUID, Mountpoint und tatsächlicher Inhalt einzeln geprüft werden.

**Wichtige Sicherheit:** Gerätebezeichnungen wie `/dev/sdX` sind nicht dauerhaft stabil und dürfen nicht unkritisch verwendet werden.

### 4.3 USB-ISO-Sticks

Dokumentierte ISO-Sticks wurden separat verwaltet. Ein früher gemeldetes Gerät mit null Byte Kapazität wurde als fehlerhaft eingestuft.

### 4.4 Backup-Strategie

Ziel ist eine saubere Trennung zwischen Produktivsystemen und Backups.

Geplant ist ein separates NAS, entweder als Eigenbau oder als fertige Lösung. Es soll insbesondere sichern:

- Proxmox-VMs
- Container sowie Container-Konfigurationen
- Docker-Stacks und Compose-Dateien
- Konfigurationen wichtiger Dienste
- Weitere relevante Infrastrukturdateien

Nextcloud ist für persönliche Daten und Fotos vorgesehen. Zusätzlich werden Backups auf dem NAS oder auf TrueNAS verwaltet. Kompression ist hilfreich, ersetzt aber keine unabhängigen Sicherungsebenen.

---

## 5. Docker Swarm

### 5.1 Architektur

Docker Swarm soll Container automatisch auf geeignete Nodes verteilen.

Dokumentierte Nodes:

- `pve`: leistungsstarker Host, GPU und zentrale Infrastruktur
- `debianserver`: dauerhaft verfügbare Infrastruktur- und Netzwerkdienste
- `debian-it`: leichte Container und Testdienste

Alle Nodes wurden als aktiv und bereit dokumentiert; der aktuelle Status muss vor Änderungen erneut geprüft werden.

### 5.2 Zielarchitektur

Die geplante Verteilung sieht ungefähr so aus:

- `pve`: leistungsintensive Container, GPU-Workloads und interne Infrastruktur
- `debianserver`: dauerhaft verfügbare Netzwerkdienste und mittelgroße Workloads
- `debian-it`: leichte Container, Testdienste und Anzeigeaufgaben
- HP ProDesk: künftige Erweiterung für zusätzliche Kapazität

Die Verteilung soll nicht nur auf festen Hostnamen basieren. Labels, Ressourcenanforderungen, Affinitäten und Platzierungsregeln sollen die Auswahl steuern.

### 5.3 Bekannte Node-Labels

Für die Nodes wurden Labels dokumentiert:

- `pve`: `gpu=nvidia`, `tier=heavy`
- `debianserver`: `gpu=none`, `tier=medium`
- `debian-it`: `gpu=none`, `tier=light`

Container mit GPU-Anforderungen müssen auf geeignete Hosts beschränkt werden. Ein Label allein garantiert keinen funktionierenden GPU-Zugriff; Treiber, Runtime und Gerätefreigabe müssen ebenfalls korrekt konfiguriert sein.

### 5.4 Platzierung und automatische Verteilung

Ein früherer Test mit nginx und einer Spread-Strategie war erfolgreich. Ziel ist:

- Container sinnvoll auf verfügbare Nodes verteilen
- Leichte Nodes vor Überlastung schützen
- GPU-Dienste gezielt auf `pve` platzieren
- Nicht alle Container unnötig auf dem Hauptserver konzentrieren
- Bei Ausfällen geeigneter Nodes kontrollierte Neuplatzierungen ermöglichen

### 5.5 Migrationsskript

Es wurde versucht, ein Skript zur Migration bestehender Docker-Workloads nach Swarm einzusetzen:

- `/usr/local/sbin/swarm-migrate-all.py`

Dieses Skript darf nicht als bereits funktionsfähig vorausgesetzt werden. Vor erneuter Nutzung:

1. Datei lesen und Syntax prüfen
2. Abhängigkeiten und Docker-Befehle prüfen
3. Bestehende Container, Volumes, Netzwerke und Portbindungen inventarisieren
4. Dry-Run oder Testlauf durchführen
5. Backup- und Rollback-Verfahren definieren
6. Zunächst unkritische Dienste migrieren

---

## 6. Container und Infrastruktur-Dienste

### 6.1 Homepage

- Host: Debian-Server
- Verzeichnis: `/opt/homepage`
- Image: `gethomepage/homepage`
- Port: `3000`

Homepage dient als zentrale Startseite für das Homelab.

Bekannte beziehungsweise vorgesehene Verknüpfungen:

- Proxmox
- Grafana
- VictoriaMetrics
- Portainer
- AdGuard Home
- Resilio

### 6.2 AdGuard Home

- DNS-Port: `53`
- Verwaltungsoberfläche: `8080`
- Hauptaufgabe: DNS-Filterung und Werbe-/Tracker-Blockierung

Es gab wiederholt Probleme mit der DNS-Funktion, darunter Fehlermeldungen wie `SERVFAIL` sowie unzuverlässige Blockierung. Diese müssen systematisch durch Abgleich der tatsächlichen DNS-Pfade, Upstream-Server und Konfigurationen überprüft werden.

### 6.3 Portainer

- Port: `9000`
- Aufgabe: Docker-Verwaltung und Übersicht über Container und Swarm

### 6.4 Weitere Verwaltungsdienste

Dokumentierte Dienste und Ports:

- Homepage: 3000
- Grafana: 3002
- VictoriaMetrics: 8428
- Portainer: 9000
- Dockge: 5001
- Dozzle: 9081
- AdGuard Admin: 8080
- DNS: 53
- SSH: 22
- Proxmox: 8006

### 6.5 Resilio

- Dokumentierter Port: `8888`
- War als Dienst im Homepage-Dashboard vorgesehen

---

## 7. Monitoring und Metriken

Das Monitoring soll den Zustand aller Hosts und wichtiger Dienste erfassen.

Geplante beziehungsweise vorhandene Komponenten:

- Prometheus für Scraping und Metriksammlung
- Grafana für Dashboards
- VictoriaMetrics für Zeitreihen
- Node Exporter oder vergleichbare Host-Exporter
- Docker- und Proxmox-Metriken

Zu überwachende Bereiche:

- CPU-Auslastung und Load
- RAM und Swap
- Datenträgerfüllstand und I/O
- ZFS-Pool-Zustand
- Docker-Container und Swarm-Tasks
- Netzwerkverfügbarkeit und Paketverluste
- Tailscale-Verbindungen
- DNS-Funktion und AdGuard-Status
- Temperaturen, sofern Sensoren verfügbar sind
- Backup-Ergebnisse und fehlgeschlagene Jobs

---

## 8. Lokale KI und Agenten

### 8.1 Bisherige KI-Infrastruktur

Es gab eine lokale KI-Installation mit Ollama.

Dokumentierte Bestandteile:

- Ollama mit persistentem Volume
- API-Port `11434`
- Modelle aus der Qwen2.5-Familie
- LibreChat unter eigenem Verzeichnis

### 8.2 Geplante Änderung

Es wurde der Wunsch geäußert, Ollama vollständig zu entfernen und durch ein agentenorientiertes Setup zu ersetzen.

Ziel:

- Lesen von Systemstatus und Logs
- Überwachung von Containern und VMs
- Analyse von Fehlermeldungen
- Erstellung von Wartungs- und Fehlerberichten
- Kontrolle von Backup-Ergebnissen
- Analyse von Proxmox- und Docker-Konfigurationen

### 8.3 Sicherheitsanforderungen für KI-Agenten

Die KI darf nicht automatisch uneingeschränkten Root-Zugriff erhalten.

Vorgesehene Sicherheitsstufen:

1. Lesen
2. Analysieren
3. Vorschlagen
4. Ausführen
5. Kritische Änderungen mit ausdrücklicher Bestätigung

Sensible Daten, Zugangsdaten, Tokens, private Fotos und interne Informationen dürfen nicht unkontrolliert an externe APIs übertragen werden.

---

## 9. Medienserver und GPU

Jellyfin und verwandte Medienanwendungen sind als GPU-Workloads vorgesehen.

Ziel:

- Medienwiedergabe am Fernseher
- Hardwarebeschleunigung, sofern von Codec, GPU und Treiber unterstützt
- Bevorzugte Platzierung auf `pve`
- Andere leichte Dienste weiterhin auf Debian-Nodes verteilen

Die NVIDIA Quadro P400 verfügt über begrenzten VRAM. Bei der Planung müssen VRAM, unterstützte Codecs, Treiber und Transkodierungsanforderungen berücksichtigt werden.

Ein weiterer erwähnter Dienst bzw. Projektname ist „Viaporama“. Seine genaue technische Rolle sollte vor Änderungen geklärt werden.

---

## 10. Firewall und Sicherheit

### 10.1 Proxmox-Firewall

Frühere Konfigurationen enthielten unter anderem:

- INPUT-Policy `ACCEPT`
- SSH-Freigaben
- Freigaben für Weboberflächen und Tailscale-Verkehr
- Interne Freigaben für bestimmte Nodes
- Abschlussregel für bestimmte Paketmengen

Diese Angaben sind kein Beleg für die heutige vollständige Firewallkonfiguration. Regeln müssen aktuell gelesen und im Zusammenspiel mit Proxmox, Docker und Tailscale bewertet werden.

### 10.2 Geplante Firewall-Härtung auf Debian

Ziel ist eine dauerhafte, systemd-gestützte Absicherung der veröffentlichten Ports.

- AdGuard und Verwaltungsdienste vor unberechtigtem Zugriff schützen
- Zugriffe auf relevante Ports auf erlaubte Netze begrenzen
- Tailscale-Verkehr über das lokale Tailscale-Interface weiter ermöglichen
- Docker-Portfreigaben und direkte IPv6-Erreichbarkeit berücksichtigen
- SSH-Zugriff nicht versehentlich sperren

**Wichtig:** Vor neuen Regeln sind `iptables`, `ip6tables`, `nftables`, Docker-Regeln und systemd-Units zu prüfen. Keine Firewall pauschal leeren und keine bestehende Remote-Verbindung gefährden.

---

## 11. Proxmox-Cluster und zukünftiger Ausbau

### 11.1 Ziel

Langfristig soll aus den vorhandenen und künftigen Rechnern ein Proxmox-Cluster entstehen.

Geplante Erweiterungen:

- HP ProDesk 400 G5 SFF als zusätzliche Plattform
- Weitere DDR4-Mini-PCs mit zwei RAM-Steckplätzen
- Bis zu 32 GB RAM je geeignetem Node
- Weitere kleine Switches für spätere Netzwerkerweiterung
- Separates NAS für Backups

### 11.2 Hardwareprioritäten

Bei künftigen Käufen:

- Gebrauchte Hardware bevorzugen, wenn sie zuverlässig und wirtschaftlich ist
- Mindestens eine geeignete Core-i3-/Core-i5-Klasse für anspruchsvollere Nodes
- DDR4 und nach Möglichkeit zwei RAM-Steckplätze
- Aufrüstbarkeit auf 32 GB RAM
- Ausreichend Storage für geplante VMs
- Stromverbrauch, Lautstärke und Dauerbetriebsfähigkeit beachten

### 11.3 NAS-Strategie

Das NAS soll unabhängig von den produktiven Hosts betrieben werden, damit ein Ausfall des Hauptservers nicht automatisch auch alle Backups zerstört.

Offen bleiben:

- Eigenbau oder Fertigsystem
- TrueNAS oder anderes NAS-Betriebssystem
- Laufwerksanzahl und Kapazität
- Redundanzkonzept
- Netzwerkgeschwindigkeit
- Backup-Aufbewahrung
- Verschlüsselung
- Wiederherstellungstests

---

## 12. Offene Fehler und Aufgaben

### Priorität 1 – Netzwerk und DNS

- [ ] AdGuard-Blockierung zuverlässig zum Laufen bringen
- [ ] DNS-Fehler anhand aktueller Logs untersuchen
- [ ] Tailscale-DNS-Einstellungen und Client-Konfiguration prüfen
- [ ] Aktuelle Netzwerkinterfaces, Bridges und Routingtabellen erfassen
- [ ] Doppelte Netzkonfiguration prüfen
- [ ] Debian-Server-Dauerbetrieb und Stromsparregeln kontrollieren
- [ ] Erreichbarkeit des Printservers untersuchen

### Priorität 2 – Docker Swarm

- [ ] Aktuellen Node- und Managerstatus erfassen
- [ ] Alle laufenden Container, Images, Volumes und Netzwerke inventarisieren
- [ ] Bestehende Services und Swarm-Stacks dokumentieren
- [ ] Ressourcenlimits und Placement Constraints überprüfen
- [ ] GPU-Zugriff für bestimmte Dienste prüfen
- [ ] Fehler im Migrationsskript untersuchen
- [ ] Migration ausschließlich nach Test und Backup durchführen

### Priorität 3 – Monitoring

- [ ] Prometheus-Targets auf Erreichbarkeit prüfen
- [ ] Exporter auf allen Hosts überprüfen
- [ ] Grafana-Dashboards für CPU, RAM, Storage, Docker und Netzwerk ergänzen
- [ ] Verfügbarkeit von AdGuard, Tailscale und Backup-Jobs überwachen

### Priorität 4 – Backups und Storage

- [ ] USB-Backup-Datenträger und tatsächlichen Mountstatus prüfen
- [ ] Backup-Konzept für VMs, Container und Konfigurationen festlegen
- [ ] NAS-Anforderungen und Budget konkretisieren
- [ ] Wiederherstellungstests etablieren
- [ ] Interne ZFS-Systemdatenträger unangetastet lassen

### Priorität 5 – KI

- [ ] Bestehenden KI-Stack vollständig inventarisieren
- [ ] Entscheiden, welche Komponenten entfernt werden
- [ ] OpenClaw-/Agentenarchitektur festlegen
- [ ] Lesezugriffe und erlaubte Aktionen strikt begrenzen
- [ ] Externe Datenübertragung kontrollieren
- [ ] Erst nach erfolgreichem Ersatz alte Modelle und Volumes entfernen

### Priorität 6 – Ausbau

- [ ] HP ProDesk vollständig inventarisieren
- [ ] RAM- und Storage-Ausbau prüfen
- [ ] Proxmox-Cluster-Erweiterung planen
- [ ] NAS und Backup-Netzwerk umsetzen
- [ ] Weitere Container und Dienste sinnvoll auf Nodes verteilen

---

## 13. Arbeitsweise für GitHub Copilot

Copilot soll dieses Dokument als Projektkontext verwenden und bei allen Änderungen folgende Regeln beachten.

### Vor jeder Änderung

1. Ist-Zustand erfassen
2. Betroffene Hosts, Container, Volumes und Netzwerke identifizieren
3. Abhängigkeiten und Auswirkungen analysieren
4. Backup- und Rollback-Möglichkeit bestimmen
5. Zunächst einen Plan und konkrete Befehle präsentieren
6. Riskante Änderungen erst nach ausdrücklicher Freigabe ausführen

### Bei Shell-Skripten

- Bash-Skripte robust und nachvollziehbar gestalten
- Für wichtige Skripte `set -Eeuo pipefail` verwenden, sofern kompatibel
- Eingaben validieren und Fehler verständlich melden
- Änderungen idempotent gestalten, wenn möglich
- Vor Änderungen vorhandene Dateien sichern
- Konfigurationen nachvollziehbar erzeugen
- Keine Passwörter, Tokens oder privaten Schlüssel in Git commits übernehmen

### Bei Docker

- Vorhandene Volumes und persistente Daten schützen
- Zwischen eigenständigen Containern und Swarm-Services unterscheiden
- Keine unbedachten `docker system prune`, Volume-Löschungen oder pauschalen Containerentfernungen
- Ressourcenlimits und Placement Constraints begründen
- Docker- und Swarm-Versionen auf den Nodes berücksichtigen
- Vor Migrationen bestehende Konfigurationen sichern

### Bei Proxmox und Storage

- Keine Datenträger formatieren oder partitionieren, ohne ausdrückliche Zustimmung
- Interne Systemdatenträger schützen
- Geräte anhand von Modell, Seriennummer, UUID und Mountstatus verifizieren
- Keine Änderungen an Bridges, Routing oder Firewall ohne Analyse der Fernzugriffsauswirkungen
- Keine ungetesteten Änderungen an produktiven VMs vornehmen

### Bei KI-Agenten

- Prinzip der minimalen Berechtigung
- Read-only als Standard
- Schreibzugriffe nur für klar definierte Aufgaben
- Kritische Aktionen mit Bestätigung
- Keine unkontrollierte Übertragung interner Daten an externe Anbieter

---

## 14. Wichtige technische Bestandsaufnahme

Die Dokumentation enthält sowohl bestätigte Hardwaredaten als auch historische Konfigurationsstände. Sie darf deshalb nicht als Ersatz für eine aktuelle Inventarisierung dienen.

Zu Beginn einer neuen Arbeit sollte Copilot – sofern die betreffenden Hosts erreichbar sind – mindestens Folgendes erfassen:

**Proxmox**

- Proxmox-Version und Kernel
- Netzwerkinterfaces, Bridges, IP-Adressen und Routing
- ZFS-Poolstatus
- Storage-Konfiguration
- VMs und Container
- Firewallstatus

**Docker**

- `docker node ls`
- `docker node inspect`
- `docker service ls`
- `docker stack ls`
- `docker ps -a`
- `docker volume ls`
- `docker network ls`
- Docker-Versionen aller Nodes

**Debian**

- Betriebssystem und Kernel
- systemd-Units für Netzwerk, Firewall und Stromsparfunktionen
- Tailscale-Status
- DNS-Auflösung und Erreichbarkeit der veröffentlichten Dienste
- Mounts, Datenträger und Speicherplatz

**Monitoring**

- Prometheus-Targetstatus
- Verfügbarkeit der Exporter
- Grafana- und VictoriaMetrics-Zustand

---

## 15. Zusammenfassung

Das Homelab besteht derzeit im Wesentlichen aus einem leistungsstarken Proxmox-Tower, einem dauerhaft verfügbaren Debian-Server und einem leistungsschwachen Debian-Node. Ein HP ProDesk 400 G5 SFF ist als Erweiterung vorhanden beziehungsweise vorgesehen.

Docker Swarm soll die Container sinnvoll verteilen, während Proxmox die Virtualisierung bereitstellt. Tailscale ermöglicht den Fernzugriff. AdGuard, Homepage, Portainer und das Monitoring bilden wichtige Teile der Infrastruktur.

Die wichtigsten nächsten Schritte sind:

1. Netzwerk- und DNS-Zustand stabilisieren
2. Tatsächliche Docker-Swarm-Konfiguration inventarisieren
3. Persistente Daten und Backup-Strategie absichern
4. KI-Agenten mit begrenzten Berechtigungen einführen
5. Die Proxmox-Infrastruktur kontrolliert erweitern
6. Ein separates NAS für VM- und Container-Backups aufbauen

**Leitlinie:** Erst den tatsächlichen Zustand feststellen, dann eine nachvollziehbare Änderung planen und erst nach Prüfung ausführen. Stabilität, Datensicherheit und Fernzugriff haben Vorrang vor schnellen Umbauten.

---

## 16. Repo-Übersicht

Die Repository-Struktur spiegelt die wichtigsten Bereiche wider:

- `Debian-Monitoring/` — Monitoring- und Management-Plattform
- `Debian/` — Debian-System, Sicherheit und Betrieb
- `Docker/` — Container, Stack-Definitionen und Portainer
- `Proxmox/` — Proxmox-Konfigurationen, VM/Container-Management und KI-Umgebungen
- `Netzwerk/` — Topologie, Firewall, Drucker und Problemlösung
- `Fehlerbehebung/` — Dokumentation bekannter Fehler und Recovery-Prozesse
- `Wartung/` — Checklisten, Backups und Betriebsroutinen

**Quick Start**

- Debian Monitoring: `Debian-Monitoring/README.md`
- Proxmox: `Proxmox/README.md`
- Docker: `Docker/README.md`
- Netzwerk: `Netzwerk/README.md`
- Fehlerbehebung: `Fehlerbehebung/`

**Status:** Aktiv
