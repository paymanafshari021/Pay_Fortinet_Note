# Implicit Rules and Load-Balancing Accuracy

### **SD-WAN Load Balancing Algorithms**

FortiGate SD-WAN uses **rules** to decide how to send traffic across multiple links (connections). There are a few different ways it can balance traffic:

- **Sessions**
- **Spillover**
- **Volume**

Each method works a bit differently.

---

### **Sessions Algorithm**

- **What it does:** Distributes **sessions** (like a single TCP/UDP conversation) across different links based on a **weight** you assign.
- **Important point:** Weight ≠ actual bandwidth used.

**Example:**

- You have 2 links, Link A and Link B. Both have weight = 1.
- 10 ICMP (ping) sessions go to Link A.
- 1 FTP session goes to Link B.

Even though Link A has **more sessions**, the FTP session on Link B may use **more bandwidth** than the 10 ICMP sessions on Link A. So weights only control **session distribution**, not total data usage.

---

### **Spillover and Volume Algorithms**

- **Spillover:** Sends traffic to the main link until a limit (threshold) is reached, then uses other links.
- **Volume:** Distributes traffic based on assigned volume ratios.

**Key points:**

- Even if traffic goes over the threshold, it’s okay.
- Existing sessions **don’t get moved**; they stay on the link until they finish or are forced to reroute.

**Example:**

- Link A has a spillover limit of 100 Mbps. Link B is backup.
- A session starts on Link A and suddenly uses 120 Mbps. That’s fine—it **stays on Link A** until it ends. New sessions may go to Link B if the limit is exceeded.

---

### **Dynamic Routing vs. Static Routing**

- If you’re using **dynamic routing** with ECMP (Equal-Cost Multi-Path), FortiGate will **split sessions equally** across links.
- Your weight or load-balancing settings only really matter for **static ECMP routes**.

**Example:**

- Two links with equal cost in dynamic routing. Even if you set different weights, FortiGate will just split sessions evenly.

---

### ✅ **Key Takeaways**

1. **Sessions = per session distribution, not bandwidth**.
2. **Spillover/Volume = thresholds are flexible; sessions stay put until they expire**.
3. **Long-lived sessions can use a lot of bandwidth and aren’t moved automatically**.
4. **Dynamic routing may override your load-balancing settings**; static routes respect them.