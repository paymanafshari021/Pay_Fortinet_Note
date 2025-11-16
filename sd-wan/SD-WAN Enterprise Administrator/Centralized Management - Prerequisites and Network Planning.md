# Centralized Management
##  Prerequisites and Network Planning

### **FortiManager and Hub Devices**
- FortiManager is the central management system for Fortinet devices. It must control the main FortiGate devices that act as hubs in your SD-WAN network.
- **Example:** Imagine you have a headquarters in Vienna and several branch offices. The headquarters firewall (FortiGate) will be the hub, and FortiManager will manage it.
### **Device Groups for Branches**
- Branch devices (FortiGate units at remote offices) should be grouped together in FortiManager for easier configuration.
- **Example:** If you have branches in Berlin, Prague, and Budapest, you create a group called “Branch Devices” and add those three FortiGates to it.
### **Network Planning**
Before creating the SD-WAN overlay, you need to plan:
- **Overlay Network Address Space:** This is the IP range used for the SD-WAN tunnels.
    - _Default:_ `10.10.0.0/16`
    - **Example:** All SD-WAN tunnels between hubs and branches will use IPs like `10.10.x.x`.
- **Loopback IP Address Space:** Used for internal routing and identification.
    - _Default:_ `172.16.0.0/16`
    - **Example:** Each branch might get a loopback IP like `172.16.1.1`.
- **BGP AS Number:** A unique number for routing between sites.
    - _Default:_ `65000`
    - **Example:** If your SD-WAN uses BGP, all devices in the overlay region share AS 65000.
### **Topology Type**
- Decide how branches connect to hubs.
- **Example:**
    - _Hub-and-Spoke:_ All branches connect to the hub (common for corporate networks).
    - _Full Mesh:_ Branches can talk directly to each other (less common, more complex).
### **Why This Matters**
You can’t build the SD-WAN overlay until:
- Hubs are managed by FortiManager.
- Branch devices are grouped.
- IP ranges and routing numbers are planned.

### **✅ Checklist**

1. Ensure FortiManager is installed and accessible.
2. Add hub FortiGate devices to FortiManager (physical or model devices).
3. Configure WAN, ISP links, and other interfaces on hub devices.
4. (Optional) Add branch FortiGate devices to FortiManager now or later.
5. Create a device group in FortiManager for all branch devices.
6. Plan network details:
    - Decide topology type (Hub-and-Spoke or Full Mesh).
    - Allocate overlay network address space (default: `10.10.0.0/16`).
    - Allocate loopback IP address space (default: `172.16.0.0/16`).
    - Select BGP AS number (default: `65000`).
7. Verify IP ranges do not conflict with existing networks.
8. Define SD-WAN overlay template in FortiManager:
    - Assign hubs and branch device group.
    - Configure overlay network and loopback IPs.
    - Set BGP AS number and routing policies.
9. Deploy configuration to all devices and validate connectivity.
10. Test failover and performance to ensure SD-WAN works as expected.

---

## Convert Single HUB to Dual HUB

### **Single HUB vs. Dual HUB Topology**
- **Single HUB**: Your SD-WAN network has one central hub that connects all branch offices.
- **Dual HUB**: You add a second hub for redundancy and better performance. If one hub fails, traffic can go through the other.
**Example:**  
Imagine a company with 10 branch offices connected to one main data center (HUB). If that hub goes down, all branches lose connectivity. By adding a second hub, branches can switch to the backup hub automatically.
### **Converting Single HUB to Dual HUB**
- You don’t need to rebuild everything from scratch. You can reuse existing network settings for the second hub.
- This saves time because you keep the same device groups, routes, and objects.
**Example:**  
If your current hub uses IP settings like `192.168.1.x`, you can apply similar settings to the new hub without reconfiguring all branches.
### **Device Groups and Branches**
- Branches are grouped into **device groups** for easier management.
- When you add more devices (branches), you can assign them to the same group.
**Example:**  
If you have branches in Vienna, Graz, and Linz, you can put them in one group called “Austria Branches” and apply the same SD-WAN policies.
### **SD-WAN Manager & Overlay Orchestration**
- **SD-WAN Manager**: A tool in FortiManager that helps configure and manage SD-WAN.
- **Overlay Orchestration**: Automates the creation of tunnels between hubs and branches.
**Example:**  
Instead of manually creating VPN tunnels for each branch, the orchestration feature sets them up automatically.
### **Overlay Template & Region Settings**
- Templates define how your SD-WAN overlay works (authentication, topology, etc.).
- **Region Settings**: Decide which hubs and branches belong to which region.
**Example:**  
You can create a template for “Europe Region” with two hubs (Vienna and Berlin) and assign branches in Europe to it.
### **BGP AS Number**
- **BGP (Border Gateway Protocol)** is used for routing between hubs and branches.
- Each hub needs an **AS number** (Autonomous System number) for BGP.
**Example:**  
Primary hub might use AS number `65000`, secondary hub `65001`.
### **Role Assignment**
- Defines which device acts as **Primary HUB**, **Secondary HUB**, or **Branch**.
- Helps automate failover and routing.
**Example:**  
Vienna = Primary HUB, Berlin = Secondary HUB, Graz = Branch.
### **Why This Matters**
FortiManager makes it easy to scale from a simple single-hub setup to a more resilient dual-hub or multi-hub architecture without starting over.
### **✅ Checklist for Converting Single HUB to Dual HUB Topology**

