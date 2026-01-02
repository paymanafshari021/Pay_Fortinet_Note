East-West Traffic Inspection refers to the security measures implemented to secure **lateral traffic** moving between internal networks or application tiers within your cloud environment.

This type of inspection architecture is crucial for segmentation and containment within a Virtual Private Cloud (VPC) environment:

### Purpose and Use Cases

- **Securing Internal Movement:** East-West inspection is primarily responsible for securing lateral traffic. It is used to **detect and block lateral movement** in case one application tier or VPC network is compromised.
- **Three-Tier Architectures:** It is a common requirement for traditional **three-tier architecture deployments** (such as presentation, application, and data layers).
- **Control and Visibility:** It can be used to provide **control and visibility** between different internal applications or services.

### Recommended Architecture (Hub-and-Spoke)

The recommended approach for implementing East-West inspection, particularly using FortiGate, is a **hub-and-spoke architecture** utilizing VPC peering.

- **Hub and Spokes:** Each application tier (the spokes) must be placed in its own VPC and connected to a central firewall VPC (the hub) using **VPC peering**.
- **VPC Peering:** Using VPC peering is the Fortinet recommended practice when a FortiGate must protect many networks, as it reduces the network interfaces that must be attached to the FortiGate instance.
- **Efficiency:** This architecture allows for flexibility to secure up to **25 LAN segments using a single internal NIC**, making it a more cost-effective option than multi-NIC (network interface card) deployments.

### Configuration and Traffic Flow

To guarantee that traffic between the spokes passes through the central FortiGate for inspection, specific routing mechanisms are utilized:

1. **Route Implementation:** You must apply custom routes to the spoke VPCs. This process requires using the **Export custom route and Import custom route options** in the peering settings for both the hub and spokes.
2. **Connection Initiation:** A client initiates a connection from one VPC (e.g., Tier 1 VPC) to a destination in another VPC (e.g., Tier 2 VPC).
3. **Traffic Redirection:** This connection matches the imported default custom route, directing the traffic toward an **internal load balancer (ILB)**.
4. **Forwarding and Inspection:** The internal load balancer forwards the traffic to the active FortiGate cluster member, where it is inspected for threats and policy violations.
5. **Forwarding to Destination:** After inspection, the packet is forwarded out the **same internal port** (port2) to the destination VPC (Tier 2 VPC).

If the Google Cloud VPC peering group size limit proves too low for a given deployment, an alternative is to configure FortiGate VMs with multiple internal network interfaces, grouping them into a single zone to simplify firewall management.