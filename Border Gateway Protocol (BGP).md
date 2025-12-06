# Border Gateway Protocol (BGP)
## BGP Overview
- Border Gateway Protocol (BGP) is a standardized **Exterior Gateway Protocol (EGP)**. 
- Differs from interior protocols like **RIP, OSPF, and EIGRP**, which are **Interior Gateway Protocols (IGPs)**.
- **BGPv4** is the current version in widespread use.
### Purpose and Function
- Designed for routing **between Autonomous Systems (ASes)**, not within them.
- Operates as a **Path Vector protocol**, using:
    - **AS Path length** as a primary factor.
    - Additional BGP **attributes** instead of traditional IGP metrics (e.g., distance or cost).
- Forms the backbone routing protocol of the **Internet**, which consists of interconnected ASes.
### Autonomous System Numbers (ASNs)
- ASN is a **16-bit identifier**, ranging from **1–65535**.
- **64512–65535** are reserved for **private/internal use**.
### Transport
- BGP uses **TCP port 179** to reliably exchange routing information.
---
## BGP Components
### BGP Speaker or Peer
 - A router that both sends or receives Border Gateway Protocol (BGP) routing information.
### BGP Session
- The communication link established between two BGP peers for exchanging routing data.
---
## BGP Session Types
### Internal BGP (iBGP)
- Formed between routers **within the same Autonomous System (AS)** or **within the same BGP confederation**.
- Routes learned via iBGP are installed in the RIB with an **administrative distance (AD) of 200**.
### External BGP (eBGP)
- Formed between routers **in different Autonomous Systems**.
- Routes learned via eBGP are installed in the RIB with an **administrative distance (AD) of 20**.
---
## AS Types
### **Stub AS**
- An AS that only handles **local traffic** and has **a single connection** to another AS.
- **Characteristics:**
    - Only one exit point to the Internet or another AS.
    - Routes traffic that originates and terminates **within the AS**.
### **Multihomed AS**
- An AS that handles **local traffic only**, but has **multiple connections** to different ASes.
- **Characteristics:**
    - Multiple exit points for redundancy or load balancing.
    - Still does not provide transit for other ASes.
### **Transit AS**
- An AS that handles **local traffic** and also **transit traffic** (traffic passing through to other ASes).
- **Characteristics:**
    - Provides connectivity between other ASes.
    - Can originate, terminate, and forward traffic.
---
## Split Horizon

- **iBGP Split-Horizon Rule:** iBGP routers do **not** advertise routes learned from one iBGP peer to another.
- **Prevents routing loops** within the autonomous system (AS).
- To ensure all routers learn all prefixes inside the AS, a **full iBGP mesh** must be created among all iBGP routers.

---
## Route Reflectors
### **The Problem: Full Mesh Peering**
- Every IBGP router must form a direct connection (a BGP session) with **every other IBGP router**.
- The number of connections grows _fast_ as you add routers.
- If you have _N_ routers, you need **N × (N-1) / 2** connections.
- This becomes hard to manage and wastes resources.
### **The Solution: Route Reflectors (RRs)**
A **Route Reflector (RR)** is a router that acts like a _central hub_ for IBGP information.  
Instead of every router talking to every other router, they all talk to the RR.
### **How RRs reduce connections**
- Each router only needs to connect to the RR (or a small set of RRs).
- The RR receives route updates from one router and “reflects” (forwards) them to others.
### **Example**
Imagine you have **5 routers** again, but now Router A is the **Route Reflector**.
Instead of 10 connections:
- Router A connects to B, C, D, E
- Routers B, C, D, and E only connect to A — **not to each other**
Now you have **only 4 connections** instead of 10.
### **What the RR does**
If Router B learns a new route:
- B tells the RR (A)
- The RR tells C, D, and E
- Everyone stays updated without being directly connected
### **Why this helps**
- **Greatly reduces the number of IBGP sessions**
- **Makes large networks easier to manage**
- **Still ensures that routing information is shared everywhere inside the AS**
---
