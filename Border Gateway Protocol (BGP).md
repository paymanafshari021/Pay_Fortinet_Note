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
+ Indicates how the route originated (IGP, EGP, or Incomplete).
+ It tells how a route was first put into BGP — in other words, where did this route come from originally?
+ The ORIGIN value is one of the early “tie-breakers” in the best-path selection process (step 5 in FortiGate’s list).

| ORIGIN Code | Name               | Meaning (in simple words)                                                                                                                                                                                  | How it usually gets set                                                                                                                | Preference (lower = better) |
| ----------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| **0**       | **IGP**            | The route was learned **inside the same AS** using an internal routing protocol (like OSPF, RIP, static, connected). This is the most trusted way.                                                         | When you redistribute a route from OSPF/static into BGP, or use the `network` command.                                                 | **Best** (wins)             |
| **1**       | **EGP**            | The route came from an old exterior protocol (EGP – the predecessor of BGP). Almost never seen today.                                                                                                      | Very rare nowadays.                                                                                                                    | Medium                      |
| **2**       | **Incomplete / ?** | Nobody really knows how this route got into BGP. Usually it was redistributed from somewhere without proper origin info (e.g., redistributed from another BGP speaker or manually created). Least trusted. | When one AS re-advertises a route received from eBGP without changing the origin, or when using `redistribute` without extra settings. | **Worst** (loses)           |

Lower values mean “better.”
### 🔹 **LOCAL_PREF** (Well-known discretionary)
+ LOCAL_PREF = Local Preference
+ It is the most powerful way to control which way traffic leaves your own network (AS).
+ Used **inside one AS** to decide the preferred exit point.
+ It is never sent to other companies/ISPs (it does not cross AS borders).
+ Higher LOCAL_PREF = better path.

Example:  
Your company (AS 65000) has **two internet connections**:
- Link A → ISP-FAST (100 Gbit/s, low latency, expensive)
- Link B → ISP-CHEAP (10 Gbit/s, higher latency, cheap backup)

You want **all outgoing traffic** to normally leave via the fast ISP, but you still keep the cheap ISP as backup.

You configure on your border routers:
```
config router bgp
    config neighbor
        edit "ISP-FAST"
            set route-map-in "SET-HIGH-LOCALPREF"
        next
        edit "ISP-CHEAP"
            set route-map-in "SET-LOW-LOCALPREF"
        next
    end
end

config route-map
    edit "SET-HIGH-LOCALPREF"
        config rule
            edit 1
                set set-local-preference 200   ← higher = better
            next
        end
    next
    edit "SET-LOW-LOCALPREF"
        config rule
            edit 1
                set set-local-preference 50    ← lower = worse
            next
        end
    next
end
```
Result inside your entire AS:
- Every router sees routes from ISP-FAST with LOCAL_PREF = 200
- Every router sees routes from ISP-CHEAP with LOCAL_PREF = 50 → **All routers automatically prefer ISP-FAST for outgoing traffic** (because 200 > 50)

If the fast link goes down, LOCAL_PREF 50 is still valid → traffic automatically switches to the backup ISP. When the fast link comes back up, traffic automatically switches back. Perfect!
#### Where LOCAL_PREF Stands in the Decision Process

In FortiGate’s best-path list (slide 343), LOCAL_PREF is **step 2** – very early and very strong:
1. Highest Weight (Cisco-only, FortiGate ignores it)
2. **Highest LOCAL_PREF** ← this one almost always decides the winner inside your AS
3. Locally originated
4. Shortest AS-PATH 
5. … and so on

That’s why network engineers say: 
**“LOCAL_PREF is the big knob for outbound traffic engineering.”**

| Attribute       | Scope                  | Direction it influences | Preference rule | Typical values               |
| --------------- | ---------------------- | ----------------------- | --------------- | ---------------------------- |
| LOCAL_PREF      | Only inside your AS    | Outbound (exit) traffic | Higher = better | 100 (default), 50–500 common |
| AS_PATH prepend | Sent to other ASes     | Inbound traffic         | Longer = worse  | —                            |
| MED             | Sent to neighboring AS | Inbound traffic (weak)  | Lower = better  | 0–4 billion                  |
### 🔹 **MED** — Multi Exit Discriminator (Optional non-transitive)
**MED** is a polite **hint** you send to **another company (another AS)** saying:  
“Hey, if you have multiple connections to my network, please come in through **this** door — it’s better for me.”
- **Lower MED = better** (preferred entry point)  
- Default MED = 0 (which is the best possible)  
- It is **optional** and **non-transitive** → it usually only works between **directly connected** ASes (you and your ISP, or you and a peer).

