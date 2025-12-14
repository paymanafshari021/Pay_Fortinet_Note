#sd-wan #overlay #ipsec 
# Overlay Fundamentals
## IPsec in SD-WAN Overlays
IPsec is a suite of protocols providing authentication and encryption for traffic between peers. It is the most widely used tunneling protocol for SD-WAN overlays due to its strong security features.
### Key Protocols in IPsec Suite
- **IKE (Internet Key Exchange)**  
  - Handles handshake, tunnel maintenance, and teardown  
  - Default port: UDP 500
- **ESP (Encapsulating Security Payload)**  
  - Provides data integrity, confidentiality (encryption), and authentication  
  - Uses IP protocol number 50 (no TCP/UDP ports)
### NAT Traversal (NAT-T) Issue and Solution
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
=======
    - ESP packets are encapsulated in **UDP 4500** (UDP encapsulation) 
This allows IPsec tunnels to function reliably in NAT environments. 
---
## Internet Key Exchange (IKE) and IPsec SAs
IKE is essential for establishing an **IPsec tunnel** by negotiating the **private keys** and **encryption** used by the communicating devices.
### **Security Associations (SAs)**

- **Function:** An SA is a **bundle of algorithms and parameters** that defines how data is encrypted and authenticated within the tunnel.
- **Establishment:** Both devices must agree on the SAs and secret keys, a process facilitated by IKE.
- **Traffic Flow:** Normal two-way traffic requires **a pair of SAs**—one for each direction.
- **Tunnel Requirement:** The tunnel will **not be established** if both sides cannot agree on the security rules and identity verification.
- **Lifetime:** SAs have a **lifetime** and must be renegotiated by the peers when they expire.
### **IKE Phases and SA Types**

IKE uses two distinct phases to negotiate different SA types:

- **Phase 1 Negotiation (IKE SA):**
    - Negotiates the **IKE SA**.
    - Used to set up a **secure channel** for negotiating Phase 2.
    - IKE_SA_INT and IKE_SA_AUTH exchanges (IKEv2) -> Outcome: IKE SA
- **Phase 2 Negotiation (IPsec SA):**
    - Negotiates the **IPsec SA**.
    - Used directly for **encrypting and decrypting** the data transmitted through the tunnel.
    - CREATE_CHILD_SA exchange (IKEv2) -> Outcome: IPsec SA
### **IKE Versions**
- **IKEv1:** The legacy version; it is **deprecated** and should be avoided for new deployments.
- **IKEv2:** The recommended version for new deployments.
    - It supports **more features** and is easier to operate.
    - Exclusively supports the **network ID feature**, which allows establishing **multiple tunnels** between the same gateways (useful for failover/ADVPN).

This detailed breakdown highlights why **IKEv2** is superior to the legacy **IKEv1** protocol.
### **Quick Summary: Why Switch?**
Think of **IKEv1** as a legacy dial-up connection: it works, but it's chatty, slower to set up, and drops easily if you move around. **IKEv2** is like modern broadband: it connects faster with fewer messages, stays connected even if you switch networks (like Wi-Fi to 4G), and has built-in tools to fix itself if the connection stalls.
### **Feature Comparison Table**

| **Feature**        | **IKEv1 (Legacy)**                                                                                       | **IKEv2 (Modern Standard)**                                                                 |
| ------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Setup Speed**    | **Slow:** Requires 6–9 messages to build a tunnel.                                                       | **Fast:** Requires only 4 messages to build a tunnel.                                       |
| **Reliability**    | **Low:** Extensions like _Dead Peer Detection (DPD)_ are add-ons and often incompatible between vendors. | **High:** DPD and Keep-alives are built into the protocol standard.                         |
| **Mobility**       | **None:** If your IP changes (e.g., switching Wi-Fi), the tunnel drops and must restart.                 | **MOBIKE Support:** The tunnel stays up even if the device’s IP address changes.            |
| **Authentication** | **Symmetric:** Both sides usually need the same auth method (e.g., PSK on both).                         | **Asymmetric:** One side can use a certificate while the other uses a Pre-Shared Key (PSK). |
| **NAT Traversal**  | **Add-on:** "Bolted on" later; often tricky to configure through firewalls.                              | **Native:** Built-in support makes traversing firewalls/NAT much smoother.                  |
### **Deep Dive: Key Technical Differences**

#### **1. Simplified Message Exchange (Efficiency)**
The most significant technical difference is how they establish a connection. IKEv1 is "chatty," requiring multiple back-and-forth steps. IKEv2 streamlines this drastically.
- **IKEv1 (6–9 Messages):**
    - **Phase 1:** Negotiates the secure channel. In "Main Mode," this takes **6 messages**.
    - **Phase 2:** Negotiates the actual IPsec keys. This takes **3 messages**.
- **IKEv2 (4 Messages):**
    - It combines Phase 1 and Phase 2 into a fluid process.
    - **IKE_SA_INIT (2 messages):** Negotiates crypto algorithms and exchanges random numbers (nonces).
    - **IKE_AUTH (2 messages):** Authenticates identities and sets up the first IPsec tunnel immediately.
