1. Local AS 
2. Router ID
3. BGP neighbours
4. Advertise Networks
---

| مبحث                            | توضیح کوتاه                       | کاربرد عملی FortiGate                      | مثال کانفیگ                                                             | سناریوی تست                                 |
| ------------------------------- | --------------------------------- | ------------------------------------------ | ----------------------------------------------------------------------- | ------------------------------------------- |
| Introduction                    | پروتکل BGP برای تبادل مسیر بین AS | ارتباط با ISP و شبکه داخلی                 | –                                                                       | بررسی مسیرهای BGP و همسایه‌ها               |
| BGP Neighborship                | تعریف همسایه BGP (iBGP/eBGP)      | تعیین neighbor و ASN                       | `config router bgp ... edit "10.1.1.2" set remote-as 65002`             | بررسی اتصال TCP 179 و وضعیت neighbor        |
| BGP Neighborship States         | وضعیت همسایگی BGP                 | مشاهده حالت Idle, Active, Established      | `get router info bgp summary`                                           | اطمینان از وضعیت Established                |
| Inject Prefix                   | اعلام شبکه به BGP                 | انتشار شبکه داخلی                          | `config network`<br>`edit 1` <br>`set prefix 192.168.10.0/24`           | مشاهده مسیر در neighbor                     |
| Aggregation                     | کاهش تعداد مسیرها                 | جمع کردن چند شبکه                          | `config aggregate-address` <br>`edit 1` <br>`set prefix 192.168.8.0/22` | بررسی advertised-routes                     |
| Advertise Default               | ارسال مسیر پیش‌فرض                | هدایت ترافیک مقصد ناشناخته                 | `set default-originate enable`                                          | مسیر 0.0.0.0/0 در neighbor دیده شود         |
| Advertise BGP Routes            | تبلیغ مسیرها به neighbor          | کنترل مسیرهای خروجی                        | route-map با filter                                                     | بررسی advertised-routes                     |
| Routing Table / Synchronization | همگام‌سازی مسیرها                 | ورود مسیرهای BGP به جدول اصلی              | `get router info bgp route`                                             | بررسی ورود مسیر به routing-table            |
| Confederation                   | تقسیم AS بزرگ                     | مدیریت ASهای داخلی                         | `set confederation-id 65000`                                            | مسیرهای sub-AS به عنوان یک AS دیده شوند     |
| Route Reflector                 | کاهش نیاز full-mesh iBGP          | انتشار مسیرها بین iBGP clients             | `set route-reflector-client enable`                                     | مسیرهای iBGP بین clients منتشر شوند         |
| BGP Filtering                   | کنترل مسیرهای ورودی/خروجی         | جلوگیری از مسیرهای غیرمجاز                 | route-map با prefix-list                                                | مسیرهای غیرمجاز فیلتر شوند                  |
| AS-Path Filtering               | فیلتر مسیرها بر اساس AS           | انتخاب مسیرهای خاص                         | AS-path + route-map                                                     | مسیرهای دارای AS مشخص مسدود شوند            |
| Decision Making                 | انتخاب مسیر بر اساس اولویت        | تغییر Weight, Local Pref, MED              | route-map با set-local-preference                                       | مسیر مطلوب انتخاب شود                       |
| PBR Weight                      | انتخاب مسیر محلی                  | مسیر با Weight بالاتر انتخاب شود           | route-map با set-weight                                                 | مسیر Weight بالاتر انتخاب شود               |
| PBR Local Preference            | انتخاب مسیر در AS                 | مسیر با Local Preference بالاتر انتخاب شود | route-map با set-local-preference                                       | مسیر منتخب در همه iBGP peers                |
| PBR AS-Path                     | Policy بر اساس AS Path            | ترجیح مسیرهای خاص                          | route-map با match-as-path                                              | مسیرهای مشخص انتخاب شوند                    |
| Remove Private-AS               | حذف AS خصوصی                      | امنیت مسیر eBGP                            | `set remove-private-as enable`                                          | Private AS قبل از ارسال به ISP پاک شود      |
| local-as                        | نمایش ASN متفاوت به neighbor      | scenarios خاص چند-AS                       | `set local-as 65010`                                                    | neighbor مسیرها را با ASN متفاوت دریافت کند |
| PBR MED                         | انتخاب مسیر ورودی از AS دیگر      | مسیر با MED کمتر ترجیح داده شود            | route-map با set-med                                                    | مسیر با MED پایین‌تر انتخاب شود             |
| MultiPath                       | Load balancing                    | استفاده چند مسیر همزمان                    | `set multipath-relax enable`                                            | مسیرهای متعدد در جدول مسیریابی              |
| Community                       | برچسب مسیر                        | گروه‌بندی مسیرها                           | route-map با set-community                                              | Community روی مسیرها اعمال شود              |
| Special Community               | NO_EXPORT, NO_ADVERTISE           | کنترل انتشار مسیرها                        | route-map با NO_EXPORT                                                  | مسیر فقط در AS محلی دیده شود                |
| Dampening                       | کاهش اثر مسیر ناپایدار            | حذف مسیرهای Flap                           | `set dampening enable`                                                  | مسیرهای ناپایدار برای مدتی ارسال نشوند      |
| MP-BGP IPv6                     | پشتیبانی IPv6                     | ارسال مسیر IPv6 روی BGP                    | activate-ipv6 enable                                                    | مسیر IPv6 دریافت و وارد جدول شود            |
| Convergence                     | همگرایی BGP                       | کاهش زمان پایدار شدن مسیر                  | set keepalive/hold-timer                                                | مسیر جدید سریع انتخاب شود                   |
| Conditional Advertisement       | اعلام شرطی مسیر                   | تبلیغ مسیر فقط وقتی مسیر دیگر موجود باشد   | conditional-advertisement                                               | مسیر فقط در شرایط خاص ارسال شود             |
| Conditional Route Injection     | تزریق شرطی مسیر                   | Inject مسیر در شرایط خاص                   | route-map + prefix-list                                                 | مسیر فقط وقتی شبکه up باشد ارسال شود        |
| TTL Security                    | امنیت BGP                         | جلوگیری از spoofing                        | set ttl-security enable                                                 | اگر TTL کمتر باشد session ایجاد نشود        |
| ORF                             | فیلتر مسیر خروجی                  | کاهش حافظه و پردازش                        | set orf prefix-list enable                                              | مسیر غیرمجاز قبل از ورود به RIB حذف شود     |
| Redistribute-internal           | Redistribute مسیر داخلی           | تبلیغ static/connected                     | config redistribute                                                     | مسیرهای داخلی به neighbor اعلام شود         |
| BGP in Enterprise               | استفاده سازمانی BGP               | iBGP/eBGP, route-reflector, policy         | ترکیب route-maps و communities                                          | بررسی مسیرها، failover و load balancing     |
| BGP in Enterprise Part2         | پیشرفته Enterprise                | Failover, Load balancing, Security         | PBR, multipath, TTL Security                                            | تست failover و امنیت مسیرها                 |

