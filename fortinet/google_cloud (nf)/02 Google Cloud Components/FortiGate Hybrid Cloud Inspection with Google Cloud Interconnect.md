Hybrid Cloud Inspection specifically addresses the requirement to **secure traffic moving between your public cloud environment (Google Cloud) and your on-premises networks**. This architecture is critical for organizations maintaining a hybrid cloud setup due to regulatory or compliance requirements, necessitating the inspection of traffic traversing this boundary.

In a FortiGate deployment in Google Cloud, hybrid cloud inspection is typically achieved using the **Cloud Interconnect** service combined with FortiGate Virtual Machines (VMs).

### Key Components and Connectivity

1. **Cloud Interconnect:** This Google Cloud service establishes a private, high-performance, and secure connection between your on-premises infrastructure and Google Cloud. It is conceptually similar to Azure ExpressRoute and AWS Direct Connect, bypassing the public internet to offer reliable, low-latency, and highly secure network performance.
2. **VLAN Attachments:** These logical connections map the physical interconnect to your Virtual Private Cloud (VPC) networks.
3. **Cloud Router:** This manages dynamic route advertisement using BGP between your on-premises network and Google Cloud, facilitating automated routing between the two environments.
4. **FortiGate VMs:** FortiGate provides the security enforcement layer, offering capabilities such as inline Intrusion Prevention System (IPS) inspection and access policy enforcement for Cloud Interconnect connections.

### Hybrid Cloud Inspection Traffic Flow

To enforce inspection by the FortiGate, traffic is routed through the firewall cluster using internal passthrough network load balancers (NLBs). The traffic flow is managed to prevent direct connections between the Cloud Interconnect link and the internal workloads:

1. **On-Premises to Cloud Workload (Ingress):**
    
    - A packet originates from the on-premises network and is routed to a Cloud Interconnect link via **custom BGP advertisements** configured on the Cloud Router.
    - When the packet reaches the external VPC, a **custom static route** forwards the traffic to the **active FortiGate cluster member**.
    - The FortiGate inspects the traffic and then forwards the packet to the internal VPC network.
    - The packet reaches its final destination in a peered network.
2. **Cloud Workload to On-Premises (Egress/Return Traffic):**
    
    - The return packet from the internal workload is routed through the internal NLB in the internal VPC.
    - The traffic passes through the active FortiGate cluster member to the external VPC network.
    - Finally, the packet is sent back to the on-premises network through Cloud Interconnect.

### Architectural Best Practices

- **Inspection Point:** FortiGate is responsible for inline IPS inspection and enforcing access policies on the traffic entering and leaving the Cloud Interconnect connection.
- **VPC Placement:** To ensure all traffic is inspected, VLAN attachments should be placed in an **external VPC** that connects only to the FortiGate, avoiding direct connections to the workload VPCs.
- **Routing:** Custom routes are necessary to direct traffic between Cloud Interconnect and the workload VPCs, sending it through two internal passthrough NLBs (one on the external side and one on the internal side of the FortiGate cluster).

By utilizing FortiGate-VM in hybrid cloud architectures, organizations gain more control and visibility, and receive a highly optimized security solution that extends beyond native cloud vendor options.