#### **2. Reliability & "Self-Healing"**
- **Sequence Numbers:** IKEv2 assigns a sequence number to every message and expects a response. If a packet is lost, IKEv2 knows exactly which one to re-transmit. IKEv1 simply waits for a timeout, which is slower and less reliable.
- **Dead Peer Detection (DPD):** In IKEv1, checking if the other device was still "alive" was a non-standard add-on. In IKEv2, it is a core part of the protocol, ensuring that "zombie" tunnels don't hang around wasting resources.
#### **3. Mobility (MOBIKE)**
IKEv2 supports **MOBIKE** (Mobility and Multihoming Protocol). This is critical for modern SD-WAN and mobile users.
- **Scenario:** A user creates a VPN tunnel on office Wi-Fi but then walks out the door and switches to 5G LTE.
- **IKEv1:** The tunnel breaks immediately because the source IP changed. The user must re-login.
- **IKEv2:** The protocol updates the IP address in the background without dropping the secure tunnel. The connection persists seamlessly.
#### **4. The "Network ID" Feature (SD-WAN Context)**
Fortinet study guide specifically mentioned the **Network ID**. This is a critical feature for SD-WAN architectures (like ADVPN).
- **Limit in IKEv1:** It generally identifies tunnels strictly by IP address pairs. If you have multiple services running between the same two gateways, IKEv1 struggles to distinguish them.
- **Advantage in IKEv2:** It can tag traffic with a **Network ID**. This allows you to build **multiple distinct tunnels** between the exact same local and remote IP addresses. This is mandatory for complex failover scenarios where you might want one tunnel for "Voice Traffic" and a parallel tunnel for "Data Traffic" between the same two sites.

---
## SD-WAN Hub-and-Spoke IPsec Overlay Topology

#### Topology Design
- Standard deployment uses **IPsec overlays** in a **hub-and-spoke** model
- Hub: Located in central office/data center; acts as **dial-up IPsec server**
- Spokes (branches/edges): Distributed remote sites; connect as dial-up clients
- Each underlay interface on the hub has a **separate dial-up server** configuration → creates **point-to-multipoint (PTMP)** overlays
- Spokes typically connect to **all overlays** for full-mesh reachability and redundancy
#### Performance Recommendations
- Fortinet best practice: **Group overlays by ISP** (same-ISP overlays preferred over cross-ISP)
- Reason: Lower latency and better performance when tunnels stay within the same ISP
#### Traffic Flow & SD-WAN Placement
- Majority of traffic initiated **spoke → hub** (branches accessing central workloads)
- SD-WAN therefore primarily required on **spokes**
- SD-WAN may also be needed on the **hub** when hub-initiated traffic (hub → spoke) is significant
#### Routing
- Preferred protocol: **Dynamic routing** (usually **BGP**) over the overlays
- Purpose: Exchange prefixes across all overlays; enables scalability and supports **ADVPN** with SD-WAN
- Static routing possible but not recommended for larger deployments

Goal: Full prefix exchange and multi-path connectivity across all available overlays.

---
## Other Common Overlay Designs

#### 1. Hub-and-Spoke with Static Tunnels
- Each branch/site establishes static IPsec (or GRE, etc.) tunnels only to the hub(s)
- Branches normally do **not** have direct tunnels to each other

**Advantages:**
- High stability and predictability
- Centralized policy enforcement at the hub
- Easier log reading and troubleshooting (all traffic flows through the hub)
- Simple scalability from the branch perspective (each branch only manages 1–2 tunnels)

**Disadvantages:**
- Suboptimal routing (hairpinning through the hub even for branch-to-branch traffic)
- Hub can become a bandwidth and processing bottleneck
- Single point of failure unless multiple/redundant hubs are deployed

#### 2. Full-Mesh Static Tunnels
- Every site establishes static tunnels directly to every other site
- Creates a complete any-to-any topology

**Advantages:**
- Optimal routing / lowest latency (direct site-to-site paths)
- No hub bottleneck
- High stability (static configuration, no dynamic protocol issues)

**Disadvantages:**
- Poor scalability – number of tunnels grows exponentially
- Formula for number of bidirectional VPN tunnels required:
  
  **N × (N-1) / 2**  
  where N = number of sites

  Example:
  - 5 sites  → 10 tunnels
  - 10 sites → 45 tunnels
  - 20 sites → 190 tunnels
  - 50 sites → 1,225 tunnels

- Dramatically increased planning, configuration, and operational overhead
- Very difficult to manage and troubleshoot at scale

### Quick Comparison Table

| Design                  | Scalability | Optimal Routing | Ease of Management | Typical Use Case                  |
|-------------------------|-------------|-----------------|--------------------|-----------------------------------|
| Hub-and-Spoke (static)  | Good        | No (hairpin)    | Easy               | Small to medium networks, centralized services |
| Full-Mesh (static)      | Very Poor   | Yes             | Very Hard          | Very small networks (< ~8–10 sites) |
| Dynamic (e.g., SD-WAN, DMVPN, ADVPN) | Excellent   | Yes (with dynamic spokes) | Moderate       | Medium to large/very large networks |

In modern deployments, full-mesh static tunnels are rarely used beyond a handful of sites. Most organizations today prefer SD-WAN or dynamic overlay solutions (like Fortinet ADVPN, Cisco DMVPN, Viptela, Velocloud, etc.) that combine the stability of static hub tunnels with on-demand/direct spoke-to-spoke tunnels to get the best of both worlds.

---
## SD-WAN Overlay Orchestrator Topology
- Defines a **hub-and-spoke** SD-WAN topology
- Secures overlay links using **IPsec**
- Supports up to **four hubs**
### Overlay Routing Protocol Options (IBGP)
The orchestrator provides two IBGP topologies for branch-to-hub routing:
- **BGP per overlay**
  - FortiGate establishes a separate BGP neighbor relationship **for each overlay**
  - Widely deployed in the field
  - Suitable for **small-to-medium-sized topologies**, with or without ADVPN
- **BGP on loopback**
  - FortiGate establishes a **single BGP neighbor relationship** with each device (using loopback interfaces)
  - Scales better as the number of devices and links increases
  - Used in the Fortinet lab exercises
