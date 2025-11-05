### 🧠 **Main idea**

This section explains what happens in **FortiGate SD-WAN** when a network link becomes **bad or unusable**, and how FortiGate reacts to keep traffic flowing smoothly.

---

### 💡 **Key concept: Performance SLA**

A **performance SLA** (Service Level Agreement) in SD-WAN is like a set of **rules that check the quality of a link** — for example, how fast it is (latency), how reliable it is (packet loss), and how consistent it is (jitter).
If a link doesn’t meet the set performance criteria, FortiGate can **take actions** to switch traffic to a better link.

---

### ⚙️ **Two automatic actions FortiGate can take**

When FortiGate decides a link is “down” (because it’s performing poorly), it can do two things automatically:
1. **update-static-route**
2. **update-cascade-interface**
Both are **on by default**.

Let’s focus on what they mean.

---

### 1️⃣ **update-static-route**

This means FortiGate will **disable static routes** that use the bad interface.
**Example:**
- You have two Internet links:
    - **port1** → ISP A (main link)
    - **port2** → ISP B (backup link)
- You have static routes (fixed paths) that send traffic through **port1**.
- If the link on **port1** becomes bad, FortiGate **disables those static routes**.
- This automatically pushes all traffic through **port2** (the working link).

✅ **Benefit:** Keeps users online without manual changes.  
⚠️ **Warning:** You need to be careful with **overlay tunnels** (like IPsec VPNs).

---

### 🧱 **Impact on overlay tunnels (like VPNs)**

If you have a VPN tunnel (say, to headquarters) that uses **port1** and a **static route**, and that static route is disabled, the tunnel will go down too.

FortiGate will then try to **rebuild the tunnel through the backup link (port2)** — _if it’s configured to do that._

---

### 2️⃣ **update-cascade-interface**

This is another automatic action that helps FortiGate adjust internal routing when a link goes down — basically making sure everything connected to that link is updated properly.

---

### 🧭 **Simple real-world analogy**
Think of it like driving:
- You have two roads to work: Road A (main) and Road B (backup).
- You set up a rule: “If Road A has traffic (too slow), take Road B.”
- **update-static-route** is like your GPS saying: “Don’t even consider Road A anymore — I’ll block it off.”
- FortiGate will reroute all cars (traffic) through Road B automatically.

But — if your carpool app (VPN tunnel) was specifically set to start only on Road A, you need to make sure it can also start on Road B when needed.

---
---

### 🧩 The Main Idea

These pages are explaining what happens when a **FortiGate** firewall detects that one of its **SD-WAN links** (connections to the internet or other networks) is **not working well** or has gone **down**.

---

### 1️⃣ What is a Performance SLA?

A **performance SLA (Service Level Agreement)** is like a **health monitor** for each internet connection (SD-WAN link).  
It checks whether the link is still **alive** (good) or **dead** (bad) — for example, by testing delay, jitter, or packet loss.

---

### 2️⃣ What Actions Can FortiGate Take?

When a link’s performance gets too bad or it goes down, FortiGate can automatically take certain **actions**.  
The two main ones mentioned are:

- **update-static-route**
- **update-cascade-interface**

In this section, we focus on **update-static-route**.

---

### 3️⃣ What Does “update-static-route” Mean?

A **static route** tells FortiGate where to send traffic for a specific destination.  
Example:

> “To reach the 10.10.10.0 network, send traffic through `port1` using gateway `192.2.0.2`.”

If **update-static-route** is turned on (it is **on by default**):

- When the link (like `port1`) goes **down**, FortiGate will **disable** (make inactive) any static routes that depend on that link.
- When the link comes **back up**, those routes become **active** again.

This helps ensure that FortiGate doesn’t keep trying to send traffic through a **dead link**.

---

### 4️⃣ Example Scenario

Imagine you have two internet connections:

- **port1** → ISP A (`192.2.0.2`)
- **port2** → ISP B (`192.3.0.2`)

You have static routes that say:

- “Send most traffic through port1 (preferred path).”
- “Use port2 if port1 is down.”

Now, suppose port1’s connection fails.

✅ Because **update-static-route** is enabled:

- FortiGate will mark all static routes using port1’s gateway (`192.2.0.2`) as **inactive**.
- It removes them from the routing table.
- Then it automatically starts using the static routes through port2 (the backup link).

When port1 comes back online:

- FortiGate detects that it’s **alive** again.
- It reactivates the static routes using port1.

---

### 5️⃣ Impact on VPN or Overlay Tunnels

If a VPN tunnel (like **HUB1-VPN1**) is built over port1, and that link goes down:

- The static route used to reach the VPN peer (remote endpoint) also becomes **inactive**.
- This means the VPN tunnel will go down.
- FortiGate will then try to re-establish the VPN using another available link (like port2), if configured to do so.
    

---

### 💡 In Short

|Condition|What FortiGate Does (with update-static-route enabled)|Example|
|---|---|---|
|Link is **alive**|Static routes through that link are **active**|port1 works → routes through port1 are used|
|Link goes **dead**|Static routes through that link become **inactive**|port1 fails → routes through port1 disabled, port2 takes over|
|Link becomes **alive again**|Static routes are reactivated|port1 restored → routes through port1 reappear|