Think of MED as a little sign on your front doors when someone wants to visit your house (your AS):

```
Your Company (AS 65000)
┌──────────────────────┐
│   Door A (London)     │ ← You put a sign: MED = 0   (please use this one!)
│   100 Gbit/s link     │
├──────────────────────┤
│   Door B (Frankfurt)  │ ← You put a sign: MED = 100 (backup, slower link)
│   10 Gbit/s link      │
└──────────────────────┘
         ↑                ↑
     ISP’s network (AS 65001)
```

Because Door A has **lower MED (0 < 100)**, the ISP will send **all traffic destined to your company** through the fast London link — exactly what you wanted!
#### Real-World Example (The Classic Use Case)
You are a customer with **two BGP links** to the **same ISP**:
- Primary link: 100 Gbit/s in New York  
- Backup link: 10 Gbit/s in New Jersey

You want the ISP to send you traffic over the fast link whenever possible.

On your FortiGate(s), you configure:

```text
config router bgp
    config neighbor
        edit "ISP-NYC"          # primary link
            set capability-default-originate enable
        next
        edit "ISP-NJ"           # backup link
            set send-med 100    # ← higher = worse for inbound
        next
    end
end
```

Result:  
- Routes advertised over the NYC link → MED = 0 (default) → **preferred**  
- Routes advertised over the NJ link → MED = 100 → **less preferred**

The ISP sees the lower MED on the NYC path and automatically sends all your inbound traffic there. If the NYC link fails, the ISP automatically fails over to NJ.
#### Very Important Things to Know About MED

| Fact                                              | What It Means in Practice                                                             |
| ------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Only compared **between the same neighboring AS** | MED from ISP-A is **never** compared to MED from ISP-B. It only works inside one ISP. |
| Often ignored or stripped                         | Many ISPs remove or ignore MED by default. You have to ask them to honor it.          |
| Non-transitive                                    | If your ISP re-advertises your routes to someone else, the MED is usually removed.    |
| Step 6 in FortiGate’s decision list               | It’s quite late — Weight, Local_Pref, AS_PATH, Origin all win over MED.               |
| Lower = better (opposite of Local_Pref)           | Local_Pref: higher = better (outbound), MED: lower = better (inbound)                 |

#### Quick Comparison: The Big Three Traffic Engineering Tools

| Tool          | Direction it Controls | Scope                     | Preference Rule     | Who Sees It?                  |
|---------------|------------------------|---------------------------|---------------------|--------------------------------|
| **Local_Pref**   | Outbound (exit)       | Only inside your AS       | Higher = better     | Only your own routers          |
| **AS_PATH prepending** | Inbound (entry)     | Everyone on the internet  | Longer = worse      | Everyone (very strong)         |
| **MED**          | Inbound (entry)       | Usually only your direct ISP | Lower = better      | Only the neighboring AS (weak) |

## 🔹 **COMMUNITY** (Optional transitive)

**COMMUNITY** = **sticky labels (tags)** that you can stick on BGP routes.

These labels travel with the route across the internet (they are **transitive** – they survive even when the route passes through many different companies).  
Whoever receives the route can look at the labels and decide:  
“Do I want to treat this route specially?”

Think of it like putting colored stickers on letters you send:

- Red sticker = “This is customer traffic – treat it nicely!”  
- Blue sticker = “This is backup link – only use if everything else is dead.”  
- Black sticker = “Do NOT announce this prefix to anyone else!”
#### The Most Famous Communities (Used by Almost Every ISP)

| Community Value         | Meaning (what the receiver usually does)                              | Real-World Example |
|-------------------------|-----------------------------------------------------------------------|--------------------|
| **65000:666**           | “Blackhole this prefix” – drop all traffic to it (used against DDoS) | You are under attack on 203.0.113.10/32 → you ask your ISP to add this community |
| **65001:100**           | Set Local_Pref = 100 (normal customer route)                         | Default for your prefixes |
| **65001:80**            | Set Local_Pref = 80 (backup/customer route)                          | You announce your prefix via a slower backup link |
| **65001:90**            | Set Local_Pref = 90 (peer route)                                     | Routes learned from another ISP you peer with |
| **no-export** (well-known) | Do NOT send this route outside your own AS                          | You announce an internal subnet by mistake → slap no-export on it |
| **no-advertise** (well-known) | Do NOT send this route to ANY BGP neighbor (even inside your AS)  | Super private prefixes |

#### Example

You have two internet links:

