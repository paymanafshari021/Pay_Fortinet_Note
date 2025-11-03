# Use SD-WAN for Local-Out Traffic?

### 🧠 What is _local-out traffic_?

**Local-out traffic** means any traffic **that starts from the FortiGate itself**, not from the devices behind it.  
It’s traffic FortiGate generates for its **own management or system purposes**.

**Examples:**

- FortiGate pings another device (like `execute ping 8.8.8.8`)
    
- FortiGate contacts FortiGuard to download updates
    
- FortiGate sends logs to FortiAnalyzer
    
- FortiGate queries a DNS server for a name lookup
    

So, it’s “FortiGate talking to the outside world,” not user devices inside the network.

---

### ⚙️ How does local-out traffic normally find its way out?

By default, FortiGate uses its **routing table (FIB = Forwarding Information Base)** to decide which interface to use.  
That means:

> It picks the interface with the _best route_ (lowest cost, highest priority) to reach the destination.

**Example:**

- FortiGate has two WAN interfaces:
    
    - `port1` → Internet via ISP 1
        
    - `port2` → Internet via ISP 2
        

Routing table says:

- Route to `8.8.8.8` (Google DNS) is best via `port1`.
    

✅ So FortiGate uses **port1** for system DNS queries.

---

### 🌐 Using SD-WAN instead

Now, you can choose to have **local-out traffic use SD-WAN rules instead of the routing table**.

You do this by telling FortiGate to use SD-WAN for a specific feature:

```bash
config system dns
    set interface-select-method sdwan
end
```

Once that’s done:

- FortiGate will **look at SD-WAN rules** (not the normal routing table) to decide which WAN link to use.
    

**Example:**

- SD-WAN rule says “use `port2` for all DNS traffic.”
    
- Now, DNS queries go out through **port2**, even if the routing table said `port1` was better.
    

✅ So SD-WAN gives you more control and flexibility — you can steer local-out traffic based on SD-WAN policies.

---

### 🧩 Where can you use this?

You can enable SD-WAN routing for many “system-level” features, such as:

- **DNS**
    
- **NTP** (time sync)
    
- **NetFlow**
    
- **Central Management**
    
- **Syslogd**
    
- **FortiAnalyzer connections**
    

---

### 🧪 Testing with Ping or Traceroute

You can also make **FortiGate’s own ping and traceroute commands** use SD-WAN:

```bash
execute ping-options use-sdwan yes|no
execute traceroute-options use-sdwan yes|no
```

That way, when you ping something from the FortiGate CLI, it will follow SD-WAN rules.

---

### ⚠️ Important Details

1. **The same SD-WAN logic applies:**
    
    - Only members that have a valid route in the routing table can be used.
        
    - Health checks and priorities in SD-WAN still matter.
        
2. **Source IP must match the SD-WAN rule:**
    
    - The rule’s _source address_ must include the FortiGate’s local IP (the one it uses to send the traffic).
        
    - Easiest option: set the rule’s source to **“all addresses”** so it always matches.
        

---

### 🧭 Summary Example

|Action|Default Behavior|With SD-WAN Enabled|
|---|---|---|
|FortiGate does DNS lookup|Uses route table → goes out `port1`|Uses SD-WAN rules → may go out `port2`|
|FortiGate pings 8.8.8.8|Uses FIB|Uses SD-WAN if `use-sdwan yes`|
|FortiGate sends logs|Uses route table|Can be forced through SD-WAN path|
