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