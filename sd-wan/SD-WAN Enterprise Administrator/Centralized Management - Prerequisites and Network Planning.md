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
