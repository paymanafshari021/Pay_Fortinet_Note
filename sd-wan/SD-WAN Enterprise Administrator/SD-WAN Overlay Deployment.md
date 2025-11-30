## SD-WAN Overlay Deployment
![[16.png]]
## IPsec VPN Tunnel Configuration in Hub-and-Spoke Topology on FortiManager

#### Options for Configuration
- Primary method: Use the SD-WAN overlay orchestrator for automated IPsec tunnel setup.
- Alternative method: Manually use predefined IPsec tunnel templates (no orchestrator required).
#### Key Features of Both Methods
- FortiManager applies the same recommended IPsec settings in both cases.
#### Predefined Template Usage
- Templates: **BRANCH_IPsec_Recommended** and **HUB_IPsec_Recommended**
- Administrator only needs to define main parameters.
- Templates automatically add recommended settings covered in the lesson (e.g., tunnel type, net-device).
- Templates can be edited later for fine-tuning and advanced options.
#### ADVPN Support
- Predefined templates also support enabling ADVPN with corresponding recommended parameters in hub-and-spoke topologies.
---
![[17.png]]
## IPsec Templates Settings BGP per Overlay Orchestrator (Default/Recommended)  in FortiManager

FortiManager automatically prepares IPsec templates for **hub-and-spoke topology** with **BGP per overlay** routing.
#### Hub Template Settings
- **Tunnel type**: Dynamic (dial-up server)  
  → Hub accepts connections from spokes with unknown/dynamic IP addresses
- **net-device**: disable  
  → Improves performance optimization due to high number of tunnels
- Configures system interface settings (IP address and allowed remote IP range) on the new VPN interface
#### Branch (Spoke) Template Settings
- **Tunnel type**: Static (dial-up client)  
  → Hub IP addresses are known and fixed
- **net-device**: enable  
  → Required for ADVPN shortcut support; performance impact is minimal due to low overlay count on spokes
#### Common Settings (Both Hub and Branch)
- **network-overlay**: enable
- **network-id**: configured (unique per overlay)  
  → Prevents cross-overlay traffic
**Optional**: Add keepalive settings to both templates as needed.
---
![[18.png]]
## IPsec Templates Settings - BGP on Loopback Overlay Template in FortiManager

**Key Behavior** When "BGP on loopback" is selected in the overlay template, FortiManager automatically optimizes the generated IPsec templates for loopback-based BGP routing.
**Primary Difference in Tunnel IP Addressing**

- **BGP per overlay**: Uses standard IKE mode-config for tunnel endpoint assignment
- **BGP on loopback**: Uses Fortinet-proprietary **exchange-interface-ip** option to exchange loopback IPs as tunnel endpoints

**Resulting IPsec Template Parameters**

**Hub Side (Dial-up Server)**

- Tunnel type: Dynamic
- net-device: disable

**Branch Side**

- Tunnel type: Static (remote IP known via exchanged loopback)
- net-device: enable

These settings are the recommended IPsec configuration when BGP routing runs over loopback interfaces instead of directly over the overlay tunnels.

---
![[19.png]]
## IBGP Configuration for Spokes with BGP Per Overlay

#### Key Characteristics
- Uses **IBGP** (local AS = remote AS = 65000)
- Preferred over EBGP because IBGP preserves the original next-hop, which is essential for proper ADVPN operation
- Supports SD-WAN ECMP; requires `set ibgp-multipath enable` to allow equal-cost multipath for IBGP routes
#### Neighbor Configuration
- Each `config neighbor` entry defines one hub overlay IP:
  - e.g., 192.168.1.61 → Hub overlay over ISP1
  - e.g., 192.168.1.125 → Hub overlay over ISP2
- `interface` setting binds BGP session to the specific SD-WAN overlay interface
- 🎁 `update-source` explicitly sets the source IP (optional/redundant here because `interface` already forces the correct source IP, but recommended for consistency)
#### Purpose of Binding
- Prevents accidental cross-overlay BGP peerings (e.g., ISP1 overlay talking to ISP2 overlay)
- Ensures BGP control plane follows the same SD-WAN path selection as data plane
#### Route Redistribution
- `config redistribute "connected"` injects the local LAN subnet (e.g., 10.0.1.0/24 on port5) into BGP so it is advertised to hub and other spokes
---
![[20.png]]