- Primary ISP (AS 65001) – fast and expensive  
- Backup ISP (AS 65002) – slow and cheap

You want the whole internet to prefer your primary ISP, but keep the backup as failover.

You configure on your FortiGate:

```text
config router route-map
    edit "TAG-PRIMARY"
        config rule
            edit 1
                set set-community "65001:100"   # normal priority
            next
        end
    next
    edit "TAG-BACKUP"
        config rule
            edit 1
                set set-community "65001:80"    # lower priority
            next
        end
    next
end

config router bgp
    config neighbor
        edit "Primary-ISP"
            set route-map-out "TAG-PRIMARY"
        next
        edit "Backup-ISP"
            set route-map-out "TAG-BACKUP"
        next
    end
end
```

Result:  
- Primary ISP receives your prefixes with community 65001:100 → sets Local_Pref 100 → preferred by everyone  
- Backup ISP receives your prefixes with community 65001:80 → sets Local_Pref 80 → only used if primary disappears

This is how almost every company does **inbound traffic engineering** when they can’t use MED or AS-path prepending is too aggressive.
#### Quick Facts Table

| Feature               | COMMUNITY                                      |
|-----------------------|--------------------------------------------------|
| Type                  | Optional transitive (travels everywhere)        |
| Format                | Usually **AS:number** (e.g., 65001:80) or well-known names |
| Main use              | Tell other networks “please treat my route like this” |
| Most common actions   | Change Local_Pref, prepend AS_PATH, blackhole, no-export |
| Who decides meaning?  | The **receiver** (your ISP decides what 65001:80 actually does) |

---
# Route Selection
A router may learn **multiple possible paths** to reach the same destination.  
BGP must pick **one “best” route** (unless ECMP is enabled).

To do this, BGP compares route attributes in a **specific order**, called the **route selection process**.

Think of this as BGP’s **tie-breaker list** — it checks rule #1 first, and only moves to rule #2 if the first rule is tied, and so on.
## BGP Route Selection (Simple Explanation of Each Step)

### **1. Highest weight**
(Weight is Cisco-proprietary; FortiGate treats it like a custom value.)

- Bigger weight = better route.
- Useful when **you** want to force traffic a certain way.
## **2. Highest local preference (LOCAL_PREF)**

Used **inside one AS** to choose the best exit point. 

Higher LOCAL_PREF = more preferred.
## **3. Prefer routes that originated locally**

If the router itself created a route (for example, through `network` or redistribution), it prefers it over routes learned from neighbors.
## **4. Shortest AS path**

Routes with **fewer AS hops** are considered shorter and preferred.
## **5. Lowest origin type**

Origin types (best to worst):

1. IGP
2. EGP
3. Incomplete
## **6. Lowest MED (Multi-Exit Discriminator)**

Lower MED = “Enter my AS through this router — it's better.”
## **7. Lowest IGP metric to the NEXT_HOP**

This checks the internal metric (inside your AS) to reach the next hop router.
- Lower metric = closer = better.
## **8. Prefer EBGP routes over IBGP routes**

If all previous rules tie, routes learned from **external neighbors** (EBGP) are preferred over those learned internally (IBGP).
## **9. If ECMP is enabled: install up to 10 equal-cost routes**

ECMP = Equal Cost Multi-Path.

- If multiple routes tie on all previous rules, FortiGate can install **up to 10** of them.
- Traffic is shared across the equal routes.
## **10. Lowest router ID**

Router ID = unique identification for a BGP router.

If absolutely everything else matches, use the router with the **lowest router ID**.
### Easy Real-Life Analogy

Imagine you’re picking the best route to drive somewhere.

You evaluate:
1. Is there a route you personally prefer?
2. Which road has fewer traffic rules?
3. Which one starts closer to you?
4. Which has fewer toll booths (AS hops)?
5. Which road is officially recommended (origin)?
6. Which road has lower congestion warnings (MED)?
7. Which road is physically nearest to join (IGP metric)?
8. Prefer main highways (EBGP) over back streets (IBGP).
9. If two routes tie, use both (ECMP).
10. If still tied, pick the direction with the lower sign number.
### Quick Summary (Super Simple)

BGP compares paths in this order:
1. Highest weight
2. Highest LOCAL_PREF
3. Originated locally
4. Shortest AS_PATH
5. Lowest origin type
6. Lowest MED
7. Lowest IGP metric to next hop
8. EBGP > IBGP
9. ECMP (if enabled)
10. Lowest router ID

This ensures BGP always picks a single “best path” in a predictable way.

---
