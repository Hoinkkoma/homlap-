# HP OfficeJet 4658 über Digitus Printserver (Proxmox / Debian-VM)

Ziel

Diese Seite dokumentiert, wie ein HP OfficeJet 4658 an einen Digitus Printserver angeschlossen ist, der per LAN am Proxmox-Host hängt. Auf dem Proxmox-Host läuft eine Debian-VM, die die HP-Treiber und CUPS bereitstellt. Über die Homepage (Dashboard) wird eine einfache Upload-/Druck-Oberfläche angeboten (oder alternativ die CUPS-Weboberfläche verlinkt).

Topologie (vereinfacht)

Host/Hardware:

- HP OfficeJet 4658 (USB an Printserver)
- Digitus Printserver (USB -> Netzwerk)
- Proxmox-Server (LAN)
  - Debian VM (IP z. B. 192.168.1.20)
    - CUPS + HPLIP installiert
    - Drucker angelegt (Device-URI: socket://<printserver-ip>:9100 oder driverless)

Beispielstruktur:

Proxmox --- LAN --- Digitus Printserver --- USB --- HP OfficeJet 4658
          |
          +- Debian-VM (CUPS/HPLIP) -- Webinterface (z. B. http://192.168.1.20:631)

Benötigte Pakete (Debian VM)

- cups
- hplip
- ggf. printer-driver-hpcups oder andere HPLIP-Treiberpakete
- (optional) php, nginx/apache2 - wenn eine eigene Upload-Weboberfläche verwendet wird

Installation (Beispiel)

1) System aktualisieren:

```bash
sudo apt update && sudo apt upgrade -y
```

2) CUPS + HPLIP installieren:

```bash
sudo apt install -y cups hplip
# optional
sudo apt install -y printer-driver-hpcups
```

3) CUPS aktivieren:

```bash
sudo systemctl enable --now cups
```

Printerserver (Digitus) vorbereiten

- Drucker per USB an den Digitus anschließen.
- Digitus eine statische IP im LAN vergeben (z. B. 192.168.1.55) oder DHCP-Reservierung.
- Prüfen, ob Port 9100 (Raw/JetDirect) erreichbar ist: `nc -vz 192.168.1.55 9100` oder `telnet 192.168.1.55 9100`.

Drucker in CUPS anlegen

1) URI finden / verwenden:
   - Für viele Printserver ist die JetDirect/Socket-URI geeignet: socket://<PRINTSERVER_IP>:9100
   - Alternativ `ipp://` oder `ipps://` wenn der Printserver IPP unterstützt.

2) Drucker anlegen (Beispiel):

```bash
# Beispiel: socket-basiert
sudo lpadmin -p HP_OfficeJet_4658 -E -v socket://192.168.1.55:9100 -m everywhere
# oder mit einem passenden PPD/Driver
# sudo lpadmin -p HP_OfficeJet_4658 -E -v socket://192.168.1.55:9100 -P /path/to/ppd
sudo cupsenable HP_OfficeJet_4658
sudo cupsaccept HP_OfficeJet_4658
```

Hinweis: `lpinfo -v` listet erkannte Geräte/URIs auf.

Testdruck

```bash
echo "Testdruck" > /tmp/test.txt
lp -d HP_OfficeJet_4658 /tmp/test.txt
```

CUPS Webinterface

- Standard: http://<debian-vm-ip>:631
- Hier kann man Drucker konfigurieren, Jobs ansehen und drucken.

Einfache Upload-&-Druck-Oberfläche (minimal)

Wenn du eine sehr einfache Webseite möchtest, auf der man Dateien hochlädt und sofort druckt, geht das z. B. mit einem kleinen PHP-Skript. Das folgende ist ein Minimalbeispiel — KEINE Produktionssicherheit (Auth, Upload-Validierung, Limits fehlen!).

Datei: print_upload.php

```php
<?php
// Einfaches Beispiel — NICHT direkt öffentlich stellen ohne Absicherung!
if(
    isset($_FILES['file']) && $_FILES['file']['error'] === UPLOAD_ERR_OK
){
    $tmp = $_FILES['file']['tmp_name'];
    $name = basename($_FILES['file']['name']);
    $dest = "/tmp/print_".time()."_".$name;
    move_uploaded_file($tmp, $dest);
    // Druckbefehl
    $cmd = escapeshellcmd("/usr/bin/lp -d HP_OfficeJet_4658 " . escapeshellarg($dest));
    $out = [];
    $ret = 0;
    exec($cmd, $out, $ret);
    if($ret === 0){
        echo "Druckauftrag erfolgreich gesendet.";
    } else {
        echo "Fehler beim Senden des Druckauftrags.";
    }
} else {
?>
<form method="post" enctype="multipart/form-data">
    Datei: <input type="file" name="file" />
    <button type="submit">Drucken</button>
</form>
<?php
}
```

Sudoers (für Webserver-Druck ohne Passwort)

Um dem Webserver-Prozess (z. B. www-data) zu erlauben, `lp` zu verwenden, ohne Passwort zu verlangen, kann man eine gezielte sudoers-Regel anlegen. Beispiel (editieren mit `sudo visudo`):

```text
# erlauben, nur /usr/bin/lp ohne Passwort auszuführen
www-data ALL=(ALL) NOPASSWD: /usr/bin/lp
```

Dann das PHP-Skript so anpassen, dass es `sudo /usr/bin/lp ...` verwendet. Alternativ sichere die App besser über Auth/Tailscale und lasse das Webapp-Userkonto Mitglied in einer Druckergruppe sein.

Integration in die Homelab-Homepage (Dashboard)

1) Link zur CUPS-Weboberfläche:
   - Beispiel-Eintrag in deinem Dashboard: "Drucker (HP OfficeJet)" → http://192.168.1.20:631

2) Oder Link zur Upload-App (z. B. http://192.168.1.20/print_upload.php)

Beispiel-Text zum Einfügen in README/Dashboard:

```text
HP OfficeJet 4658 (über Digitus Printserver)
- Debian-VM auf Proxmox (IP: 192.168.1.20)
- Printserver-IP: 192.168.1.55
- CUPS-WebUI: http://192.168.1.20:631
- Upload&Print Interface: http://192.168.1.20/print_upload.php (falls eingerichtet)
```

Sicherheit & Empfehlungen

- Die Weboberfläche oder CUPS nur im LAN oder über Tailscale erreichbar machen — nicht offen ins Internet.
- CUPS absichern (Auth, Admin-Passwort), Firewall-Regeln setzen.
- Upload-Dateien validieren (nur PDF/PS/Plaintext erlauben), Größen- und Timeout-Limits setzen.
- Logs rotieren und Druckaufträge überwachen.

Was ich gemacht habe

- Diese Dokumentation als neue Datei `Proxmox/hp-officejet-4658.md` im Repository hinzugefügt.

Was als Nächstes möglich ist

- Ich kann die README (Dashboard) automatisch anpassen und einen Link zur neuen Seite einfügen.
- Wenn du möchtest, erstelle ich die Beispiel-Upload-Webseite (print_upload.php) im Repository unter einem passenden Ordner (z. B. `Proxmox/webapp/`) und ergänze eine Anleitung zur sicheren Einrichtung (Reverse Proxy, TLS, Auth).

Sag mir bitte, ob ich zusätzlich die README aktualisieren oder die Beispiel-Webapp als Datei ins Repo legen soll, dann mache ich das gleich.