---

### **1. Stub AS**
- An AS that only handles **local traffic** and has **a single connection** to another AS.
- **Characteristics:**
    - Only one exit point to the Internet or another AS.
    - Routes traffic that originates and terminates **within the AS**.
### **2. Multihomed AS**
- An AS that handles **local traffic only**, but has **multiple connections** to different ASes.
- **Characteristics:**
    - Multiple exit points for redundancy or load balancing.
    - Still does not provide transit for other ASes.
### **3. Transit AS**
- An AS that handles **local traffic** and also **transit traffic** (traffic passing through to other ASes).
- **Characteristics:**
    - Provides connectivity between other ASes.
    - Can originate, terminate, and forward traffic.
---
# Redistribution

+ Taking routes from one source (like connected interfaces, static routes, or even another routing protocol)  
+ And injecting them into BGP so that BGP neighbors can learn about them.
For example:
- **Connected routes** = networks directly configured on FortiGate interfaces
- **Static routes** = routes you’ve manually added
- **Other protocols** = OSPF, RIP, etc.
```bash
# Configure BGP
config router bgp
    set as 65001                # Your ASN
    set router-id 192.168.1.1   # BGP Router ID (usually loopback or interface IP)
    # Configure a neighbor
    config neighbor
        edit 203.0.113.2
            set remote-as 65002      # Neighbor’s ASN
        next
    end
    # Redistribute connected and static routes into BGP
    config redistribute "connected"
        set status enable
    end

    config redistribute "static"
        set status enable
    end
end
```

Summary:  
FortiGate BGP starts with **no advertised prefixes**.  
You use `redistribute connected/static/...` to tell it what to send.  
This ensures you only share the networks you want, not everything.

