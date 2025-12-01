# Check-List
### Checkliste für die Installation eines FortiAnalyzer Hardware-Appliances

Hier ist eine vollständige, praxisorientierte Checkliste für die Hardware-Installation eines FortiAnalyzer (basierend auf den aktuellen Fortinet QuickStart Guides und Administration Guides (Stand 2025, Version 7.x). Die Schritte sind bei fast allen Modellen (z. B. FAZ-200F/300F/1000F/2000E/3000G/3700G usw.) identisch oder sehr ähnlich.

#### 1. Vorbereitung / Planung (vor dem Auspacken)
- [ ] Genauer Modellname und Serie bekannt? → QuickStart Guide für dein exaktes Modell von https://docs.fortinet.com herunterladen (sehr wichtig, weil Rack-Ohren, HDD/RAID und Ports modellabhängig sind).
- [ ] FortiCare Support-Vertrag aktiv und Seriennummer registriert? (benötigt für Lizenz-Upload/Firmware-Updates/FortiGuard).
- [ ] Lizenzdatei (.lic) bereits heruntergeladen oder bereit (aus FortiCare Portal)?
- [ ] Netzwerkplanung erledigt:
  - Management-IP + Subnetz + Gateway + DNS (besser separates Management-VLAN).
  - Welcher Port wird für Management benutzt (meist port1)?
  - Welche Ports für Log-Empfang (Standard: TCP/514 für OFTP, evtl. andere).
- [ ] Umgebungsanforderungen geprüft:
  - Rack-Platz (1U/2U/4U je nach Modell).
  - Strom: Anzahl Netzteile, Spannung, redundante Versorgung vorhanden?
  - Temperatur 0–40 °C, Luftfeuchtigkeit 5–95 % (nicht kondensierend).
  - Ausreichende Luftzirkulation (vorne kalt, hinten warm → mind. 10–15 cm Abstand).
- [ ] Console-Kabel (RJ45–DB9) und Laptop mit Terminal-Programm (PuTTY/Tera Term) bereit?
- [ ] Laptop/PC im gleichen Subnetz wie Default-IP (192.168.1.0/24) für Erstzugriff.

#### 2. Physische Installation
- [ ] Lieferumfang prüfen:
  - Appliance, Stromkabel (meist 2×), Console-Kabel (RJ45), Rack-Ohren/Schienen, evtl. HDD-Trays (bei höheren Modellen), QuickStart Guide.
- [ ] Gerät auf Transportschäden kontrollieren → bei Schaden sofort Fortinet melden.
- [ ] ESD-Schutz beachten (Erdungsband oder berühre geerdetes Metall vor dem Anfassen).
- [ ] In Rack einbauen:
  - Rack-Ohren/Schienen montieren (Anleitung im QuickStart Guide beachten – bei großen Modellen zu zweit arbeiten!).
  - Gerät sicher verschrauben.
- [ ] Stromkabel anschließen (beide Netzteile bei redundantem Modell!).
- [ ] Netzwerkkabel:
  - Mindestens port1 (Management) anschließen.
  - Optional weitere Ports für HA oder Log-Traffic.
- [ ] Console-Kabel anschließen (falls du CLI bevorzugst oder GUI nicht erreichbar ist).

#### 3. Ersteinschalten & grundlegende Konfiguration
- [ ] Gerät einschalten → Lüfter starten laut, dann leiser werden (normal).
- [ ] Zugriff herstellen:
  - Default-IP: https://192.168.1.99 (HTTPS!)
  - User: admin
  - Password: leer (sofort ändern!)
  - Oder per Console (9600 8N1).
- [ ] Setup-Wizard durchlaufen (erscheint automatisch beim ersten Login):
  - Admin-Passwort setzen (stark!).
  - Hostname vergeben.
  - Zeitzone & NTP-Server einstellen.
  - Management-IP konfigurieren (port1 oder gewünschter Port).
  - DNS & Gateway eintragen.
- [ ] Lizenz aktivieren:
  - Online (wenn Internet da) → automatisch.
  - Oder Offline → .lic-Datei hochladen.
  - Nach Upload Neustart erforderlich.
- [ ] Firmware-Update prüfen:
  - Aktuellste stabile Version von support.fortinet.com herunterladen und upgraden (wenn nicht schon die neueste drauf ist).
  - Immer über GUI → System Settings → Dashboard → License Information → Update.

#### 4. Nach der Grundkonfiguration (wichtige Einstellungen
- [ ] RAID konfigurieren (bei Modellen mit mehreren Festplatten, z. B. 3700G):
  - Unter System Settings → RAID Management → gewünschtes RAID-Level wählen (meist RAID 1/5/10 empfohlen).
- [ ] ADOMs aktivieren (wenn du mehr als ein FortiGate oder unterschiedliche Kunden hast):
  - System Settings → Advanced → ADOMs enable.
- [ ] Log-Empfang aktivieren:
  - Global → System Settings → Log Settings → Log reception aktivieren (TCP/OFTP auf TCP/514).
- [ ] FortiGates/FortiSwitches/etc. hinzufügen:
  - Device Manager → Add Device → Seriennummer oder IP eingeben → autorisieren.
  - Auf den FortiGates: Log → Log Settings → Remote Logging zu FortiAnalyzer-IP aktivieren.
- [ ] Sicherheits-Hardening:
  - Admin-Zugriff auf vertrauenswürdige IPs beschränken.
  - HTTPS & SSH only, HTTP/ Telnet deaktivieren.
  - Trusted Hosts für Admin setzen.
  - Zwei-Faktor-Authentifizierung für Admins aktivieren.
- [ ] Backup der Konfiguration anlegen (System Settings → Configuration → Backup).

#### 5. Optional / häufige Stolperfallen
- [ ] HA-Cluster geplant? → Dann zweiten Knoten identisch konfigurieren und HA einrichten (vor Log-Empfang!).
- [ ] Big-Data-Modelle (z. B. 4500G) haben separate Security Event Manager-Appliances → extra beachten.
- [ ] Internetzugang für FortiGuard-Updates vorhanden? (empfohlen, sonst Offline-Updates).
- [ ] Nach Lizenz-Upload und Firmware-Update immer Neustart und prüfen, ob alles läuft (Dashboard → System Resources).

Wenn du mir dein exaktes Modell sagst (z. B. FAZ-1000F, FAZ-2000E usw.), kann ich dir den direkten Link zum passenden QuickStart Guide geben und modell-spezifische Besonderheiten ergänzen (z. B. wie viele HDDs, welche Rack-Schienen).
