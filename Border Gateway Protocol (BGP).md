# Border Gateway Protocol (BGP)
#bgp #bfd #Routing-objects
+ Border Gateway Protocol (BGP): https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/750736/bgp
+ Bidirectional Forwarding Detection (BFD): https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/771813/bfd
+ Routing objects: https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/654952/routing-objects
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
### **Route Reflector Clusters**
A **cluster** = one RR + its clients.  
Large networks may have multiple clusters, and multiple RRs.

This keeps things organized and prevents single points of failure.
#### Simple Example
##### Imagine a company network with 5 routers:

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

### Multi-Cluster Example
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
# RIBs (Routing Information Bases)

A BGP router stores route information in **three logical tables** called **RIBs**.  
Think of them as _three inboxes_ that process routing information step by step.

The three RIBs are:
1. **RIB-in**
2. **Local RIB**
3. **RIB-out**

Let's break them down using simple words and examples.
## 1. **RIB-in — “Everything I heard”**

The **RIB-in** contains **all the routes a router receives** from its BGP neighbors _before_ any filtering or decisions. It is the raw inbox.

> The RIB-in contains unprocessed routing information learned from inbound update messages.

### Example:
Router R1 receives these routes from its neighbor:
- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.1.0/24

Even if R1 plans to ignore some of them, **all three go into RIB-in** first.
## 2. **Local RIB — “What I decide to keep”**

The **Local RIB** contains routes the router has **accepted** after applying:
- filters,
- route maps,
- policies.

This is the router’s **“cleaned-up” inbox**.  
Only the routes the router actually wants are kept.

> Local RIB contains routing information that the BGP speaker selects after applying its local policies.

### Example:
Continuing from R1:
RIB-in had 10.0.0.0/8, 172.16.0.0/12, 192.168.1.0/24.

A filter says: “Block 192.168.1.0/24.”

So the **Local RIB** ends up with:

- 10.0.0.0/8
- 172.16.0.0/12

Only the accepted routes remain.
## 3. **RIB-out — “What I choose to send out”**

The **RIB-out** is the list of routes the router decides to advertise to its BGP neighbors.

Before sending routes out, the router may apply:
- outbound filters,
- route maps,
- attribute changes.
> RIB-out contains the routing information selected to advertise to peers.

### Example:
R1’s Local RIB has:

- 10.0.0.0/8
- 172.16.0.0/12

But R1 wants to advertise _only_ 10.0.0.0/8 to a neighbor.

Thus, the **RIB-out** contains:

- 10.0.0.0/8

This is like your **“outbox”** — messages you choose to send.
### Putting It All Together (Simple Flow)
```
BGP RX → RIB-in → Inbound Filter → Local RIB → Outbound Filter → RIB-out → BGP TX
           ^                                          ^
       received routes                       routes to advertise
```
1. **RIB-in:** “Everything I heard.”
2. **Local RIB:** “What I kept after filtering.”
3. **RIB-out:** “What I decide to tell others.”


