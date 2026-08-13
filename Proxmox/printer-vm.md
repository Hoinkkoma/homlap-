# Proxmox: Drucker-VM (Debian) — Drucker-Gateway mit CUPS

Kurz: Diese Anleitung beschreibt, wie du auf dem Proxmox-Host eine Debian-VM betreibst, die als Print-Gateway für den HP OfficeJet 4558 fungiert. Die VM verwendet CUPS + HPLIP und kann den Drucker über einen Digitus-Printserver (Netzwerk → USB) oder direkt per USB verwalten.

Hinweis: Ersetze Platzhalter wie <VM_IP>, <DIGITUS_IP>, <PRINTER_NAME> mit deinen echten Werten.

## Ziel
- Debian-VM stellt einen Netzwerkdrucker per IPP/CUPS bereit.
- CUPS nimmt Druckaufträge von deiner Homepage-Anwendung entgegen und leitet sie an den Digitus-Printserver (socket://) weiter oder verwaltet den Drucker per USB.

## Voraussetzungen
- Proxmox VE installiert und funktionsfähig.
- Debian 12 VM (empfohlen: 1–2 vCPU, 2–4 GB RAM, 8–16 GB Disk).
- Netzwerk-Bridge auf Proxmox (z. B. vmbr0), VM hat LAN-Zugriff.
- Digitus Printserver im selben LAN (oder erreichbar von der VM).
- Optional: USB-Passthrough wenn Drucker direkt an VM angeschlossen werden soll.

## Netzwerk / Proxmox-Einstellungen
- Stelle sicher, dass die VM ein Bridged-Interface hat (z. B. vmbr0):
  - In Proxmox GUI: VM -> Hardware -> Network Device -> Bridge: vmbr0
- Vergib eine statische IP (empfohlen) oder richte eine DHCP-Reservierung in deinem Router ein.
- Falls du USB vom Host an die VM weitergeben willst: Hardware -> Add -> USB Device (oder PCI passthrough für spezielle Hubs).

## Installation auf der Debian-VM

1. System aktualisieren

```bash
sudo apt update && sudo apt upgrade -y
```

2. Benötigte Pakete installieren

```bash
sudo apt install -y cups hplip hplip-gui avahi-daemon avahi-utils
# Optional: sane-utils falls Scannen nötig
sudo apt install -y sane-utils
```

3. CUPS aktivieren

```bash
sudo systemctl enable --now cups
sudo systemctl status cups
```

4. Firewall (ufw) anpassen (falls aktiv)

```bash
# IPP (CUPS)
sudo ufw allow 631/tcp
# mDNS (Avahi) für lokale Discovery
sudo ufw allow 5353/udp
```

5. Avahi aktivieren (für mDNS/Bonjour)

```bash
sudo systemctl enable --now avahi-daemon
```

## Drucker hinzufügen

Variante A — Digitus Printserver (Network/JetDirect/Raw)

- Wenn der Digitus den Drucker per Port 9100 (JetDirect/raw) freigibt, kannst du die Verbindung so anlegen:

```bash
sudo lpadmin -p <PRINTER_NAME> -E -v socket://<DIGITUS_IP>:9100 -m everywhere
sudo cupsdisable <PRINTER_NAME> || true
sudo cupsenable <PRINTER_NAME>
```

- `-m everywhere` verwendet den generischen IPP/Driverless-Workflow (CUPS/IPP Everywhere). Falls ein spezifischer PPD nötig ist, nutze `-m /path/to/ppd` oder `-P`.

Variante B — LPD

```bash
sudo lpadmin -p <PRINTER_NAME> -E -v lpd://<DIGITUS_IP>/queue -m everywhere
```

Variante C — USB direkt an VM

- Wenn der Drucker per USB an die VM durchgereicht wird, nutze `hp-setup` (HPLIP) für HP-Modelle:

```bash
sudo hp-setup -i
# oder interaktive GUI falls X vorhanden
sudo hp-setup
```

## CUPS-Freigabe konfigurieren

- WebUI: http://<VM_IP>:631 (Administration -> Server -> Share printers connected to this system)
- Oder per CLI:

```bash
# Erlaube remote printing (nur bei geschütztem LAN!)
sudo cupsctl --remote-any
# Optional: Erlaube Browsing/Sharing über Avahi
sudo cupsctl --share-printers
```

- Für restriktivere Konfigurationen editiere `/etc/cups/cupsd.conf` und definiere Zugriffsregeln für `<Location /printers>` und `<Location />`.

Beispiel (nur LAN zugriff):

```conf
<Location />
  Order allow,deny
  Allow from 192.168.1.0/24
</Location>
```

Nach Änderung: `sudo systemctl restart cups`

## Testen

- Status des Druckers:

```bash
lpstat -p
lpstat -d
```

- Testseite drucken (Text):

```bash
echo "Testseite" | lp -d <PRINTER_NAME>
```

- Direkt zum Digitus-Port testen (Netcat):

```bash
nc -vz <DIGITUS_IP> 9100
```

## Integration mit der Homepage-Anwendung
- Konfiguriere die App so, dass sie IPP (http://<VM_IP>:631/printers/<PRINTER_NAME>) oder `lp`/`lpr` verwendet.
- Wenn die App Dateien per HTTP POST liefert, richte einen kleinen Print-Worker ein (z. B. ein Script/Service), das empfangene PDFs an `lp` übergibt.

Beispiel Print-Worker (einfach):

```bash
#!/bin/bash
# print-worker.sh
FILE="$1"
lp -d <PRINTER_NAME> "$FILE"
```

## Fehlerbehebung
- CUPS-Logs: /var/log/cups/error_log (bei Bedarf Debug-Level einschalten in cupsd.conf)
- Prüfe Digitus-WebUI und ob Port 9100 offen ist
- Bei Treiberproblemen: `hp-check` (HPLIP), `hp-plugin` für proprietäre Plugins
- Bei Permission-Problemen: Prüfe dass `lp`-Benutzer Rechte hat oder Service als passender Benutzer läuft

## Sicherheit & Empfehlungen
- Wenn möglich, nur das interne LAN Zugriff gewähren (Firewall, cupsctl und cupsd.conf)
- Verwende mDNS/Avahi nur im vertrauenswürdigen Netz
- Setze Login/ACLs in CUPS falls mehrere Nutzer untrusted sind
- Halte HPLIP/CUPS auf aktuellem Stand

## Optional: USB-Passthrough anstatt Digitus
- Wenn Digitus Probleme macht, kannst du den Drucker direkt an den Proxmox-Host anschließen und per USB an die VM durchreichen (USB Device Passthrough). Nachteile: Hostabhängigkeit.

---

Weitere Anpassungen schreibe ich gern direkt in die Datei, wenn du konkrete IP-Adressen, VM-Namen oder Druckernamen angibst.
