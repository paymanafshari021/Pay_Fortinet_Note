
# Prerequisites and Network Planning

### **1. FortiManager and Hub Devices**
- FortiManager is the central management system for Fortinet devices. It must control the main FortiGate devices that act as hubs in your SD-WAN network.
- **Example:** Imagine you have a headquarters in Vienna and several branch offices. The headquarters firewall (FortiGate) will be the hub, and FortiManager will manage it.
### **2. Device Groups for Branches**
- Branch devices (FortiGate units at remote offices) should be grouped together in FortiManager for easier configuration.
- **Example:** If you have branches in Berlin, Prague, and Budapest, you create a group called “Branch Devices” and add those three FortiGates to it.
### **3. Network Planning**
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
### **4. Topology Type**
- Decide how branches connect to hubs.
- **Example:**
    - _Hub-and-Spoke:_ All branches connect to the hub (common for corporate networks).
    - _Full Mesh:_ Branches can talk directly to each other (less common, more complex).
### **Why This Matters**
You can’t build the SD-WAN overlay until:
- Hubs are managed by FortiManager.
- Branch devices are grouped.
- IP ranges and routing numbers are planned.