Cloud Interconnect is a Google Cloud service that enables you to establish a **private, high-performance, and secure connection** between your on-premises infrastructure and Google Cloud.

### Key Features and Function

- **Dedicated Connection:** Cloud Interconnect provides a **direct physical connection** between your on-premises network and Google’s network. This direct link offers **higher bandwidth and lower latency** compared to internet-based connections.
- **Hybrid Cloud Environments:** It is commonly used for **hybrid cloud environments**, where organizations maintain part of their infrastructure on-premises and part in Google Cloud.
- **Bypassing the Internet:** The service **bypasses the public internet**, which results in more reliable and consistent network performance and enhanced security.
- **Comparison:** Cloud Interconnect is conceptually similar to Azure ExpressRoute and AWS Direct Connect.

### Components and Configuration

The key features and components associated with Cloud Interconnect include:

- **VLAN Attachments:** These are **logical connections** that map the physical interconnect to your Virtual Private Cloud (VPC) networks in Google Cloud.
- **Cloud Router:** This manages **dynamic route advertisement** between your on-premises network and Google Cloud, ensuring efficient and automated traffic routing.
- **Redundancy:** You can configure Cloud Interconnect with **redundancy** to guarantee high availability and failover capability in case of network problems.

### Role in Fortinet Security Architectures

In Fortinet hybrid cloud deployments, Cloud Interconnect is used for securing traffic between on-premises data centers and Google Cloud.

- **Security and Inspection:** FortiGate can provide **inline Intrusion Prevention System (IPS) inspection** and enforce access policies for Cloud Interconnect connections.
- **Avoiding Direct Flow:** To ensure all traffic is inspected, you should **avoid placing VLAN attachments directly in the workload VPCs**. Instead, the VLAN attachments should be placed in an external VPC that connects exclusively to a FortiGate cluster.
- **Routing Traffic:** Traffic between Cloud Interconnect and the workload VPCs is typically routed through two internal passthrough network load balancers (one on the external side and one on the internal side of the FortiGate cluster) using **custom routes**.
- **Hybrid Traffic Flow Example:**
    1. A packet from the on-premises network is routed to the Cloud Interconnect link using **custom BGP advertisements** configured on the Cloud Router.
    2. The packet reaches the external VPC and is forwarded to the active FortiGate cluster member via a custom static route.
    3. After inspection, the packet is sent to the internal VPC network and reaches its destination in a peered network.
    4. The return packet reverses this process, routing through the internal network load balancer, passing through the active FortiGate cluster member, and finally returning to the on-premises network via Cloud Interconnect.