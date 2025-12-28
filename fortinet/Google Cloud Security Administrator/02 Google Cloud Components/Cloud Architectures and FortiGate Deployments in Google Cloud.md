The sources describe three primary types of architectural approaches when adopting cloud services in general, and several specific FortiGate architectures relevant to traffic inspection in Google Cloud.

## Cloud Deployment Architecture Types

Cloud services are offered through different service models based on which resources are managed by the vendor versus the client. The main models of cloud architecture are:

1. **Public Cloud:** These environments are available to any organization and are provided by vendors such as Amazon, Microsoft, and Google.
    - **Owner:** Cloud provider.
    - **Hardware:** Shared.
    - **Connectivity:** Through the internet.
    - **End Users:** Multiple.
    - **Cost:** Lower.
2. **Private Cloud:** These clouds are visible only to the organization that creates them.
    - They provide benefits similar to a public cloud but allow the organization to **maintain ownership of the data and equipment**.
    - A private cloud is essentially an organization's private data center running virtual environments for applications and data storage.
    - **Owner:** Organization.
    - **Hardware:** Dedicated.
    - **Connectivity:** Through a private network.
    - **End Users:** Within the organization.
    - **Cost:** Higher.
3. **Hybrid Cloud:** This infrastructure combines two or more distinct cloud infrastructures (like private, community, or public clouds) that remain unique entities.
    - These entities are bound together by standardized or proprietary technology that enables data and application portability (e.g., cloud bursting for load balancing).
    - A hybrid cloud scenario is responsible for **securing traffic between your public cloud and on-premises networks**.
    - **Owner, Hardware, Connectivity, and End Users:** Mixed.
    - **Cost:** Highest.

## FortiGate Architectures in Google Cloud

FortiGate deployments in Google Cloud utilize specific architectures to protect applications from cyberattacks, generally categorized by the direction of the inspected traffic or the High Availability (HA) approach used:

### 1. Traffic Inspection Architectures

These architectures describe the purpose of the security deployment:

- **North-South Traffic Inspection (Inbound/Outbound):** Responsible for securing inbound traffic originating from the internet and outbound traffic originating from internal workloads.
    - **Inbound:** FortiGate VM inspects all incoming traffic. Incoming connections are evaluated against Google Cloud firewalls, translated using FortiGate Virtual IPs (VIP), evaluated against FortiGate policies, scanned for threats, and then routed to workloads.
    - **Outbound:** The FortiGate VM acts as a **secure web gateway** to protect outbound traffic. Outbound traffic is routed to an internal load balancer via a custom route, inspected by FortiGate, and typically undergoes Source Network Address Translation (SNAT) before exiting the external load balancer.
- **East-West Traffic Inspection (Internal Segmentation):** Responsible for securing lateral traffic between different internal networks.
    - This is a common requirement for **three-tier architecture deployments** (presentation, application, data).
    - It is used to **detect and block lateral movement** if one of the tiers or VPC networks is compromised.
    - The recommended approach is a **hub-and-spoke architecture** where each application tier is placed in its own VPC (spoke) and connected to a central internal firewall VPC (hub) using VPC peering.

### 2. High Availability (HA) Architectures

FortiGate HA architectures provide redundancy and can be deployed in Google Cloud using modified protocols and cloud-native services:

|Architecture Type|HA Protocol/Model|Key Characteristics|
|:--|:--|:--|
|**A-P HA SDN Connector**|Modified FortiGate Clustering Protocol (FGCP) unicast heartbeat|Does **not require a load balancer**, leading to lower deployment cost. It uses Google Cloud APIs to move shared resources (like the elastic public IP address and updating the outbound routing table) to the newly active FortiGate during failover. **Failover is slower and unpredictable** due to serial API calls involved. It supports only two peers in an active-passive configuration.|
|**A-P HA Load Balancer**|FGCP unicast heartbeat|Uses a **"load balancer sandwich"** (external and internal load balancers). Failover is **faster** than the SDN connector architecture because it relies on static configuration and Google Cloud health checks to switch traffic immediately. It generally results in higher costs compared to the SDN connector model.|
|**Active-Active (A-A) HA**|FortiGate Session Life Support Protocol (FGSP)|Required when **more than one FortiGate must process traffic**. It uses load balancers to distribute traffic. FGSP synchronizes sessions across multiple devices for continuity but does **not synchronize configurations**. Flow symmetry is maintained for internal connections via internal load balancers using symmetric hashing.|
|**Auto-Scale**|FortiGate Next-Generation Firewall (NGFW) combined with cloud features|Dynamically scales FortiGate instances out or in based on traffic demand and resource utilization. Uses managed instance groups (MIGs) and load balancers. This solution is cost-efficient and provides elasticity and high availability. Configurations and licenses can be managed through optional components like Cloud Functions and Cloud Storage.|