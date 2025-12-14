# SD-WAN Rules
### Key Concepts Explained in Simple Words

SD-WAN rules are like **traffic directors** on a FortiGate device. They tell the system **where to send** network packets, not **whether to allow** them (that's the job of firewall rules).

---

#### 1. **What SD-WAN Rules Do**
- **Match traffic** → "Who are you?" (e.g., Is this going to Salesforce?)
- **Steer traffic** → "Go this way!" (e.g., Use the fastest internet link)

> **Simple analogy**:  
> Think of SD-WAN rules as a GPS for your internet traffic. It picks the **best road** (link) based on your preferences (speed, cost, etc.).

---

#### 2. **Two Parts of an SD-WAN Rule**
| Part                    | What It Does           | Example                                            |
| ----------------------- | ---------------------- | -------------------------------------------------- |
| **Matching Criteria**   | Identifies the traffic | "All traffic to `salesforce.com` or `office.com`"  |
| **Steering Preference** | Chooses the path       | "Use the link with the **lowest delay** (latency)" |

---

#### 3. **How FortiGate Chooses the Best Link**
FortiGate looks at **live performance** of each internet link (called **members**, like port1, port2).

It picks the **best alive member** based on your **strategy**:
| Strategy | Meaning | Example |
|---------|--------|--------|
| **Lowest Latency** | Fastest response time | Gaming or video calls |
| **Highest Bandwidth** | Most capacity | Large file downloads |
| **Lowest Cost** | Cheapest link | Backup or bulk data |

> By default: Only **one member** is used per flow (not splitting one app across links).

---

#### 4. **Order Matters (Top-to-Bottom Matching)**
Just like firewall rules:
- FortiGate checks rules **from top to bottom**
- Uses the **first rule that matches**

> **Tip**: Put important apps (like VoIP or CRM) at the top!

---

#### 5. **Firewall Policy Still Needed!**
SD-WAN rule says: *"Send this traffic via port1"*  
But **without a firewall policy**, the traffic is **blocked**.

> **Must have both**:
> - ✅ SD-WAN rule → steers
> - ✅ Firewall policy → allows

---

#### 6. **What If No Rule Matches? → Implicit Rule**
- If no SD-WAN rule fits → FortiGate uses **normal routing**
- Usually means **load balancing** across all equal links (ECMP)

> Example: Random background updates get spread across both ISPs.

---

### Real-World Example: **Critical-DIA Rule**

```text
Rule Name: Critical-DIA
Matches: Traffic to Microsoft.Portal and Salesforce
Strategy: Lowest Latency
Members: port1 (ISP-A), port2 (ISP-B)
```

**What happens**:
1. User opens Salesforce.
2. FortiGate sees: "This matches Critical-DIA rule."
3. Measures latency:
   - port1 → 25 ms
   - port2 → 60 ms
4. Picks **port1** (lowest latency)
5. Sends traffic through port1
6. (Firewall policy must allow this traffic!)

---

### Summary Table

| Concept             | Simple Explanation                                 |
| ------------------- | -------------------------------------------------- |
| **SD-WAN Rule**     | GPS for traffic – picks the best path              |
| **Match + Steer**   | "If it's Salesforce → use fastest link"            |
| **Strategy**        | How to pick "best" (latency, bandwidth, etc.)      |
| **Top-to-Bottom**   | First matching rule wins                           |
| **Firewall Policy** | Must allow the traffic too                         |
| **Implicit Rule**   | If nothing matches → normal routing + load balance |

---

**Bottom line**:  
SD-WAN rules = **smart traffic steering**  
Firewall rules = **traffic permission**  
Use both together for secure, fast, reliable connections!