+ SD-WAN is Session based
+ point of presence (PoP): It’s a **gateway** that brings internet or cloud services **closer to users** for faster and more reliable connections.
+ **Cloud on-ramp** means a **dedicated, high-performance connection** between your company’s network and a **cloud provider** (like AWS, Azure, or Google Cloud).
+ Visibility of application detection criteria is, by default, hidden on the FortiGate GUI. Because it is an advanced parameter that requires the administrator to understand its specifics, you must enable the feature visibility on the CLI using the global command `set gui-app-detection-sdwan enable`. Note that if GUI visibility is disabled, configuration for application criteria remains active and configuration is still visible on the CLI.
+ Migrating Interfaces to SD-WAN
	+ ![Alt text](attachments/Pasted_image_20251101114330.png)
	+ ![Alt text](attachments/Pasted_image_20251101114444.png)
	+ Delete Entry: Delete the Firewall Policy
+ When `update-static-route` is enabled, FortiGate disables the static routes for inactive interfaces. 
+ When you enable `update-static-route`, you must carefully consider the consequence on overlay tunnels.
# **Update-Cascade-Interface Feature (SD-WAN 7.6)**

- The **`update-cascade-interface`** feature is **enabled by default** but only functions when at least one **cascade interface** is defined.
- Used to manage interface behavior based on SD-WAN member status.
- **If all SD-WAN members are dead:** FortiGate **shuts down alert interfaces** (e.g., due to degraded performance).
   - **If at least one member is alive:** FortiGate **brings up alert interfaces**.
    #### **Purpose**
- Ensures traffic behind alert interfaces is **rerouted through another device** if FortiGate **cannot forward WAN traffic**.
- Cascade interfaces are **defined globally** for the SD-WAN instance.
- Detection and actions are **activated per performance SLA** (e.g., for underlay links only).
- Focus on **critical interfaces** (e.g., LAN interfaces providing WAN access).
- Shutting down such interfaces can **trigger failover** via mechanisms like **VRRP**.
#### **Example**
- A company uses a **5G backup device** (not connected to FortiGate).
- A performance SLA monitors **ISP links (port1 and port2)**, with **`update-cascade-interface`** enabled to **shut down port5** when links fail.
    
    ![Text](attachments/Pasted_image_20251101141635.png)
    ![](attachments/Pasted_image_20251101141915.png)
    
### ⚠️ **The Problem: Asymmetric or Poor Traffic Paths**

If the **hub** doesn’t know which link (Internet connection) is performing best at a spoke, it might:

- Send data **back to the spoke** over a _different_ (and possibly worse) link.
- This creates **asymmetric traffic flow** — data goes one way over a fast path, but returns on a slower or unstable path.

💡 Example:

- Spoke A has two Internet links: **Link 1 (fiber)** and **Link 2 (4G backup)**.
- Spoke A decides to send traffic to the hub over **Link 1** (good SLA).
- But the **hub doesn’t know** that Link 2 is bad, so it might send return traffic _via Link 2_.  
    → Result: slow or broken communication.
---
### 💬 **The Solution: Passing SLA Information**

To fix this, the spoke can **tell the hub** which links are healthy or not.

It does this by **embedding SLA (Service Level Agreement) information**—things like latency, jitter, packet loss—**into ICMP probe packets** (these are small "ping" messages).

💡 Example:

- Spoke A sends an ICMP probe to the hub saying:
    
    > “Hey, Link 1 is good — latency 15ms, loss 0%. Link 2 is bad — latency 300ms, loss 10%.”
    
- The **hub receives** this info and updates its routing decisions accordingly.
    

---

### 🧭 **How the Hub Uses SLA Info**

When the hub knows which links are good or bad:

- It can **prefer routes** (IKE routes, BGP routes, or static routes) over links that are **inside SLA** (healthy).
- It can **de-prioritize** routes over links **outside SLA** (unhealthy).

In simple terms:

> The hub chooses the best “road” based on real-time link health reported by the spoke.

---

### 🔄 **When Used with Routing Protocols**

- Works well with **BGP** (Border Gateway Protocol) and **static routes**.
- If the network uses **loopback interfaces**, this method integrates neatly with those topologies.
- If not, SD-WAN can use another method — the **route-map-out-preferable** feature — to send link health info to the hub through BGP.

---

### 📡 **Active Probes and Protocol Ping**

To make this work:

- The system needs **active probes** — it must actively send out test packets.
- **Protocol ping** is used to communicate the SLA data between devices.

These pings are what carry the SLA status.

---

### ✅ **In Summary**

|Concept|Simple Meaning|Example|
|---|---|---|
|Hub and Spoke|Central site and remote branches|HQ ↔ branch offices|
|Asymmetric flow|Data goes one way on a good link, returns on a bad one|Upload fast, download slow|
|Embedded SLA info|Health data added into ping messages|Latency, loss, jitter metrics|
|Hub priorities|Hub uses SLA data to pick best routes|Prefers Link 1 if Link 2 is poor|
|Active probes|Periodic test packets to check link quality|Pings every few seconds|

---

Would you like me to include a **diagram** showing how the SLA info flows between hub and spoke? That can make this concept even clearer.
# tshoot
https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/665425/troubleshooting-sd-wan
