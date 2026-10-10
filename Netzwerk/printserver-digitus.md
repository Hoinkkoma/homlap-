# Netzwerk: Digitus Printserver — Einrichtung & Tipps

Kurz: Diese Seite beschreibt das Prinzip der Integration eines Digitus Printservers mit einem Rechner bzw. einer VM, die den Drucker über CUPS oder alternativen Druckprotokollen freigibt.

---

## Ziel

- Digitus verbindet einen Drucker per USB oder Netzwerk mit der internen Infrastruktur
- Printserver stellt den Drucker für das LAN oder für eine dedizierte VM bereit
- CUPS oder ein gleichwertiger Druck-Stack verarbeitet die Jobs und liefert sie an den Printserver

---

## Physische Verkabelung

- USB: Drucker → Digitus Printserver
- Ethernet: Digitus Printserver → Switch / Router / interne Infrastruktur
- Stromversorgung sicher und stabil betreiben

---

## Netzwerkeinstellungen

- statische IP oder DHCP-Reservierung bevorzugen
- Hostname oder stabilen Namen im lokalen DNS oder DHCP reservieren
- Gerät nur in der dafür vorgesehenen Druck- oder Admin-Zone betreiben

---

## Protokolloptionen

Typische Optionen:

- Raw/JetDirect auf Port `9100`
- LPD
- IPP

Wichtige Empfehlung:

- Für einfache Drucker-Integration gilt Raw/JetDirect oft als robust und gut nachvollziehbar.
- IPP oder LPD nur dann nutzen, wenn das Gerät oder die Umgebung das ausdrücklich unterstützt.

---

## Konfigurationsprinzipien

1. Hostname/Netzwerk stabil halten
2. CUPS oder alternativen Druck-Stack nur mit begrenztem Zugriff betreiben
3. Druckserver in eigener Druck-Zone oder klarer Segmentierung verwalten
4. Zugriff nur aus benötigten Zonen erlauben

---

## Testen

```bash
nc -vz <DIGITUS_HOSTNAME> 9100
lpstat -p
lpstat -d
```

Vergewissere dich vor dem Einsatz, dass der Drucker und der Printserver sauber erreichbar sind und die CUPS-/Druck-Konfiguration dokumentiert ist.

---

## Troubleshooting

- kein Netzwerkzugriff: Kabel, Switch-Port, Link-Status und Geräte-Status prüfen
- Port 9100 nicht erreichbar: Printserver und Firewall prüfen
- Druckaufträge fehlschlagen: CUPS-Logs, Treiber und Protokoll-Konfiguration prüfen
- Auswahl des falschen Druckprotokolls vermeiden

---

## Sicherheit

- Druck-Services nur im vertrauenswürdigen LAN oder über VPN nutzen
- Management-Endpunkte nicht öffentlich freigeben
- empfindliche Drucker- oder Hostnamen nicht unkontrolliert in Doku eintragen

---

## Empfehlungen

- DHCP-Reservierung oder fixen Hostnamen verwenden
- Druck-umgebungen isoliert betreiben
- Backups der CUPS-Konfiguration und Drucker-Profiles regulär erstellen

---

## Hinweis

Die genaue produktive Netzwerktopologie kann je nach Hausnetzwerk variieren. Für den operativen Betrieb müssen die finalen Werte lokal und separat validiert werden.