---
##  1. **Access List**

- A simple way to define which **prefixes** are allowed or denied.
- With `distribute-list-in/out`.
- **Granularity**: Can filter routes, but pretty basic.
- **Extra parameters**:
    - `ge` (greater or equal) and `le` (less or equal) — used to match on prefix length.  
        Example: match `10.0.0.0/8` but only if the prefix length is `/16` or more.

Example:

```bash
config router access-list
    edit "ACL1"
        config rule
            edit 1
                set prefix 10.0.0.0 255.0.0.0
                set ge 16
                set le 24
            next
        end
    next
end

config router bgp
    config neighbor
        edit 203.0.113.2
            set distribute-list-out "ACL1"
        next
    end
end
```

Here, only subnets of `10.0.0.0/8` between `/16` and `/24` are advertised.
## 2. **Prefix List**

- A more flexible and modern way to filter based on prefixes.
- With `prefix-list-in/out`.
- **Granularity**: More powerful than access lists (supports logical operators and ordered rules).
- **Use case**: Often used in service provider environments to filter exact networks or ranges of networks.

Example:

```bash
config router prefix-list
    edit "PLIST1"
        config rule
            edit 1
                set prefix 192.168.0.0 255.255.0.0
                set ge 24
                set le 28
            next
        end
    next
end

config router bgp
    config neighbor
        edit 203.0.113.2
            set prefix-list-out "PLIST1"
        next
    end
end
```

This allows only `192.168.x.x` prefixes with a length between `/24` and `/28` to be advertised.
## 3. **Route Map**

- **The **most advanced and flexible** filtering method.
- **With `route-map-in/out` or inside `redistribute`.
- **Granularity**:
    - Can match based on access lists, prefix lists, or other attributes (like AS_PATH, community, next-hop).
    - Can **set BGP attributes** (like local-preference, MED, weight, etc.).
- **Use case**: Used when you need **policy control**, not just filtering.
    

Example:

```bash
# Step 1: Create a prefix list to match the prefixes you care about
config router prefix-list
    edit "PLIST2"
        config rule
            edit 1
                set prefix 10.10.0.0 255.255.0.0
                set ge 24
                set le 24
            next
        end
    next
end

# Step 2: Create a route map that references the prefix list
config router route-map
    edit "RM1"
        config rule
            edit 1
                set match-ip-address "PLIST2"
                set set-local-preference 200
                set action permit
            next
        end
    next
end

# Step 3: Apply the route map to a neighbor
config router bgp
    config neighbor
        edit 203.0.113.2
            set route-map-out "RM1"
        next
    end
end
```

This advertises only `10.10.x.x/24` networks and sets their **local preference** to `200`.

---
## Quick Comparison

| Method          | Where Used        | Power Level | Can Modify Attributes? | Typical Use Case                                   |
| --------------- | ----------------- | ----------- | ---------------------- | -------------------------------------------------- |
| **Access List** | `distribute-list` | Basic       | ❌ No                   | Simple prefix blocking                             |
| **Prefix List** | `prefix-list`     | Medium      | ❌ No                   | Fine-grained prefix filtering                      |
| **Route Map**   | `route-map`       | Advanced    | ✅ Yes                  | Policy control, filtering + attribute modification |
So, **access and prefix lists** are for **basic filtering** (yes/no decisions).  
**Route maps** are for **advanced policies** (filtering + changing attributes).

---
# Routing Information Bases

![[Pasted image 20250914154209 1.png]]
1. Routes **come in** from BGP neighbors (RIB-in).
2. **Inbound filters** decide which are accepted.
3. Accepted routes are placed in the **Local RIB**.
4. Routes from the **routing table** (connected, static, OSPF, RIP, IS-IS) can be **redistributed into BGP**.
5. Before advertising, **Outbound filters** control which routes leave.
6. Allowed routes go to **RIB-out**, then to neighbors (**BGP TX**).
---
# BGP Commands Comparison

