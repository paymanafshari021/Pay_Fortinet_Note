# SD-WAN Overlay Design

## Overlay Tunnel Design in Fortinet SD-WAN

#### Recommended Tunnel Type
- Fortinet SD-WAN supports IPsec, GRE, and IP-in-IP tunnels.
- **IPsec** is the most commonly used and officially recommended protocol for SD-WAN overlay tunnels.
#### Static vs. Dynamic IPsec Tunnels
- **Static tunnels**:
  - Commonly used for full-mesh topologies.
  - Provide stability.
  - Require manual configuration on both ends for each new tunnel.
  - Rarely used in SD-WAN due to poor scalability.
- **Dynamic IPsec tunnels**:
  - Recommended for hub-and-spoke topologies.
  - Highly scalable — spoke configuration remains unchanged when adding new spokes.
#### Tunnel Interface IP Address Configuration Options
- Two available methods:
  - IKE standard **mode-cfg**
	  - Administrator defines a range of IP addresses on the hub
	  - Spoke receives the tunnel interface IP address from the hub
  - Fortinet proprietary **exchange-interface-ip** attribute
	  - Administrator manually assigns the IP address on the spoke and hub sides
---
![[11.png]]
## IPsec Dynamic Tunnel Establishment (Dial-Up VPN)
- Enables branches to establish IPsec tunnels with a central server without pre-configuring every remote endpoint.
- Common in **hub-and-spoke topologies**.
#### Key Components
- **Dial-up Server**: Hub FortiGate that accepts dynamic connections and assigns virtual IP addresses to clients.
- **Dial-up Client**: Branch FortiGate that initiates the tunnel and receives an assigned IP.
#### IKE Mode Config (Mode-Cfg)
- IKE standard method for dynamically assigning virtual IP addresses to dial-up clients.
**Server-side activation (CLI)**:
**IP assignment sources on server**:
- IP address range
- Named firewall address group
- DHCP server
- RADIUS server
```bash
config vpn ipsec phasel-interface
edit HUB1-VPN1
set type dynamic
set mode-cfg enable
set ipv4-start-ip 192.168.1.65
set ipv4-end-ip 192.168.1.124
set ipv4-netmask 255.255.255.192
end
end
```
**Client-side configuration (CLI)**:
- Default tunnel type: static (dial-up)
- Accept assigned IP on IPsec interface:
```set assign-ip enable```
- Enable mode-cfg:
```set mode-cfg enable```
```bash
config vpn ipsec phasel-interface
edit HUB1-VPN1
set type static
set mode-cfg enable 
set assign-ip enable # Accept the IP address assigned by the server
end
end
```
#### SD-WAN Overlay Orchestrator Behavior
- Automatically configures hubs as dial-up servers.
- Assigns IPs to clients from the administrator-defined range.
- Configures branches as dial-up clients.
---
![](attachments/12.png)
## IKE Mode Config vs. IPsec Exchange Interface

- **IKE Mode Config** (default in examples):
  - Simple configuration
  - Automatically assigns spoke overlay IP addresses during tunnel negotiation
  - Drawback: Cannot assign fixed (static) IP addresses to spokes; addresses depend on pool availability

- **IPsec Exchange Interface Feature** (alternative):
  - Allows manual assignment of fixed IP addresses on both hub and spoke overlays
  - During IKE negotiation, gateways exchange these pre-configured addresses
  - Enables predictable, static overlay IPs useful for monitoring and identification
  - Addresses can be configured at:
    - Interface level (example uses this on spoke)
    - VPN tunnel parameter level (example uses this on hub)
  - Supports IPv4, IPv6, or both
### Key Recommendations and Notes

- With BGP on loopback design: Fortinet recommends setting the tunnel (loopback) IP address in the VPN tunnel definition
- Configuration changes (hub and spoke):
  - Same commands on both sides except:
    - Assign different local overlay IPs
    - On spoke only: explicitly define the hub’s overlay IP address, using the full overlay subnet mask (e.g., /24, not /32) for proper next-hop resolution with IBGP route reflection
### Important Limitation

- Uses a Fortinet-proprietary IKE attribute to exchange addresses
- Works only when both endpoints are FortiGate devices (unlike standard IKE mode config)