1. **Review Current Topology**  
    Confirm your existing single HUB setup and note all network settings.
2. **Plan Second HUB**  
    Choose location and hardware for the secondary HUB (e.g., another data center).
3. **Reuse Existing Settings**  
    Apply the same network settings (IP ranges, routes) to the second HUB for consistency.
4. **Update SD-WAN Overlay Template**  
    In FortiManager, edit the overlay template to include Dual HUB topology.
5. **Assign Roles**  
    Set Primary HUB and Secondary HUB roles in the template.
6. **Configure Device Groups**  
    Ensure branches are grouped properly; add new branches if needed.
7. **Overlay Orchestration**  
    Use SD-WAN Manager to automate tunnel creation between hubs and branches.
8. **Set BGP AS Numbers**  
    Assign unique Autonomous System numbers for each HUB (e.g., 65000 for primary, 65001 for secondary).
9. **Test Failover**  
    Simulate HUB failure to confirm branches switch to the secondary HUB.
10. **Document Changes**  
    Record all updates for future troubleshooting and audits.

---

## SD-WAN Member - Installation Target
### **SD-WAN Template**
An SD-WAN template is like a blueprint for configuring multiple devices in your network. Instead of setting up each device manually, you create one template and apply it to many devices.
### **Common vs. Unique Configurations**
Most devices might share the same basic SD-WAN setup (for example, two internet links: ISP1 and ISP2).  
But some devices have extra links, like:
- **Branch A**: Has ISP1 and ISP2 only.
- **Branch B**: Has ISP1, ISP2, and an MPLS link.
- **Branch C**: Has ISP1, ISP2, and a 4G/5G link.
So, not all devices are identical.
### **Installation Targets**
This feature lets you choose **where** a specific SD-WAN member (interface) should be installed:
- You can select **device groups** (e.g., “Main Branches”) or **individual devices**.
> [!CAUTION] 
> ⚠️ If you leave the “Installation Target” empty, the member applies to **every device**.
### **Example from the text**
- The template defines **three SD-WAN members**:
    - **port1 (ISP1)** → applies to all branches.
    - **port2 (ISP2)** → applies to all branches.
    - **port4 (MPLS)** → applies **only** to devices in the “main group” (big branches).
So, when FortiManager installs the configuration:
- Small branches get ISP1 and ISP2.
- Main branches get ISP1, ISP2, and MPLS.

---

## Metadata Variables

- **ADOM-Level Parameters**: Defined at the ADOM level and also available at the Global ADOM level for per-ADOM mapping.
- **Purpose**: Provide flexibility when a parameter needs different values across devices.
#### **Usage**
- Can be applied in:
    - **CLI scripts**
    - **Templates**
    - **Model devices**
#### **Management**
- **Access Location**:  
    _Policy & Objects → Advanced → Metadata Variables menu_
- **Actions Available**:
    - Review
    - Edit
    - Create
#### **Naming Rules**
- Allowed characters:
    - **Letters**
    - **Numbers**
    - **Underscores**

---
## Metadata Variables in SD-WAN Configuration

#### **Purpose of Metadata Variables**
- Allow assigning different values to a setting for each device.
- Useful for configuring SD-WAN members via templates without creating separate templates for each device.
#### **Key Use Cases**
- **Interface Member Setting**:  
    Enables different interfaces on different devices within the same template.
