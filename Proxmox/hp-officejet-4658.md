# HP OfficeJet 4658 über Digitus Printserver (Proxmox / Debian-VM)

Diese Seite beschreibt das allgemeine Setup einer Drucker-Integration über einen Digitus Printserver mit einem lokalen Print-Stack, etwa CUPS auf einer Debian-VM oder einem vergleichbaren Host.

---

## Ziel

- Digitaler oder physischer Drucker wird über einen Printserver im LAN erreichbar
- CUPS oder ein ähnlicher Druck-Stack verarbeitet Aufträge
- Dienste und Apps können Druckaufträge an den Drucker weiterreichen, ohne direct hardware-dependent zu sein

---

## Topologie (vereinfacht)

```text
Proxmox Host
  └── Debian-VM / Druckstack
        └── Printserver (Digitus)
              └── HP OfficeJet
```

---

## Voraussetzungen

- Proxmox-Host mit VM oder lokaler Debian-Umgebung
- CUPS oder kompatibler Druckservice
- Digitus Printserver erreichbar im internen LAN
- Drucker kompatibel mit den im Einsatz verwendeten Treibern oder Raw/JetDirect

---

## Allgemeines Setup

1. Drucker und Printserver stabil vertikalen/physisch verbinden
2. Netzkonfiguration und Hostnamen dokumentieren
3. Druckserver in der Druck-Zone oder in der administrativen, abgesicherten LAN-Segmentierung betreiben
4. CUPS/Print-Stack auf der VM oder im hostseitigen Service konfigurieren
5. Drucker mit passendem Port-/URI-Konfigurationsschema an CUPS anbinden

---

## Empfohlene Konfigurationsprinzipien

- Verwendung von Hostnamen statt statischen IPs in Dokumentation, sofern stabil
- Zugriff nur aus den notwendigen Zonen und Services erlauben
- keine öffentlichen Zugriffe für Druckwebinterfaces oder Print-Management einrichten
- Logs und Druckstatus zentral überwachen

---

## Beispiel für CUPS/Print-Stack

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y cups hplip
sudo systemctl enable --now cups
```

Danach können Drucker je nach Ausstattung als Raw, LPD oder IPP verbunden werden. Die genaue URI hängt von Printserver, Gerätetyp und Umgebung ab.

---

## Testdruck

```bash
lpstat -p
lpstat -d
```

Für den Test kann ein einfacher Druckauftrag verwendet werden. Wichtiger ist die Verifikation der Drucker-Queue und des tatsächlichen Status auf der Maschine oder VM.

---

## Sicherheit & Betrieb

- CUPS nur im internen LAN oder über VPN freigeben
- Druckwebinterface nicht breit öffentlich verfügbar machen
- Logs regelmäßig prüfen
- bei mehrbenutzigen Umgebungen Zugriffskontrollen definieren

---

## Empfehlung

Die beste langfristige Lösung ist eine sauber dokumentierte Druckumgebung mit eigener Print-Zone, klaren DNS-/Hostnamen, stabilen Reservierungen und revierüberwachter CUPS-Integration. So bleibt das Setup wartbar und sicher.
