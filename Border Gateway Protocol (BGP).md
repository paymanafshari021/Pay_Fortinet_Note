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
---
# BGP Attributes

When BGP learns multiple possible paths to a destination, it must choose **the best one**.  
To make that decision, BGP looks at **attributes** — pieces of information attached to each route.

Think of BGP attributes as **labels** on a package that help you decide the best delivery path.
## Four Types of BGP Attributes

BGP Attributes divides attributes into **four categories**:  
1. **Well-known mandatory**
2. **Well-known discretionary**
3. **Optional transitive**
4. **Optional non-transitive**
### 1. **Well-Known Mandatory Attributes**

These **must** be present in every BGP route advertisement.  
If they’re missing → the route is rejected.

In other words:
> “You must attach these labels to _every_ route.”

### Important ones:
- **AS_PATH**
- **ORIGIN**
- **NEXT_HOP**

Example of a delivery analogy:  
It’s like a package needing the **destination address**, **sender**, and **return address** — otherwise it can’t be delivered.
### 2. **Well-Known Discretionary Attributes**

These attributes are common but **not required** for every route.
- LOCAL_PREF
- ATOMIC_AGGREGATE

Example:  
It’s like optional shipping instructions:  
“Fragile” or “keep upright” — helpful, but the package can still be shipped without them.
### 3. **Optional Transitive Attributes**

These attributes may or may not be present, but if a router doesn’t understand them, it **still passes them along** to the next AS.
- COMMUNITY    
- AGGREGATOR

Think of this as:  
“I don’t know what this label means, but I’ll keep it on the package in case someone else needs it.”
### 4. **Optional Non-Transitive Attributes**

If a router doesn’t understand these, it **drops them** and does _not_ pass them to other ASes.
- MULTI_EXIT_DISC (MED)

This is like a note meant only for the local post office.  
If another post office sees it and doesn’t understand it, they simply remove it.
## Key Attributes
### 🔹 **AS_PATH** (Well-known mandatory)
Shows the list of autonomous systems the route has passed through.

Used for: **picking the shortest path**.
#### Example
Route A: AS_PATH = 64512 → 64520 → 64530  
Route B: AS_PATH = 64512 → 64599

Route B has fewer AS hops → **preferred**.
### 🔹 **NEXT_HOP** (Well-known mandatory)
The IP address of the next router to send traffic to.

Example:  
To reach 10.0.0.0/8, send traffic to **192.0.2.1**.
### 🔹 **ORIGIN** (Well-known mandatory)
Indicates how the route originated (IGP, EGP, or Incomplete).
It tells how a route was first put into BGP — in other words, where did this route come from originally?
The ORIGIN value is one of the early “tie-breakers” in the best-path selection process (step 5 in FortiGate’s list).

| ORIGIN Code | Name               | Meaning (in simple words)                                                                                                                                                                                  | How it usually gets set                                                                                                                | Preference (lower = better) |
| ----------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| **0**       | **IGP**            | The route was learned **inside the same AS** using an internal routing protocol (like OSPF, RIP, static, connected). This is the most trusted way.                                                         | When you redistribute a route from OSPF/static into BGP, or use the `network` command.                                                 | **Best** (wins)             |
| **1**       | **EGP**            | The route came from an old exterior protocol (EGP – the predecessor of BGP). Almost never seen today.                                                                                                      | Very rare nowadays.                                                                                                                    | Medium                      |
| **2**       | **Incomplete / ?** | Nobody really knows how this route got into BGP. Usually it was redistributed from somewhere without proper origin info (e.g., redistributed from another BGP speaker or manually created). Least trusted. | When one AS re-advertises a route received from eBGP without changing the origin, or when using `redistribute` without extra settings. | **Worst** (loses)           |

Lower values mean “better.”

---

## 🔹 **LOCAL_PREF** (Well-known discretionary)

Used **inside one AS** to decide the preferred exit point.

Higher LOCAL_PREF = better path.

Example:  
Two paths to the internet:

- Path A: LOCAL_PREF 200
    
- Path B: LOCAL_PREF 100
    

Routers choose **Path A**.

---

## 🔹 **MED** — Multi Exit Discriminator (Optional non-transitive)

Used between ASes to say:

> “Please enter my AS through _this_ router.”

Lower MED = preferred.

Example:  
ISP advertises two entry points:

- R1 MED = 20
    
- R2 MED = 50
    

Neighbors prefer **R1**.

---

## 🔹 **COMMUNITY** (Optional transitive)

A tagging system used to mark routes for special handling.

Example:  
A route marked with **no-export** should not be sent outside the AS.

COMMUNITY tags make policies easier to manage.

---

# ⭐ How BGP Uses Attributes

BGP compares attributes **in a specific order** to pick the best route.  
The PDF lists the order (weight, local-pref, as-path, etc.) but the main idea is:

> Attributes guide BGP’s decision on the “best” path.

Without attributes, BGP wouldn’t know which path to prefer.

---

# ✔ Simple Example Putting It All Together

### Router R1 receives two routes to 10.0.0.0/8:

**Route A:**

- LOCAL_PREF = 200
    
- AS_PATH = 64510 → 64520
    
- MED = 40
    

**Route B:**

- LOCAL_PREF = 100
    
- AS_PATH = 64510
    
- MED = 10
    

### Which one wins?

1. Compare LOCAL_PREF → Route A wins (200 > 100)  
    → **Decision stops here** (higher priority than AS_PATH or MED)
    

Result: **Route A** becomes the best path.

---

# ✔ Quick Summary (Super Simple)

- BGP attributes are **labels** used to pick the best path.
    
- Attributes fall into **four types**:
    
    - Required everywhere
        
    - Optional but common
        
    - Optional and passed along
        
    - Optional and dropped
        
- Important attributes include **AS_PATH**, **NEXT_HOP**, **LOCAL_PREF**, **ORIGIN**, **MED**, and **COMMUNITY**.
    
- BGP checks attributes in a priority order to decide the best route.
    

---

If you’d like, I can also explain **Route Selection**, **AS Types**, or **Prefix Lists** in the same clear style!