| Command                             | Information Displayed                                                                                 |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `get router info bgp summary`       | BGP status of the router and all neighbors, including: local AS, packet counters, uptime of neighbors |
| `get router info bgp neighbors`     | Details of each neighbor: peer IP, router ID, remote AS, BGP state, negotiated capabilities           |
| `get router info bgp network`       | Displays the BGP database (all networks known to BGP)                                                 |
| `get router info routing-table bgp` | Shows the BGP routing table (routes chosen by BGP and installed in the RIB)                           |
# Split Horizon
By default, IBGP does not advertise prefixes between iBGP neighbors. This IBGP split horizon rule prevents routing loops. Within an AS, you must then establish full mesh IBGP peerings to learn all the prefixes within an AS.
# Loopback Interfaces for BGP
Using loopbacks as BGP sources provides stability and redundancy. The trade-off is that you must enable `ebgp-multihop` and ensure reachability through your IGP or static routes.

```bash
config router bgp
    set as 65100
    set router-id 172.16.1.254
    config neighbor
        edit 100.64.1.254
            set remote-as 65101
            set update-source Loopback_Interface # Uses the loopback IP as the BGP source address instead of a physical interface
            set ebgp-enforce-multihop enable # Needed because loopback interfaces are not directly connected; BGP packets must traverse multiple hops.
        next
    end
end
```
### Why `ebgp-multihop`?

Normally, eBGP peers must be directly connected (next hop = physical link).  
But when you peer via loopbacks:
- Packets originate from the loopback (not directly reachable over one hop).
- Router needs to allow multihop so BGP packets can traverse underlying paths.

---
## Why Use `neighbor-group`?

- In **large BGP deployments**, many peers share the same settings (e.g., same interface, same remote ASN, same policies).
- Instead of configuring those parameters for each peer **individually**, you define them **once** in a **neighbor group**.
- Each peer that matches (via `neighbor-range` or explicit assignment) automatically inherits those settings.
This is especially useful in **SD-WAN overlays**, where:
- The **hub** has many **spokes**.
- Spokes connect dynamically (dial-up BGP).
- Each underlay transport (e.g., ISP1, ISP2, LTE) might need its own neighbor group.
## Config Example (Hub)
```bash
config router bgp
    set as 65100
    set router-id 172.16.1.254
    set ibgp-multipath enable

    config neighbor-group
        edit SpokeISP1
            set interface ISP1
            set remote-as 65100
        next
    end

    config neighbor-range
        edit 1
            set prefix 10.1.0.0 255.255.255.0
            set neighbor-group SpokeISP1
        next
    end
end
```
## Key Parts Explained
1. **`config neighbor-group`**
    - Defines a reusable template.
    - Here:
        - **`set interface ISP1`** → BGP sessions will be established over `ISP1`.
        - **`set remote-as 65100`** → Since this is within the same AS, these are **iBGP peers**.
2. **`config neighbor-range`**
    - Defines **which peers belong** to a group.
    - Here:
        - **`set prefix 10.1.0.0 255.255.255.0`** → Any peer whose source IP is in this subnet joins the `SpokeISP1` group.
        - **`set neighbor-group SpokeISP1`** → Those peers inherit the group’s settings.
3. **`set ibgp-multipath enable`**
    - Since all peers are in the same ASN (65100 = iBGP), multipath is enabled.
    - Allows **ECMP (Equal-Cost Multi-Path)** routing for multiple iBGP-learned paths.
4. Is useful in an SD-WAN overlay design

---
## Problem in iBGP
- By default, **iBGP does not advertise routes learned from one iBGP peer to another iBGP peer.**
- **Solution (traditional)**: Build a **full mesh** iBGP network → every router peers with every other router.
- **Issue**: Full mesh doesn’t scale (requires _N(N–1)/2_ sessions).
## Solution: Route Reflectors (RRs)
- **Route Reflectors act as hubs** inside the AS.
- Clients peer only with the RR, not with every other router.
- The RR **reflects routes** from one client to others.
- Greatly reduces session count, simplifies management, and improves convergence in large topologies.
##  Config Example (RR)

```bash
config router bgp
    config neighbor
        edit 100.64.1.254
            set next-hop-self enable
            set route-reflector-client enable
        next
    end
end
```
### Key Commands:
- **`set next-hop-self enable`**
    - Ensures the RR announces itself as the next-hop when reflecting routes.
    - Useful when clients cannot directly reach the original next-hop.
- **`set route-reflector-client enable`**
    - Marks this neighbor as an **RR client**.
    - The router becomes responsible for reflecting routes between this client and others.
---
## What is BFD?

- **BFD (Bidirectional Forwarding Detection)** is a lightweight protocol that **rapidly detects link or device failures**.
- Works independently of BGP or other routing protocols.
- Detects failures **much faster** than BGP keepalives (sub-second detection vs. seconds).
- Useful for **faster convergence**, especially in critical or highly available networks.
## Why Use BFD with BGP?