- **Gateway Setting**:  
    Define a metadata variable for gateway IP addresses, which differ across devices.
#### **Example Scenario**
- **Variable `sdwan_port1_gw`**:  
    Assigns unique gateway IP for `port1` on each device (e.g., `192.2.0.2` for branch1, `203.0.113.2` for branch2).
- **Variable `inet3`**:  
    Specifies interface name for SD-WAN member (e.g., branch1 uses `port3`, branch2 uses `port8`).
#### **Performance SLA**
- Health check servers can use:
    - One static IP (same for all devices).
    - One metadata variable (adjusts IP based on device location).
#### **How to Reference Metadata**
- Prefix variable with `$` in configuration.
- FortiManager displays available variables and allows creating new ones via “+” option.
#### **Indicator for Metadata Usage**
- A **magnifying glass with a `$` sign** marks fields where metadata variables can be applied.
#### **How to Use Metadata Variables**
- Enter `$` in the field → a **pop-up menu** appears:
    - **Select** an existing variable.
    - **Create** a new variable by clicking **“+”**.
    - **Edit** an existing variable using the **pen icon** (visible on hover).
#### **Managing Metadata Variables**
- To **review or edit all metadata variables** at the **ADOM level**:
    - Navigate to:  
        **Policy & Objects → Advanced → Metadata Variables**
---
## **Creating Metadata Variables in FortiManager**

- **New Variable Creation**
    - Click **“+”** to create a new metadata variable.
    - A window appears to define:
        - **Variable name**
        - **Values**
        - **Per-device mapping**
- **Default Value**
    - Always set a **default value** for each metadata variable.
    - FortiManager uses this default when:
        - Installing configuration on devices without specific per-device mapping.
- **Per-Device Mapping**
    - Expand the **Per-Device Mapping** menu to assign values for individual devices.
    - Configure either:
        - A **default value**, or
        - A **device-specific mapping** for every applicable device.
> [!CAUTION] 
>  ⚠️ **Important Rule** : FortiManager **cannot install configurations** if:
>  A metadata variable has **no value** (neither default nor per-device mapping).

---

## **Device Database Variables in Jinja Templates**

- **Purpose**: Provide dynamic configurations without defining duplicate metadata variables.
- **Availability**:
    - Natively available on **FortiManager** for each managed device.
    - Supported **only in Jinja scripts**, **not compatible with CLI scripts**.
### **Key Features**
- Direct access to device database variables in Jinja templates.
- Eliminates need for administrator-defined metadata variables.
### **Example Usage**
- **Jinja Script**:
```
    config system global
    set hostname {{DVMDB.name}}
    end
```
- **Common Variables**:
#### Device Database Variables (`DVMDB`)
Use these to access device-specific details:
- **Name**: `{{DVMDB.name}}`
- **Serial Number**: `{{DVMDB.serial}}`
- **OS Type**: `{{DVMDB.os_type}}`
- **Platform**: `{{DVMDB.platform}}`
- **Version**: `{{DVMDB.version}}`
- **Hostname**: `{{DVMDB.hostname}}`
- **Description**: `{{DVMDB.description}}`
#### **System Interface Variables (`intf`)**
Use these for interface-level configurations:
- **Interface Name**: `{{intf.name}}`
- **Allow Access**: `{{intf.allowaccess}}`
- **Type**: `{{intf.type}}`
- **IP Address**: `{{intf.ip}}`
- **Mode**: `{{intf.mode}}`
- **VDOM**: `{{intf.vdom}}`
### **Important Notes**
- FortiManager GUI shows **only metadata variables** when typing `{{`.
- To use device database variables, **manually type them**.
- Full list of supported variables: Refer to **FortiManager Administration Guide**.

---
## Script Types on FortiManager

### **CLI Scripts**
- Use FortiGate CLI commands.
- Reference metadata variables with the **$** symbol.
- Simple to use; **no advanced programming logic**.
### **Jinja Scripts**
- Use **Python-like syntax** for advanced configuration scenarios.
- Reference metadata variables with **double braces: `{{ }}`**.
## Template Groups
- Allow assignment of **multiple CLI and/or Jinja scripts** to managed devices.
- **Mixed script types** are supported.
- Scripts are executed in **top-down order**.

---
## **Firewall Policy and IPsec Interfaces**
- **Policy Package Requirement**
    - Firewall policies for SD-WAN must be grouped into a **policy package**.
    - A policy package can contain multiple firewall policies assigned to one or more managed devices.
