## FortiOS HA Technical Reference Guide

### 1. Verification and Status

Before making changes, you need to know who is who and if they are talking to each other.

- **`get system ha status`**
- The primary health check. It shows the cluster mode, which unit is the Primary, and sync status.
- **`diagnose sys ha dump-by vcluster`**
- Essential for checking **uptime differences**. FortiGate uses a 5-minute (300s) window for uptime; if the difference is less than that, it's considered a "tie."
---

### 2. The Election Logic (The "Why")

How the FortiGate decides who becomes the Primary depends entirely on the **Override** setting.

|**Order**|**Override Disabled (Default)**|**Override Enabled**|
|---|---|---|
|1|Connected Monitored Ports|Connected Monitored Ports|
|2|**HA Uptime**|**Device Priority**|
|3|Device Priority|HA Uptime|
|4|Serial Number|Serial Number|

> **Note:** Your snippet mentioned `set override enable` for both. To disable it, use `set override disable`. Enabling override makes **Priority** the king of the election.

---
### 3. Triggering a Manual Failover

If you need to move the traffic to the secondary unit for maintenance, use these methods:

- **The "Priority" Method (Best Practice):**
    
    Increase the priority on the Secondary or decrease it on the Primary (with `override enable`).
    
- **The "Uptime" Method:**
    
    `diagnose sys ha reset-uptime`
    
    _This resets the Primary’s internal HA timer, making it "younger" than the Secondary, forcing a swap._
    
- **The "Virtual Cluster" Method:**
    
    `execute ha failover set 1`
    
    _This is a manual trigger. Always remember to `unset` it later, or the unit will stay in a failover state regardless of health._
    

---

### 4. Sync & Communication Issues

If you see a "Checksum Mismatch," the units have different configurations.

- **`diagnose sys ha checksum recalculate`**: Tells the FortiGate to stop, take a breath, and re-read its own config to generate a fresh hash.
- **`execute ha synchronize start`**: Forces the Secondary to pull the configuration from the Primary.
- **`execute ha manage <id> <admin_user>`**: Allows you to jump from the Primary CLI to the Secondary CLI without needing a separate console cable.
