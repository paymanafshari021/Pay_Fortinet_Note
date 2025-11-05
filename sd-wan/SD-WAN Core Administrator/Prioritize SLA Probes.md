# Prioritize SLA Probes
### 🔹 Key Idea: QoS and SD-WAN Health-Check Probes

**QoS (Quality of Service)** is a way to control how network traffic is prioritized — for example, making sure voice or video traffic gets faster service than bulk file downloads.

**Health-check probes** are small test packets that SD-WAN devices send across network links to check if a link is healthy (fast, responsive, and not dropping packets).  
If these probes detect problems, SD-WAN can reroute traffic to a better path.

---

### 🔹 What the passage says

1. **You can give a “class ID” (a QoS priority level) to your SD-WAN probes.**  
    This means you can decide how important these test packets are compared to other network traffic.
    
2. **If you give probes a _high priority_, they’ll always get through — even when the network is busy.**  
    That’s good because your SD-WAN device will still be able to accurately test link quality during congestion.  
    → This helps prevent “link flapping,” where SD-WAN keeps switching links back and forth because it thinks one is bad when it’s actually just congested.
    
3. **But don’t make probes _too_ high priority.**  
    If the probes always get through perfectly but your real user traffic is suffering (slow or dropping), SD-WAN might think the link is fine — even though it’s not.  
    → That could cause poor performance because SD-WAN will keep using a bad link.
    
4. **You can configure this using the CLI command:**  
    `class-id <number>`  
    (where `<number>` corresponds to a specific QoS class or traffic priority level in your system).
    

---

### 🔹 Example 1: Prioritizing probes

Let’s say you have:

- **VoIP calls** = High priority (class ID 5)
    
- **Web traffic** = Medium priority (class ID 3)
    
- **Downloads** = Low priority (class ID 1)
    

If you set **SLA probes** (health checks) to **class ID 5**, they’ll still work well even if the link is busy with web traffic or downloads.  
✅ **Good** — your SD-WAN won’t wrongly think the link is broken during congestion.  
⚠️ **But be careful** — if the probes are always top priority, they may hide real link issues.

---

### 🔹 Example 2: Matching probe and traffic classes

Alternatively, if a link mostly carries **video traffic (class ID 4)**, you might set the **probe’s class ID to 4** too.  
That way, the probes experience the same network conditions as the traffic they represent — giving a more realistic view of link quality.

---

### 🔹 Summary

|Concept|Meaning|Why it matters|
|---|---|---|
|**QoS class ID**|Defines traffic priority|Controls which packets get sent first in congestion|
|**SLA probe**|Health-check packet|Tests link quality (latency, loss, jitter)|
|**High-priority probes**|Always get through|Prevent false link failures|
|**Too high priority**|Probes look good, user traffic bad|Can mislead SD-WAN into using a bad link|
|**Best practice**|Align probe class with main traffic|Ensures realistic health checks|