#### **Key Rules for SD-WAN Policies**
- Policies must reference **SD-WAN zones**, **not individual members**.
- Other interfaces in the policy are typically **normalized interfaces**.
#### **Normalized Interfaces**
- Allow referencing different interfaces per device or platform.
- Require **mapping rules** to correctly associate normalized names with actual device interfaces.
- Enable sharing of objects (e.g., firewall policies) across multiple devices with varying configurations.
#### **FortiManager Behavior**
- When installing objects with normalized interfaces:
    - Reads mapping rules.
    - Assigns the correct physical interface to each target device.
#### **When Normalization is Required**
- **Required:** If you do **not** plan to use an IPsec interface as an SD-WAN member.
    - Reason: Firewall policies need to reference normalized interfaces for the tunnel to function.
- **Not Required:** If you plan to configure an IPsec interface as an SD-WAN member.
    - Reason: SD-WAN members do not use normalized interfaces.
#### **Firewall Policy Considerations**
- For SD-WAN:
    - Policies must reference **SD-WAN zones**, not individual members.
- For IPsec (non-SD-WAN):
    - After normalizing the IPsec interface, you can reference it in firewall policies.
---
### **Configuring an SD-WAN Zone in Device Manager**
#### **Mandatory Settings**
- **Name**: Assign a name to the zone.
- **Interface Members**: Select one or more interfaces for the zone (at least one required).
    - Zones can be used to steer traffic.
#### **Optional Setting: `service-sla-tie-break`**
- Applies to all SD-WAN rule strategies **except** when using **load balancing**.
- Commonly used with **lowest cost strategy**.
### **Tie-Breaker Options**
- **`cfg-order`**:
    - Default behavior.
    - Selects members meeting SLA in the order they were configured.
- **`fib-best-match`**:
    - Chooses member with the most specific route (longest prefix match).
- **`input-device`**:
    - Ensures overlay **stickiness** (traffic stays on the same overlay if possible).
    - Replies sent through the overlay link that received the incoming flow, if SLA is met.
    - CLI commands:
        - `set input-zone`
        - `set tie-break input-device`
### **FortiManager Behavior**
> [!CAUTION]  
> When creating a zone via **SD-WAN template**, FortiManager automatically creates a **normalized interface**.

---

## **FortiManager Performance SLA Configuration Sections**

- **Header**
    - Configure member health check settings.
    - Specify which members the health check applies to.
- **SLA Target**
    - Define performance requirements for alive members.
    - Used for traffic steering strategies (lowest cost, with/without load balancing).
- **Link Status**
    - Set criteria for alive/dead members.
    - Configure probe interface and acceptable failure rate.
- **Action When Inactive**
    - Define actions when a member’s state changes (alive/dead).
    - Options include updating static routes or toggling alert interfaces.
- **Advanced Options**
    - Configure additional performance SLA settings.

---
## **Health Check Configuration Overview**

#### **Probe Modes**
- **Active**: FortiGate sends periodic probes through the member to monitor health and performance.
- **Passive**: Monitors actual network traffic flowing through the member.
- **Prefer Passive**: Starts in passive mode; switches to active if no **TCP** traffic for 3 minutes.
- **Remote**: Uses SLA information from a remote device.
- **Agent Based**: Collaborates with FortiMonitor agents for application-level performance stats.
#### **Server Configuration**
- For **Active** or **Prefer Passive** modes:
    - Configure up to **two servers** for probes.
>[!CAUTION]
Best practice: Two servers prevent false positives caused by server issues.
#### **Enable Probe Packets**
- Applicable for **Active** or **Prefer Passive** modes.
- **Disabling probes**:
    - FortiGate uses last known metrics but stops monitoring.
    - Recommended only for troubleshooting, as new link quality changes won’t be detected.
#### **Check Interval**
    - Frequency of sending probes to target servers.
    - **Default:** Every 500 ms.
#### **Failure Before Inactive**
    - Number of consecutive failed probes before marking a member as dead.
    - **Default:** 5 failures.
    - Dead members are not used for traffic steering.
- **Restore Link After**
    - Number of consecutive successful probes required to change a member from dead to alive.
    - **Default:** 5 successes.
    - Initially, all members are alive.
- **Probe Timeout**
    - Time to wait for a probe response before considering it failed.