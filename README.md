# Homlap

Dies ist der Anfang meines persönlichen Homlap-Projekts. Das Projekt konzentriert sich auf **Sicherheit** und **Experimentierbarkeit**.

## Übersicht

Mein Homlap ist ein dezentrales Heimnetzwerk-Setup, das aus verschiedenen Geräten und Servern besteht:

### Kernkomponenten

- **Router**: Fritzbox als zentraler Netzwerk-Hub
- **Mobilgeräte**: Nothing Phone 2a, Nothing Buds 2a, Xiaomi Watch
- **Laptops**: ThinkPad T490s
- **Server**:
  - Debian Server (Mini) - Läuft Tailscale, Homepache, Pi-hole, Ukuma
  - TrueNAS Server (Tower) - Speicher- und Backup-Lösung
- **Netzwerk-Infrastruktur**: Switches, Tailscale für sichere Vernetzung

## Architektur-Diagramme

Eine detaillierte Übersicht der Netzwerk-Topologie und Services finden Sie in der `homlao.drawio` Datei (3 Seiten):
1. **Seite 1**: Komplette Netzwerk-Topologie
2. **Seite 2**: Debian Server Mini Services
3. **Seite 3**: Verfügbare Services/Anwendungen

## Design-Prinzipien

### 🔒 Sicherheit
- Tailscale für verschlüsselte Fernzugriffe
- Isolierte Netzwerksegmente
- Pi-hole für DNS-Sicherheit und Ad-blocking
- Sichere Authentifizierung auf allen Ebenen

### 🧪 Experimentierbarkeit
- Modulares Setup für einfaches Testen neuer Technologien
- Docker/Container-basierte Deployments wo möglich
- Einfache Rollback- und Recovery-Mechanismen
- Separate Test- und Produktionsumgebungen

## Services

Auf dem Debian Server Mini laufen folgende Services:
- **Tailscale**: Sicheres VPN-Netzwerk
- **Homepache**: (Home Automation/Monitoring)
- **Pi-hole**: DNS-Filter und Ad-blocking
- **Ukuma**: (Monitoring/Management-Tool)

Weitere Services/Dashboards:
- Glance
- Netdata
- it-tools
- Hermes Agent
- Immich
- ClamAV

## Nächste Schritte

- [ ] Dokumentation der Deployment-Prozesse
- [ ] Automation mit Ansible/Terraform
- [ ] Backup-Strategie definieren
- [ ] Monitoring und Alerting einrichten
- [ ] Security Hardening durchführen

---

**Status**: Anfang der Entwicklung  
**Zuletzt aktualisiert**: 2026-06-09