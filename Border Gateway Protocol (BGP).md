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
### **The Problem Route Reflectors Solve**
Inside one autonomous system (AS), routers use **iBGP** to exchange BGP routes.
But iBGP has a rule called **split horizon**:
> **An iBGP router cannot pass along a route it learned from another iBGP router.**  
> (Otherwise, routing loops could happen.)
#### What this means:
Every iBGP router must directly peer with every other iBGP router.
This is called **full mesh**.
#### Why this is a problem:
If you have many routers, the number of sessions grows very fast.
Example:
- With 3 routers → 3 connections
- With 10 routers → 45 connections
- With 50 routers → 1225 connections
This becomes hard to manage.
### **What Route Reflectors Do**
A **Route Reflector (RR)** is a special iBGP router that _breaks_ the full-mesh requirement.  
It is allowed to **forward iBGP-learned routes to other iBGP routers.**
> Think of the RR as a “post office” for BGP routes inside the AS.

Instead of every router talking to every other router,  
all routers talk only to the RR.
### **Route Reflector Clients**
Routers that connect to, and rely on, the RR are called **clients**.
#### How it works:
- Clients send all their BGP updates to the RR.
- The RR reflects those routes to:
    - other clients
    - other RRs
    - border routers
So clients do NOT have to peer with each other.


---
## 4. **Route Reflector Clusters**
A **cluster** = one RR + its clients.  
Large networks may have multiple clusters, and multiple RRs.

This keeps things organized and prevents single points of failure.

---

# ▶ Simple Example

### 🏢 Imagine a company network with 5 routers:

```
R1 — core router
R2
R3
R4
R5
```

### Without RRs:

- Every router needs an iBGP session with every other router.
    
- 5 routers → 10 iBGP sessions.
    

### With a Route Reflector:

Let **R1** be the RR.  
All others (R2–R5) are clients.

```
R2 →|
R3 →|→   R1 (RR)
R4 →|
R5 →|
```

**Only 4 sessions**, and everyone learns every route.

---

# ▶ Multi-Cluster Example

If the AS grows, you might divide routers into clusters:

```
Cluster 1:
   RR1
   R2, R3

Cluster 2:
   RR2
   R4, R5

RR1 ↔ RR2
```

- Routers inside a cluster talk only to their RR.
    
- RRs share information between clusters.
    

This scales much better in large networks.

---

# ⭐ Why Route Reflectors Are Useful

✔ Reduce the number of iBGP sessions  
✔ Easier configuration  
✔ Better scalability in large AS networks  
✔ No need for full mesh

---

# ✔ Quick Summary in Plain English

- iBGP normally requires every router to directly peer with every other one.
    
- This doesn’t scale well.
    
- A Route Reflector acts like a “hub” for BGP routes.
    
- Clients talk only to the RR, not to each other.
    
- The AS can be divided into clusters.
    
- This keeps the network simpler and easier to manage.
    

---

If you want, I can also explain RIBs, AS types, attributes, or routing selection from the same PDF in the same simple style!
---
