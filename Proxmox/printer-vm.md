# Proxmox: Drucker-VM (Debian) — Drucker-Gateway mit CUPS

Diese Seite beschreibt das allgemeine Vorgehen, eine Debian-VM auf Proxmox als Drucker-Gateway zu betreiben. Sie dient als CUPS- oder Print-Frontend, der einen lokalen Printserver oder Drucker in der internen Infrastruktur anbindet.

---

## Ziel

- Proxmox-VM stellt einen zentralen Druck-Service bereit
- CUPS nimmt Druckaufträge entgegen
- Druckaufträge werden an den Digitus-Printserver oder einen direkt angebundenen Drucker geleitet
- Apps und Homepage können über einen dokumentierten, stabilen Workflow drucken

---

## Voraussetzungen

- Proxmox VE installiert und funktionsfähig
- Debian 12 VM oder vergleichbare Linux-VM
- Netzwerk-Zugriff auf das interne LAN oder auf die dedizierte Print-Zone
- Druckserver oder Drucker als Backend erreichbar
- optional USB-Passthrough, falls der Drucker direkt an die VM weitergereicht wird

---

## Grundkonfiguration der Debian-VM

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y cups hplip hplip-gui avahi-daemon avahi-utils
sudo systemctl enable --now cups
```

Zusätzliche Pakete je nach Drucker- und Scanner-Setup können hinzugefügt werden.

---

## Drucker anbinden

Es gibt verschiedene Wege:

- `socket://<host>:9100` für JetDirect/Raw-Druckserver
- `ipp://<host>/ipp/print` falls das Gerät IPP unterstützt
- `lpd://<host>/queue` für LPD-Umgebungen
- direktes USB beim Drucker, wenn das Gerät per USB an die VM durchgereicht wird

Die genaue URI hängt von Druckserver, Gerät und Umgebung ab.

---

## CUPS-Freigabe konfigurieren

- CUPS-Webinterface im lokalen Netz oder über Tailscale erreichbar machen
- notwendige Firewall-Regeln nur für erlaubte Zonen aktivieren
- Zugriffe auf die Druck-VM in klaren Sicherheitszonen halten

---

## Integration mit Homepage oder App

Wenn eine App PDF-Dateien oder Druckjobs senden soll, dann sollte der Ablauf dokumentiert und separiert werden:

- App erzeugt Druckauftrag
- Print-Worker oder CUPS-Queue übernimmt Verarbeitung
- Druckvorgang läuft über die definierte Druck-VM
- Erfolgs-/Fehlerstatus werden in Logs oder Monitoring sichtbar

---

## Fehlerbehebung

- CUPS Logs prüfen: `/var/log/cups/error_log`
- Druckserver- und Port-Erreichbarkeit prüfen
- Drucker-URI und Treiber testen
- Host-/Firewall/Netzwerkstatus validieren

---

## Sicherheit & Empfehlungen

- CUPS nur im internen, geplanten Netz oder per VPN erreichbar machen
- keine offenen Remote-Print-Ports in das Internet
- keine Zugangsdaten oder Druckermetadaten in Git-Dokumentation eintragen
- Drucker-Workflows mit dokumentierten, begrenzten Rechten betreiben

---

## Hinweis

Das konkrete Gerät, der Hostname und die Port-/URI-Details müssen lokal und separat in der tatsächlichen Umgebung validiert werden. Diese Datei dient als allgemeine Konfigurationsreferenz für den Betrieb einer proaktiven, abgesicherten Druck-VM.
