# Netzwerk: Digitus Printserver — Einrichtung & Tipps

Kurz: Diese Seite beschreibt, wie du den Digitus-Printserver so konfigurierst, dass er deinen HP OfficeJet 4558 per USB an das LAN freigibt und wie du ihn mit der Debian-VM/CUPS verbindest.

Hinweis: Ersetze `<DIGITUS_IP>`, `<PRINTER_NAME>` und weitere Platzhalter durch deine echten Werte.

## Ziel
- Digitus verbindet per USB mit dem HP OfficeJet 4558.
- Der Digitus stellt den Drucker im LAN bereit (Socket/JetDirect, LPD oder IPP).
- Die Debian-VM bindet den Drucker ein und stellt ihn über CUPS für die Anwendungen bereit.

## Physische Verkabelung
- USB: Digitus USB-Port → HP OfficeJet 4558 (USB-A/B Kabel)
- Ethernet: Digitus LAN → Switch / Proxmox Host / Router
- Versorge den Digitus wie vorgesehen mit Strom

## Netzwerkeinstellungen
- Vergib entweder eine DHCP-Reservierung in deiner Fritzbox oder eine statische IP direkt im Digitus Web-Interface, z. B. `192.168.1.50`.
- Notiere die IP: `<DIGITUS_IP>`

## Webinterface und Login
- Typischer Zugriff: http://<DIGITUS_IP>/ oder http://<DIGITUS_IP>:80
- Falls Login benötigt wird, verwende Admin-Creds (siehe Handbuch oder Aufkleber auf dem Gerät)

## Protokolloptionen
- Raw/JetDirect (Port 9100) — meist die einfachste Option
- LPD — wenn gewünscht
- IPP — falls unterstützt

Beispiel Device-URIs:
- socket://<DIGITUS_IP>:9100
- lpd://<DIGITUS_IP>/queue
- ipp://<DIGITUS_IP>/ipp/print

## Digitus konfigurieren (Kurz)
1. Logge dich in das Web-UI ein
2. Setze die Netzwerkeinstellungen (statisch oder DHCP-Reservation)
3. Wähle das Druckprotokoll (Raw/LPD/IPP)
4. Speichere und starte ggf. das Gerät neu

## Testen
- Prüfe Port 9100 (für Raw):

```bash
nc -vz <DIGITUS_IP> 9100
```

- Wenn Port offen ist, sende eine Testseite (PostScript/PDF) über `nc` oder `lp` von der Debian-VM:

```bash
# lokales Testfile an Digitus port 9100
cat testpage.ps | nc <DIGITUS_IP> 9100

# oder in CUPS als Raw-Drucker anlegen und lp verwenden
lp -d <PRINTER_NAME> testpage.pdf
```

## Kompatibilitätshinweise
- Nicht alle Printserver unterstützen alle Druckermodelle 100% (besonders bei Spezialfunktionen wie Duplex/Tintenlevel/Scanfunktionen). Für Basisdruck sind Raw/9100 meist zuverlässig.
- Falls Probleme auftreten, prüfe ob ein Firmware-Update für den Digitus verfügbar ist.

## Troubleshooting
- Kein Netzwerkzugriff: Prüfe Kabel, Switch-Port, Link-LEDs und IP-Konfiguration
- Port 9100 nicht erreichbar: Digitus prüfen, evtl. Firewall auf dem Netzwerksegment
- Druckdaten werden nicht umgesetzt: Teste andere Protokolle (LPD/IPP) oder direkt USB an VM
- Falls der Drucker proprietäre Treiber braucht (z. B. erweiterte Funktionen), ist USB-Direktanschluss oder HPLIP auf der VM die bessere Wahl

## Sicherheit
- Der Digitus steht im LAN — schütze das Netz (VLAN, Firewall) falls mehrere Nutzer Zugriff haben
- Deaktiviere entfernte Management-Optionen, falls nicht benötigt

## Empfehlungen
- Verwende Raw/JetDirect (socket://) für einfaches Setup
- Setze eine DHCP-Reservierung oder statische IP, damit URI stabil bleibt
- Teste mit `lp`/CUPS bevor du Integrationen in die Homepage aktivierst

---

Wenn du willst, kann ich noch eine Schritt-für-Schritt-Anleitung schreiben (mit Screenshots/Beispielen) für ein bestimmtes Digitus-Modell — nenne mir dann die Modellnummer oder lade ein Foto des Web-UIs hoch.