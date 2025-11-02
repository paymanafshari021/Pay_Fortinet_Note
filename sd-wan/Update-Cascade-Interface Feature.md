# Update-Cascade-Interface Feature

- The **`update-cascade-interface`** feature is **enabled by default** but only functions when at least one **cascade interface** is defined.
- Used to manage interface behavior based on SD-WAN member status.
- **If all SD-WAN members are dead:** FortiGate **shuts down alert interfaces** (e.g., due to degraded performance).
   - **If at least one member is alive:** FortiGate **brings up alert interfaces**.
    #### **Purpose**
- Ensures traffic behind alert interfaces is **rerouted through another device** if FortiGate **cannot forward WAN traffic**.
- Cascade interfaces are **defined globally** for the SD-WAN instance.
- Detection and actions are **activated per performance SLA** (e.g., for underlay links only).
- Focus on **critical interfaces** (e.g., LAN interfaces providing WAN access).
- Shutting down such interfaces can **trigger failover** via mechanisms like **VRRP**.
#### **Example**
- A company uses a **5G backup device** (not connected to FortiGate).
- A performance SLA monitors **ISP links (port1 and port2)**, with **`update-cascade-interface`** enabled to **shut down port5** when links fail.
    
    ![Text](attachments/Pasted_image_20251101141635.png)

    ![](attachments/Pasted_image_20251101141915.png)
