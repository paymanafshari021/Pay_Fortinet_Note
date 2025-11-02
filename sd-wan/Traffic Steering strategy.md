# Traffic Steering strategy

FortiGate gives you **5 strategies** to choose from:

---
#### **1. Single-Link Strategies** (Pick **ONE** best link)

| Strategy | Simple Explanation | When to Use | Example |
|----------|---------------------|-------------|---------|
| **Manual** | Always try the links in the order you list them. First one that works = go! | You have a **favorite fast link**, but want a backup if it fails. | Link A (office fiber) → preferred. If down → Link B (4G backup). |
| **Best Quality** | Pick the link that’s **performing best right now** (lowest delay, jitter, or packet loss). | Real-time apps like **Zoom calls** or **VoIP** that hate lag. | Zoom detects Link A has high jitter → switches to Link B automatically. |
| **Lowest Cost (SLA)** | Pick a link that **meets your speed rules (SLA)** AND has the **lowest cost**. If tie → use order. | Save money but keep decent speed. | SLA: latency < 100ms. Link A ($50, 80ms) and Link B ($30, 90ms) both pass → pick **Link B** (cheaper). |

---

#### **2. Load-Balancing Strategies** (Use **MULTIPLE** links at once)

| Strategy | Simple Explanation | When to Use | Example |
|----------|---------------------|-------------|---------|
| **Manual + Load Balance** | Split traffic **evenly** across **all working links** (or all that meet SLA). | You have **multiple good links** and want to use them all. | 3 links up → send 1/3 of traffic on each (like 3 lanes on a highway). |
| **Lowest Cost (SLA) + Load Balance** | Split traffic **only across links that meet your SLA**, then balance. | Save cost **and** use bandwidth wisely. | SLA: < 150ms latency. Only Link B & C qualify → split traffic 50/50 between them. Link A (too slow) is ignored. |

---

### Real-Life Analogy: Delivering Packages

- **Manual**: “Use Truck #1. If broken, use Truck #2.”
- **Best Quality**: “Use the fastest truck right now.”
- **Lowest Cost (SLA)**: “Use any truck that arrives in under 2 hours. Among those, pick the cheapest.”
- **Load Balance**: “Split packages across all working trucks.”
- **Lowest Cost + Load Balance**: “Split packages only across trucks that arrive in under 2 hours.”

---

### Quick Summary Table

| Goal | Strategy |
|------|----------|
| Always prefer one link (with backup) | **Manual** |
| Best for video/voice calls | **Best Quality** |
| Save money + keep performance | **Lowest Cost (SLA)** |
| Use all bandwidth | **Manual + Load Balance** |
| Use all *good* bandwidth | **Lowest Cost (SLA) + Load Balance** |
