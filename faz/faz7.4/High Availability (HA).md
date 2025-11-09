https://docs.fortinet.com/document/fortianalyzer/7.4.8/administration-guide/506748/high-availability
# High Availability
### FortiAnalyzer High Availability (HA) Overview

**Key Features**

- **Real-time redundancy:** Automatically promotes a secondary unit if the primary fails.
- **Secure synchronization:** Logs, data, and some configurations are synchronized across units.
- **Load distribution:** Secondary units can handle tasks like report generation.

**Cluster Specifications**

- **Maximum size:** Up to 4 units (1 primary + 3 secondary).
- **Hardware requirements:** All units must be from the same FortiAnalyzer series.
- **Visibility:** All units are network-visible.
- **Operation mode:** All must run in the same mode — either _Analyzer_ or _Collector_.

> [!CAUTION] 
> **Licensing:** The cluster adopts the smallest managed-device limit among member licenses.

![](attachments/03.png)

---

### **FortiAnalyzer HA Configuration Summary (v7.4.8)**

#### **Overview**

Configure High Availability (HA) under **System Settings > HA** to form or modify a cluster.  
Two panes are used:

- **Cluster Settings** – Create or change HA configuration.
- **Cluster Status** – Monitor HA status.
#### **Cluster Setup**

- Set **Operation Mode**:
    
    - **Active-Passive** or **Active-Active** (geo-redundant supported).
    - **Standalone** disables HA.
- Add **IP addresses** and **serial numbers** of all units (primary and secondary) to each other’s configuration.
- All units must share the same **Group Name**, **Group ID**, and **Password**.
- Configuration synchronization allows full management via the primary unit’s GUI.

#### **Key Configuration Options**

**Cluster Settings**

- **Operation Mode:** Active-Passive / Active-Active / Standalone.
- **Preferred Role:**
    - _Primary_ – becomes primary if first in a new cluster.
    - _Secondary_ – default; synchronizes with current primary.
- **Initial Sync:** Required before node becomes fully functional.

**Cluster Virtual IP**

- **IP Address & Interface:** Define redundancy address and interface.
- **Action:** Add (+) or remove (x) virtual IPs.

**Peer Configuration**

- **Peer IP / Peer SN:** Enter each unit’s IP and serial number.
- **Action:** Add (+) or remove (x) peers.

**Cluster Identification**

- **Group Name / Group ID (1–255) / Password:** Must match across all members.

**Heartbeat Settings**

- **Heart Beat Interval:** Time between heartbeat packets (default: 4s).
- **Heart Beat Interface:** Interface used for heartbeat traffic.
- **Failover Threshold:** Time before a unit is considered failed (Interval × 3; default 12s).

**Other Settings**

- **Priority:** Determines secondary unit seniority.
- **Log Data Sync:** Enabled by default for real-time log synchronization.

---

### Log Synchronization Overview

FortiAnalyzer HA synchronizes logs between units in two phases: **initial synchronization** and **real-time synchronization**.
### 1. Initial Logs Sync

- Occurs when a new unit joins an HA cluster.
- The **primary unit** copies its logs to the new **secondary unit**.
- After synchronization:
    - The secondary unit **reboots**.
    - It **rebuilds its log database** using the synchronized logs.
- Progress is shown in the **Cluster Status → Initial Logs Sync** column.
### 2. Real-Time Log Synchronization (Log Data Sync)

- **Automatically enabled** after initial sync.
- The **primary unit** forwards logs in **real time** to all **secondary units**.
- Ensures log consistency across the HA cluster.
- Secondary units keep Log Data Sync **on by default** so that, if promoted to primary, they continue synchronization.
- For a **standby (non-secondary) unit**, Log Data Sync can be **disabled** to save resources.

---

### FortiAnalyzer Configuration Synchronization
**Purpose:**  
Provides redundancy and load balancing across FortiAnalyzer HA cluster units by synchronizing configurations.
### **Modules Synchronized Across Cluster**
- **Device Manager**
- **Incidents & Events**
- **Reports**
- **Most System Settings**

---

### **System Settings Synchronization **

**Synchronized:**
- All ADOMs
- Storage Info
- Network > SNMP
- Admin
- Certificates > CA Certificates
- Certificates > CRL
- Log Forwarding
- Fetcher Management
- Task Monitor
- Advanced:
    - Mail Server
    - Syslog Server
    - Meta Fields
    - Device Log Settings
    - Advanced Settings
**Partially Synchronized:**
- Dashboard > Status > System Information — _Only Administrative Domain is synchronized._
**Not Synchronized:**
- Network
- HA
- Certificates > Local Certificates
- Event Log

---

### **Monitoring HA Status**

**Location:**  
_System Settings > HA_ → **Cluster Status** pane
**Purpose:**  
Displays and monitors the High Availability (HA) status of the FortiAnalyzer cluster.
### **Access Methods**

- **GUI:** View details in the _Cluster Status_ pane.
- **CLI:** Use `diagnose ha status` for the same information.
### **Information Displayed**
- **Role:** Indicates each cluster member’s role.
- **Serial Number:** Lists serial numbers of all members.
- **IP:** Shows IP addresses of all cluster members and the host.
- **Host Name:** Displays the HA cluster’s host name.
- **Uptime/Downtime:** Shows operational time for each member.
- **Initial Logs Sync:** Indicates status of initial log synchronization.
- **Configuration Sync:** Displays configuration synchronization status.
- **Message:** Lists any relevant status or error messages.

---

### FortiAnalyzer HA Failover and Rejoin Behavior 

#### **Primary Unit Failover Selection**
- Each cluster unit has a **priority (1–120)**; default is **100**.
- If the **primary fails**, the **unit with the highest priority** becomes the new primary.
- If priorities are **equal**, the **unit with the higher primary IP address** is selected.
#### **Cluster Rejoin and New Unit Rules**
- **New secondary unit (higher priority or IP)** → **Does not preempt** the current primary.
- **New primary-preferred unit joining** → **Preempts and replaces** the current primary.
#### **Reason for Preemption**

- Units with the **preferred role = primary** have:
    - **Preempt enabled** in the _keepalived_ configuration.
    - **Priority boosted by 120**, ensuring they can reclaim the primary role.

---
### **FortiAnalyzer HA Load Balancing 

**Purpose:**  
Enhances performance and responsiveness in High Availability (HA) clusters through synchronized log sharing.
**Key Features:**
- **Reports:**
    - Report generation tasks are distributed across HA units in a **round-robin** manner.
    - Generated reports are **synchronized** across all units for visibility.
- **FortiView:**
    - **Processing load** for FortiView widgets is shared among cluster units to optimize output generation.

---

# To upgrade FortiAnalyzer HA cluster firmware:

1. Log in to each secondary device
2. Upgrade their firmware
3. Wait for the upgrades to complete and verify that all secondary devices joined the cluster
4. Verify that logs on all secondary devices are synchronized with the primary device
5. Upgrade the primary device

---
# Use the following CLI commands to diagnose HA:

+ `diagnose ha status`(Shows HA status)
+ `diagnose ha stats` (Shows HA statistics)
+ `diagnose ha dump-datalog` (Dump HA data log)
+ `diagnose ha failover` (Run on master, force HA failover)
+ `diagnose ha force-cfg-resync` (Force HA to re-sync configuration) 
+ `diagnose ha load-balance` (Shows HA load balance status)
+ `diagnose ha restart-init-sync` (Run on master, restart HA initial sync)