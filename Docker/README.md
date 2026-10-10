# 🐳 Docker

Dokumentation zu Docker-Containern, Images, Swarm-Services und Portainer-Verwaltung.

---

## Überblick

Docker ist ein zentraler Baustein der Homelab-Umgebung. Die Containerversorgung umfasst unter anderem:

- zentrale Web- und Admin-Dienste
- Monitoring- und Log-Integration
- Medien- und Datei-Services
- interne Business-/Automation-Tools
- Docker Swarm-Services für verteilte Orchestrierung

---

## Grundprinzipien

- Vor jeder Migration: Ist-Zustand und Datenpersistenz prüfen
- Volumes und Konfigurationen separat sichern
- Zwischen eigenständigen Containern und Swarm-Services unterscheiden
- Ressourcenlimits und Platzierungsregeln dokumentieren
- Keine unbeaufsichtigten `docker system prune`- oder Volume-Löschungen durchführen

---

## Typische Bereiche

- Compose-Dateien und Stack-Definitionen
- Umgebungsvariablen und Secrets-Handling
- Portainer- oder Dockge-basierte Verwaltung
- Service-Healthchecks und Restart-Policies
- Volumes, Netzwerke und Backup-Strategien

---

## Empfohlene Struktur

```text
Docker/
├─ README.md
├─ compose/
│  ├─ homepage.yml
│  ├─ grafana.yml
│  └─ stack-template.yml
├─ volumes/
├─ env/
└─ notes/
```

---

## Verwaltung

### Dienstliste prüfen

```bash
docker ps -a
docker service ls
docker stack ls
```

### Volumes prüfen

```bash
docker volume ls
docker volume inspect <volume-name>
```

### Netzwerke prüfen

```bash
docker network ls
docker network inspect <network-name>
```

---

## Sicherheits- und Betriebsregeln

- sie keine Passwörter oder Tokens in Docker-Compose-Dateien übernehmen
- Secrets nur über sichere, dokumentierte Mechanismen verwalten
- Ports nur gezielt exponieren
- produktive Services mit Healthchecks und Monitoring absichern

---

## Hinweis

Die eigentlichen Container- und Stack-Konfigurationen sollten je nach Deployment lokal und separat gepflegt werden. Diese Datei dient als allgemeiner Einstieg und als dokumentierter Standardsatz für die Docker-Verwaltung im Homelab.
