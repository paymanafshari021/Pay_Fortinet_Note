Google Cloud **Routing Tables** manage how network traffic flows between resources within your Virtual Private Cloud (VPC) network and externally.

Key aspects of routing tables include:

- **Default and Custom Routes:** When a VPC is created, a **default static route** to the internet is automatically established. Administrators can create additional **custom routes** and explicitly associate them with VPCs to manage traffic flow.
- **Next Hops (Gateways):** The next hop, or gateway, defined in a route determines where the traffic is directed. This gateway can be specified as a **Compute Engine instance**, a **specific IP address of an instance**, a **VPN tunnel**, or a **forwarding rule of an internal passthrough Network Load Balancer (NLB)**.
- **Traffic Forwarding Mechanism:** Compute Engine instances inherently use the reserved default gateway IP address of their subnet, but the routing table immediately redirects traffic to the specific gateway defined by the route.
- **Granular Control:** You can assign **instance tags** to VM instances, which provides granular control for applying routes that govern traffic direction to and from those specific instances.
- **Role in Fortinet Architectures:** Custom routes are crucial for implementing security policy with FortiGate:
    - For internal (East-West) traffic inspection between VPCs, **custom routes are created** in the hub VPC and applied (exported/imported) to spoke VPCs to force traffic through the FortiGate cluster.
    - For outbound internet traffic (North-South inspection), a **custom route** directs traffic toward the internal load balancer, which then forwards it to the active FortiGate cluster member.
    - In the FortiGate Active-Passive (A-P) SDN connector high availability (HA) architecture, the passive unit sends API commands to Google Cloud during failover to change the **outbound routing table**, updating the next hop for the internal default route to point to the new active unit's private IP address.