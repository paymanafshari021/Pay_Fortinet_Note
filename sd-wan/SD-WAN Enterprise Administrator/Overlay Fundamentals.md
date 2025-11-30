#sd-wan #overlay #ipsec 
# Overlay Fundamentals
## IPsec in SD-WAN Overlays
IPsec is a suite of protocols providing authentication and encryption for traffic between peers. It is the most widely used tunneling protocol for SD-WAN overlays due to its strong security features.
#### Key Protocols in IPsec Suite
- **IKE (Internet Key Exchange)**  
  - Handles handshake, tunnel maintenance, and teardown  
  - Default port: UDP 500
- **ESP (Encapsulating Security Payload)**  
  - Provides data integrity, confidentiality (encryption), and authentication  
  - Uses IP protocol number 50 (no TCP/UDP ports)
#### NAT Traversal (NAT-T) Issue and Solution
- **Problem**: ESP cannot traverse NAT devices easily because it lacks port numbers to differentiate tunnels.
- **Solution**: Enable NAT-T on both peers  
  - Peers automatically detect NAT along the path  
  - If NAT is detected:  
    - IKE switches from UDP 500 to **UDP 4500**  
    - ESP packets are encapsulated in **UDP 4500** (UDP encapsulation)  
This allows IPsec tunnels to function reliably in NAT environments. 
## IKE-Protokoll und IPsec-Tunnels

IKE (Internet Key Exchange) verhandelt die privaten Schlüssel und Verschlüsselungsverfahren, die für den Aufbau eines IPsec-Tunnels erforderlich sind. Beide Tunnel-Enden müssen sich auf identische Security Associations (SAs) und geheime Schlüssel einigen, sonst wird kein Tunnel etabliert.
### Security Associations (SAs)
- Eine SA ist ein Bündel aus Algorithmen und Parametern für Verschlüsselung und Authentifizierung.
- Bei bidirektionalem Verkehr werden immer **zwei SAs** verwendet — je eine pro Richtung.
- Beide Peers müssen exakt dieselben Sicherheitsregeln akzeptieren.
- SAs haben eine Lebensdauer und müssen nach Ablauf durch die Peers neu verhandelt werden.
### IKE-Phasen
- **Phase 1** → Verhandelt die **IKE SA**: stellt einen authentisierten und verschlüsselten Kanal bereit, über den Phase 2 sicher ablaufen kann.
- **Phase 2** → Verhandelt die **IPsec SA(s)**: diese werden für die eigentliche Verschlüsselung und Entschlüsselung des Nutzdatenverkehrs verwendet.
### IKE-Versionen
- **IKEv1**: Ältere, weit verbreitete, aber veraltete Version (deprecated). Sollte in neuen Deployments nicht mehr verwendet werden.
- **IKEv2**: Aktuelle, empfohlene Version. Bietet mehr Funktionen, einfachere Bedienung und exklusive Features wie **Network ID**, das mehrere Tunnel zwischen denselben Gateways ermöglicht — besonders wichtig für SD-WAN-Failover und ADVPN-Szenarien.