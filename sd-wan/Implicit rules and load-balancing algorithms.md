# Implicit rules and load-balancing algorithms

### 🌐 1. The “Implicit Rule”

If you don’t create any specific SD-WAN rules for a session (like saying “Office 365 traffic should use WAN1”), FortiGate uses the **implicit rule** — a built-in default behavior.

When this happens, FortiGate looks up the **routing table (FIB)** to decide which path to use.

---

### ⚖️ 2. ECMP — Equal-Cost Multi-Path

If multiple routes lead to the same destination and have the same cost, FortiGate uses **ECMP (Equal-Cost Multi-Path)** routing.

Think of it like:

> You have two roads from your office to the same store — both take the same time.  
> FortiGate will send some cars (traffic) on Road A and some on Road B — that’s **load balancing**.

---

### 🔁 3. Load Balancing Algorithms

FortiGate has several ways (“algorithms”) to decide **how to share traffic** between multiple equal-cost paths.

Let’s look at each one in plain English with examples:

---

#### 🧍 **Source IP (default)**

- Traffic from the same **source IP address** always uses the same WAN link.
    

**Example:**

- Your laptop (192.168.1.10) always goes out through **WAN1**.
    
- Another laptop (192.168.1.11) might go through **WAN2**.
    

👉 Keeps each user’s sessions consistent, useful for avoiding session breaks.

---

#### ⚙️ **Sessions (Weight-based)**

- FortiGate balances **the number of sessions** across links.
    
- The **weight** controls how much traffic goes to each link.
    

**Example:**

- WAN1 weight = 2, WAN2 weight = 1.  
    → WAN1 gets about twice as many sessions as WAN2.
    

---

#### 📈 **Spillover (Usage-based)**

- FortiGate sends all traffic through the **first link** until its bandwidth limit is reached.
    
- Then, it “spills over” new sessions to the next link.
    

**Example:**

- WAN1 handles up to 50 Mbps.  
    When it hits 50 Mbps, new traffic moves to WAN2 automatically.
    

---

#### 🔢 **Source-Destination IP**

- Traffic between the same pair of source and destination IPs always uses the same link.
    

**Example:**

- Laptop A (192.168.1.10) → Google (8.8.8.8) → always uses WAN1.
    
- Laptop A → YouTube (142.250.x.x) → could use WAN2.
    

👉 This helps keep communication between two devices consistent.

---

#### 📊 **Volume (Measured-volume-based)**

- FortiGate tracks the **amount of data (bytes)** sent on each link.
    
- It balances based on both **traffic volume** and **weights**.
    

**Example:**

- WAN1 (weight 2) and WAN2 (weight 1).  
    FortiGate tries to send **about twice as much total data** through WAN1 as WAN2, even if sessions vary in size.
    

---

### ✅ Summary Table

|Algorithm|Key Idea|Example Use Case|
|---|---|---|
|**Source IP**|Each user/device uses the same link|Stability for users|
|**Sessions (Weight)**|Balance # of sessions by weight|Even traffic spread|
|**Spillover**|Fill one link, then overflow to next|Prioritize cheaper link first|
|**Source-Dest IP**|Same pair of IPs use same link|Keeps consistent path between endpoints|
|**Volume**|Balance by total bytes sent|Optimize throughput efficiency|
