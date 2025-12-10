
#ztp #Zero-touch
# Zero-touch Provisioning 

### **ZTP Workflow**

#### **1. Initial Configuration**
- Define **topology** and prepare configuration elements:
    - Routing
    - Overlay tunnels
    - SD-WAN template
    - Policy objects
    - Policy packages
- Use **SD-WAN overlay template** to simplify tasks.
- These steps are performed **once per deployment**.
#### **2. Site Provisioning**
- Provision sites in **FortiManager**:
    - Specify **device serial number** and **device name**.
- Optionally:
    - Prepare a **CSV file** listing devices and variable values.
    - Upload the file to FortiManager for quick setup.
#### **3. Automatic Configuration**
- When technicians connect sites:
    - **FortiManager auto-recognizes** each site.
    - Loads the **desired configuration** automatically.

![](fortinet-repo/sd-wan/SD-WAN%20Enterprise%20Administrator/attachments/01.png)
![](attachments/02.png)
![](attachments/03.png)
### **Device Blueprint Overview**
- A device blueprint defines parameters for a specific device model.
- Multiple blueprints can exist for the same model.
### **Key Parameters**
- **Automatically Link to Real Device (Auto-Link)**  
    Required for ZTP/LTP.
- **Enforce Firmware Version**  
    Ensures device firmware matches the specified version before other configurations.
- **Enforce Device Configuration**  
    Pushes changes to FortiGate management interface during ZTP/LTP.  
    _Caution:_ Misconfiguration can break connectivity.
- **Pre-Run CLI Template**  
    Runs a chosen template before other provisioning templates.
- **Assign Policy Package**  
    Selects the policy package for the device.
- **Provisioning Templates**  
    Assigns templates or template groups to the device.
### **Important Notes**
>  [!CAUTION] 
>  - Devices in a group inherit all associated templates.
> 	 - **Avoid conflicts:** Do not assign both provisioning templates and group templates to the same blueprint.
> - CSV files can map templates to device serial numbers when multiple blueprints exist for the same model.
---
### **Enforce Device Configuration (Device Blueprint Wizard)**
- **Purpose**  
    Controls FortiManager’s behavior for the interface used by FortiGate to connect during ZTP/LTP.
- **Scope**  
    Applies **only** during Zero Touch Provisioning (ZTP) or Low Touch Provisioning (LTP).
### **Functionality**
- If **enabled**:
    - FortiManager enforces blueprint settings on the connection interface (e.g., alias for `port1`).
    - Allows FortiManager to modify the interface used by FortiGate to connect.
	- Useful for:
	    - Setting interface alias.
	    - Adjusting interface speed.
	- **Warning:** Incorrect configuration can break connectivity between FortiManager and FortiGate.
- If **disabled**:
    - FortiManager does **not** apply alias or interface attributes.
    - Prevents any configuration changes to the connection interface during ZTP/LTP.
### **Important Considerations**
- Misconfiguration (e.g., wrong IP or gateway) can:
    - Break connectivity between FortiGate and FortiManager.
    - Cause ZTP process failure.

![](attachments/04.png)