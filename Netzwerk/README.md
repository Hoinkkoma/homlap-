# 🖧 Netzwerk & Verbindungen

Netzwerk-Infrastruktur, Sicherheit, Dienstzugriff und Zugriffsverwaltung des Homelabs.

---

## Überblick

Die Netzwerkarchitektur des Homelabs soll sauber und nachvollziehbar sein. Wichtige Ziele:

- getrennte Zonen für Management, Services, Print und Gäste/IoT
- sichere Remote-Zugriffe über Tailscale
- zentrale DNS- und Service-Discovery-Mechanismen
- keine produktiven Dienste ohne Absicherung

---

## Grundprinzipien

- Interne Zonen sauber trennen
- Firewall-Regeln strikt und dokumentiert halten
- Tailscale nur für definierte, notwendige Zugriffe verwenden
- DNS- und Dienstnamen statt fester IPs bevorzugen
- Legacy-Konfigurationen nicht blind übernehmen

---

## Typische Zonen

### Management-Zone

- Proxmox-WebUI
- Monitoring-Server
- Backup- und Admin-Dienste

### Services-Zone

- Medien- und App-Container
- Webdienste und interne Anwendungen
- zentrale Produktiv-Services

### Print-Zone

- CUPS- oder Druck-VM
- Digitus Printserver
- physische Drucker

### Guest-/IoT-Zone

- untrusted Geräte
- eingeschränkter Zugriff auf intern relevante Bereiche

---

## Tailscale

Tailscale dient als sichere, meshartige Verbindung zwischen Hosts und für Remote-Zugriffe ohne öffentliche Portfreigaben.

Empfohlene Nutzung:

- Remote-Zugriff auf zentrale Dienste
- sichere Verbindungen zwischen Servern und Verwaltungs-Hosts
- kontrollierte Subnet- oder Node-Routing-Konfigurationen

---

## Sicherheit

### Allgemeine Maßnahmen

- SSH-Keys statt Passwörter verwenden
- Firewall-Regeln mit Default-Deny und gezielten Regeln
- 2FA für kritische Dienste aktivieren, falls verfügbar
- Updates und Patches regelmäßig einspielen
- Backups der Konfigurationen regelmäßig sichern

### DNS und Website-Policies

- lokale DNS- und Hostnames statt unstrukturierter IP-Verweise nutzen
- keine unbekannten Domains aus externen Quellen automatisch akzeptieren
- Web- und Adminzugriffe nur über Authentifizierung oder VPN

---

## Monitoring & Diagnose

Wichtige Debug-Tools:

```bash
ping <hostname>
traceroute <hostname>
nslookup <hostname>
dig @<dns-server> <hostname>
```

---

## Geplante Verbesserungen

- [ ] VLAN-Segmentierung weiter ausbauen
- [ ] Firewall-Regeln optimieren
- [ ] Tailscale-Nutzung verifizieren
- [ ] Network- und Service-Discovery sauber dokumentieren
- [ ] Überwachung der Netzwerkkapazität erweitern

---

## Siehe auch

- [Debian Monitoring](../Debian-Monitoring/README.md)
- [Proxmox](../Proxmox/README.md)
- [Drucker und Printserver](./printserver-digitus.md)
