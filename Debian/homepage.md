# Homepage — kurze Beschreibung

moin — Die "Homepage" ist die Anwendung, die PDFs erzeugt bzw. Druckaufträge abschickt.

Kurz:
- Rolle: Erzeugt PDF oder sendet IPP‑Jobs an print‑vm.
- Integrationspunkte: HTTP Upload → Dropfolder / IPP URL: http://<PRINTER_VM>:631/printers/<PRINTER_NAME>
- Test: Lege eine Test‑PDF ab und rufe das print‑worker Script an: `./Proxmox/print-worker.sh ./test.pdf`

Dateipfade & Hinweise
- Integration mit print‑watcher: Proxmox/print-worker-watcher.sh
- Deployment: als Systemd Service (Proxmox/print-worker.service) oder Docker Container