- BGP’s **default keepalive timers** are typically 30–60 seconds.
- With BFD, BGP can detect a neighbor failure **in less than 1 second**.
- Supports both:
    - **Directly connected neighbors**
    - **Multihop neighbors** (e.g., loopback-based BGP sessions)
## FortiGate BFD Configuration for BGP
### **1. Enable BFD on the BGP neighbor**

```bash
config router bgp
    config neighbor
        edit 100.64.1.254
            set bfd enable                  # Enable BFD
            set ebgp-enforce-multihop enable  # Needed if neighbor is multihop
        next
    end
end
```

This tells FortiGate to monitor the BGP neighbor with BFD.
### **2. Configure BFD templates (for multihop)**

```bash
config router bfd
    config multihop-template
        edit 1
            set src 100.64.2.0 255.255.255.0   # Source subnet for BFD probes
            set dst 100.64.1.0 255.255.255.0   # Destination subnet
            set auth-mode md5                    # Optional: MD5 authentication
            set md5-key password
        next
    end
end
```

- **`set src` / `set dst`** → Defines the BFD endpoints for multihop detection.
- **`auth-mode md5`** → Secures BFD probes against tampering.
- **Templates** allow multiple BFD sessions to share common settings.
##  How BFD Works with BGP
1. FortiGate sends **BFD control packets** at a high rate (sub-second).
2. If a probe is **missed**, BFD immediately signals a **link failure**.
3. BGP responds quickly by **withdrawing routes** learned via that neighbor.
4. Network converges faster than waiting for normal BGP keepalives.
##  Key Notes
- BFD must be **enabled on both routers**.
- For **multihop neighbors**, you must create a **BFD template** with proper source/destination.
- Works with **any media** (fiber, MPLS, WAN links)
**Summary**:  
BFD acts as a rapid “heartbeat” for BGP sessions.
- Directly connected neighbors → enable `bfd` on the neighbor.
- Multihop neighbors → configure a **multihop BFD template** and apply to neighbor.
- Result → **faster detection of failures**, faster **BGP convergence**, and more resilient networks.
---
### **Purpose of BGP Graceful-Restart**

- **Prevent traffic interruption** during HA failover or maintenance.    
- Ensures that **BGP routes remain in the routing table** even if the BGP process stops temporarily.
- Avoids **route flaps** or unnecessary BGP reconvergence when the active unit in an HA cluster fails or reboots.
### **How It Works in HA**
1. **HA Cluster Behavior**
    - When the primary FortiGate goes offline (failover, reboot, or upgrade), the HA cluster **advertises that it is going offline** to its BGP peers.
    - This signals to the neighbor router that the routes are still valid, even though the BGP session might be temporarily down.
2. **BGP Peering**
    - BGP peering only runs on the **primary unit** of the HA cluster.
    - Upon failover, the new primary must **re-establish BGP sessions**, but thanks to graceful-restart, **existing routes are retained** in the routing table until the session is back up.
3. **Traffic Continuity**
    - Prevents traffic interruption during maintenance or failover.
    - Ensures the HA cluster comes online **with a usable routing table** immediately, avoiding a period of blackholing. 
### **Configuration Example**
On the FortiGate router:
```bash
config router bgp
    set as 65100
    set graceful-restart enable
    set router-id 172.16.1.254
    config neighbor
        edit 100.64.1.254
        set capability-graceful-restart enable
        set remote-as 200
    next
end
```
**Key Points:**
- `set graceful-restart enable` → Enables graceful-restart globally on the BGP router.
- `set capability-graceful-restart enable` → Advertises to the neighbor that this router supports graceful-restart.
- Both routers in the BGP session should have graceful-restart enabled for it to work effectively.
**Benefits**
- Smooth failover in HA environments.
- Minimizes BGP route churn and traffic disruption.
- Particularly useful during upgrades or planned maintenance.

---
## **BGP Route Selection Tie-Breakers (in order)**

When multiple routes exist to the same destination, BGP applies these rules sequentially until one best path is chosen:

1. **Highest Weight**
    - Cisco-specific attribute (not propagated to other routers).
    - Locally significant only: higher weight is preferred.
    - Default: routes originated on the router = weight 32768, others = 0.
