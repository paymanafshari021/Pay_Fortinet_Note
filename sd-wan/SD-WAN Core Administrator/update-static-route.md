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