2. **Highest Local Preference**
    - Attribute shared within the AS.
    - Higher local preference is preferred.
    - Default = 100. Used for **policy-based routing decisions**.
3. **Prefer the Path That Originated Locally**
    - Routes **originated on the router** itself (via `network` or `aggregate` commands) are preferred over routes learned from peers.
4. **Shortest AS Path**
    - Fewer AS hops is preferred.
    - Prevents routing loops and reduces path length.
5. **Lowest Origin Type**
    - Order of preference: **IGP < EGP < Incomplete**.
    - IGP = learned via `network` command, EGP = obsolete external protocol, Incomplete = redistributed into BGP.
6. **Lowest Multi-Exit Discriminator (MED)**
    - Lower MED is preferred.
    - Used to influence inbound traffic when multiple links exist between the same AS.
    - Only compared if the routes are from the same neighboring AS.
7. **Lowest IGP Metric to the BGP Next Hop**
    - Chooses the route with the lowest internal (IGP) cost to reach the BGP next hop.
8. **Prefer External (EBGP) Paths over Internal (IBGP) Paths**
    - EBGP routes are considered more reliable/closer to the destination than IBGP routes.
9. **ECMP (Equal-Cost Multi-Path) Support**
    - If ECMP is enabled, up to **10 equal-cost routes** can be inserted into the routing table.
    - Provides load sharing.
10. **Lowest Router ID**
    + As a last resort, the route learned from the BGP peer with the **lowest router ID** is preferred.
    - Router ID = manually configured, or the highest IP address on a loopback/interface.
---
## **Key Considerations for BGP on FortiGate**

### **1. Scaling Capabilities**

- FortiGate has **no hardcoded BGP limits**.
- The number of **neighbors, routes, and policies** directly depends on **system memory**.
- In large-scale deployments, memory usage is the primary constraint.    
### **2. Prefix Origination**

- By default, **FortiGate does not advertise any prefixes** into BGP.
- To originate routes, you must:
    - Use **redistribution** (e.g., from connected, static, or OSPF routes), or
    - Manually configure which prefixes to advertise.
Example:
```bash
config router bgp
    set as 65100
    config network
        edit 1
            set prefix 10.10.10.0 255.255.255.0
        next
    end
end
```

### **3. Prefix Acceptance**

- By default, **all received routes are accepted** into the BGP table.
- For better control and security, you should apply **route filters or policies**:
    - To **accept/reject prefixes** from neighbors.
    - To **set attributes** (local-pref, MED, etc.) for path selection.

Example (filtering):
```bash
config router prefix-list
    edit "ALLOW-10.10.0.0"
        config rule
            edit 1
                set prefix 10.10.0.0 255.255.0.0
                set ge 16
                set le 24
            next
        end
    next
end
```
## **Summary**

When implementing BGP on FortiGate:
1. **Scaling** is limited by memory, not hard-coded restrictions.
2. **No prefixes are originated by default** → you must configure redistribution or manual networks.
3. **All routes are accepted by default** → you should apply route policies for security and stability.
---
## **Using the `network` Command in BGP**
The `network` command is an alternative to **redistribution** for advertising prefixes in BGP.
### **1. Standard Behavior**
- When you configure a prefix under the `network` statement, FortiGate will **only advertise it if**:
    - There is an **exact match** of that prefix in the routing table, and
    - The route is **active**.
This prevents the FortiGate from advertising unreachable prefixes.
**Example:**
```bash
config router bgp
    config network
        edit 1
            set prefix 192.168.10.0 255.255.255.0
        next
    end
end
```
- If `192.168.10.0/24` exists as an active route in the routing table → it gets advertised.
- If not → it will not be advertised.
### **2. Forcing Prefix Advertisement**

If you want FortiGate to advertise prefixes **regardless of routing table presence**, you can disable the import check:
```bash
config router bgp
    set network-import-check disable
end
```
- With this disabled, **all prefixes configured in the BGP network table are advertised**, even if no matching route exists.
- Useful when:
    - You want to **advertise planned prefixes** that don’t yet exist in the routing table.
    - You’re doing **prefix-based policy control**.

**Caution:** If you disable the check, FortiGate may advertise **unreachable networks**, which can cause **blackholing** of traffic.
##  **Summary**
- `network` command = direct way to advertise specific prefixes into BGP
- Default: only advertises if the prefix **matches an active route**.
- `set network-import-check disable` = forces advertisement regardless of active routes (use carefully).