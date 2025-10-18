## Azure Virtual Network (VNet)

The Azure Virtual Network (VNet) is described as the fundamental building block of Azure networking. The VNet service securely connects Azure resources to each other.

- Networking Solution The Azure Virtual Network offers a flexible and scalable networking solution necessary for deploying and managing resources in Azure. It is one of the essential networking services provided by Azure.
- Isolation and Representation A VNet is a logically isolated network that you can create in Azure. It is a Layer 3 overlay network that essentially represents your own network in the cloud. It is used for creating isolated private networks.
- Structure A VNet must include one or more subnets. Subnets are used to further segment the network and help organize and manage resources within the VNet.
- Connectivity A VNet can be connected to your on-premises networks if needed. Connectivity between VNets can be achieved using VNet peering, Global VNet peering, Azure VPN Gateway, ExpressRoute, Virtual WAN, or Load Balancer.
- Addressing It is possible for a VNet to have more than one address space assigned to it. The system supports both dynamic assignment (where a DHCP server automatically allocates addresses) and static assignment (where the address is manually specified and reserved).
- Communication Basis Communication between Virtual Machines (VMs) or other resources within an Azure VNet is based on their IP addresses and routing, rather than MAC addresses and Layer 2 switches, which is typical in a traditional on-premises network.

The ability to connect on-premises networks to Azure VNets securely is facilitated by services like the Azure VPN Gateway and Azure ExpressRoute.

Azure handles Layer 2 networking concepts, including ARP traffic, differently than traditional on-premises networks, utilizing its underlying network infrastructure to manage these processes.

## Layer2 - ARP Traffic in Azure

Azure does not have traditional layer 2 networking.

- Communication between Virtual Machines (VMs) or other resources within an Azure Virtual Network (VNet) is based on their IP addresses and routing, rather than on MAC addresses and Layer 2 switches.
- Azure cloud computing environments generally do not use broadcast or multicast traffic; only unicast traffic is allowed.
- The implication of this abstraction is that there is no traditional layer 2 traffic, which affects features like FortiGate clustering protocol, gratuitous ARP, and instant IP failover.
- Furthermore, Azure doesn't allow layer 2 modes, such as transparent mode or virtual wire.

### Management of ARP Traffic

Azure manages all ARP traffic internally using a centralized service to maintain the underlying networking infrastructure.

- The Azure underlay network manages ARP traffic through a service (referred to as the SDN virtual router) that performs both switching and routing operations, depending on the destination address.
- When a VM (for example, with IP address 10.0.1.5) makes an ARP request, the SDN virtual router receives it and replies with its own MAC address (12:34:56:78:9A:BC).
- This process occurs for all destinations.
- If you examine the ARP table on the VM, you will see the same MAC address for all the neighbors, demonstrating that all VM traffic must pass through the SDN virtual router.
- When a VM wants to communicate with another VM or with the internet, it generates a unicast packet directed to the SDN virtual router's MAC address (12:34:56:78:9A:BC).
- The SDN virtual router then processes this traffic, adjusting the MAC address according to the original destination.
- It is noted that the SDN virtual router does not respond to ICMP requests.

The Azure cloud environment introduces specific networking restrictions and abstractions compared to traditional on-premises networks, primarily due to the lack of traditional Layer 2 networking.

### Core Layer 2 and Traffic Restrictions

- No Traditional Layer 2 Networking: Azure does not have traditional layer 2 networking. Communication between Virtual Machines (VMs) or other resources within an Azure Virtual Network (VNet) is based on their IP addresses and routing, rather than on MAC addresses and Layer 2 switches.
- Traffic Type Limitation: Cloud computing environments do not use broadcast or multicast traffic; only unicast traffic is allowed.
- Unsupported Layer 2 Features: Since there is no traditional Layer 2 traffic, features that rely on it are restricted, including FortiGate clustering protocol, gratuitous ARP, and instant IP failover. Azure also doesn't allow layer 2 modes, such as transparent mode or virtual wire.

### ARP and Underlay Network Abstraction

Azure manages low-level traffic like ARP internally through its underlying network infrastructure. Administrators lack direct access to this underlay network.

- SDN Virtual Router: Azure manages all ARP traffic through a centralized service (referred to as the SDN virtual router) that handles both switching and routing operations.
- ARP Behavior: When a VM makes an ARP request (e.g., VM 10.0.1.5), the SDN virtual router receives the request and replies with its own MAC address (e.g., 12:34:56:78:9A:BC).
- Traffic Path: This process occurs for all destinations, meaning that if you check the ARP table on the VM, you will see the same MAC address for all neighbors. Consequently, all VM traffic must first go through the SDN virtual router, which then processes the traffic and adjusts the MAC address according to the original destination.
- ICMP Restriction: Notably, the SDN virtual router does not respond to ICMP requests.

### IP Configuration Restriction

- IP Definition Requirement: An instance receives traffic only if the IP address is defined in Azure. If administrators configure static or virtual IP addresses on the VM, they must make sure that those IP addresses are also configured in Azure.

Despite these restrictions, connectivity and security requirements can still be met using Azure networking components that deliver a secure and scalable network in the cloud. For instance, administrators must configure User-Defined Routes (UDRs) to override default routing behavior, often to force traffic (such as internet-bound traffic) to a Network Virtual Appliance (NVA) like a FortiGate VM for inspection.

## Routing

Azure provides several routing capabilities and components to control and manage network traffic both within and between resources such as Virtual Machines (VMs) and Virtual Networks (VNets).

### Routing Tables and Custom Routing

- Routing Table: This feature is used to control the flow of network traffic within a VNet or between VNets.
- Default Routes: Azure provides default routing capabilities, but administrators can implement custom routes to override this behavior.
- User-Defined Routes (UDRs): UDRs allow administrators to define custom routing tables to override the default routing behavior. They function similarly to policy routes on FortiGate.
- Application of UDRs: UDRs are typically configured to force traffic to a specific destination, such as routing internet-bound traffic from a VM through a Network Virtual Appliance (NVA) like a FortiGate VM for inspection. They can also be configured to inspect traffic traveling between subnets.

### Border Gateway Protocol (BGP)

- BGP Use Cases: Border Gateway Protocol (BGP) is a separate networking concept that can be used alongside UDRs to control and manage traffic. BGP can be utilized in two main scenarios: ExpressRoute and Virtual Network Gateway.
- Advanced Routing: By combining UDRs and BGP, administrators can achieve more advanced routing configurations and control over network traffic.

### Route Priority

It is crucial to understand the route priority in Azure when more than one route attempts to control traffic for the same network prefix.

- Most Specific Route Wins: The most specific route always wins. For example, a system route for 10.0.3.0/24 would precede a BGP route for the broader 10.0.0.0/16 network.
- Priority Hierarchy (if specificity is equal): If all the routes in the route table are equally specific, the preferred route is determined by the following hierarchy:

1. User-Defined Route (UDR)
2. Border Gateway Protocol (BGP)
3. System Routes

### Azure Route Server

Azure recently introduced the Azure Route Server to simplify dynamic routing.

- Function: This service allows Network Virtual Appliances (NVAs), such as a FortiGate VM, to exchange BGP routing information directly with the Azure Software Defined Network (SDN).

## Azure Public IP addresses

Azure Public IP addresses are a critical component of Azure networking, allowing resources hosted in the cloud to communicate with internet resources.

### Core Functionality and Mechanism

- Inbound Communication: Azure public IP addresses enable resources on the internet to communicate inbound to Azure resources.
- VM Connectivity: If a Virtual Machine (VM) has a public IP address assigned to it, that VM uses the public IP address to connect directly to the internet. Additionally, public clients can connect directly to any services running on that VM using its public IP address.
- NAT Entry: The public IP address of a VM exists as a Network Address Translation (NAT) entry on the Azure fabric that is mapped to the VM.

### Public IP Address SKUs (Stock Keeping Units)

Public IP addresses can be created with one of two SKUs: Basic or Standard.

|                         |                                                                         |                                                                                       |
|-------------------------|-------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| Feature                 | Basic Public IP Address                                                 | Standard Public IP Address                                                            |
| IP Version & Type       | Can be static or dynamic for IPv4, and dynamic for IPv6.                | Can be static only.                                                                   |
| Security Status         | Open by default.                                                        | Secure by default.                                                                    |
| Security Implementation | Requires Network Security Groups (NSGs) to be used to restrict traffic. | Requires the configuration of NSGs to allow inbound traffic.                          |
| Upgradeability          | Can be upgraded to Standard for IPv4.                                   | N/A                                                                                   |
| Advanced Features       | Does not inherently support advanced features listed for Standard.      | Supports extra features like availability zones, routing preference, and global tier. |

### Integration Requirements

A notable requirement is that a public IP SKU must match the SKU of the load balancer with which it is used. For instance, a public load balancer may use one or more public IP addresses.

## Azure Load Balancer

The Azure Load Balancer is a key network component designed to manage incoming network traffic to achieve high availability and scalability for your services and applications.

### Core Functionality and Performance

- Traffic Distribution: Azure Load Balancer helps distribute incoming network traffic across multiple back-end resources, such as Virtual Machines (VMs) or back-end services.
- High Availability and Scalability: By distributing traffic, the Load Balancer can scale your applications and create high availability for your services, optimize performance, handle increased workloads, and improve fault tolerance.
- Performance Metrics: It provides low latency and high throughput and can scale up to millions of flows for all TCP and UDP applications.
- Supported Scenarios: Azure Load Balancer supports both inbound and outbound scenarios.

### Traffic Management Capabilities

You can configure Azure Load Balancer to load balance various types of traffic:

- Internet Traffic: Incoming internet traffic directed to VMs.
- VNet Traffic: Traffic flowing between VMs within a Virtual Network (VNet).
- Cloud Services Traffic: Traffic between VMs in cloud services.
- Cross-Premises Traffic: Traffic between on-premises computers and VMs in a cross-premises virtual network.
- Forwarding External Traffic: It can also be configured to forward external traffic to a specific VM.
- ICMP Support: Inbound ICMPv4 pings are supported on front ends, which allows for troubleshooting network traffic between the Load Balancer and the client device.

### Load Balancer SKUs (Basic vs. Standard)

Azure provides different types of load balancers:

- Standard Load Balancer (Recommended): This SKU is recommended as it includes additional features and offers a higher Service Level Agreement (SLA).
- It offers a 99.99% SLA.
- It can load balance traffic across multiple availability zones.
- It includes additional features like HA ports (when used as an internal balancer) and HTTPS health probes.
- Basic Load Balancer: This SKU can only load balance traffic inside the availability zone.

A public load balancer uses one or more public IP addresses, and a crucial requirement is that a public IP SKU must match the SKU of the load balancer with which it is used.

### Advanced Features

- Direct Server Returns (Floating IP): This is an Azure feature that prevents Destination NAT (DNAT) from being translated. When this feature is used, traffic received by the destination VM must reply directly to the source IP address; the destination VM does not send the reply traffic back to the load balancer, as the load balancer only redirects traffic.
- Back-End Pool Members: For back-end pool members, you can add individual VMs, a scale set, or an availability set. Any devices added to the availability set are automatically included as target members of the load balancer.

## Virtual Machine Scale Sets (VMSS)

Virtual Machine Scale Sets (VMSS) are a powerful Azure resource designed to manage and scale groups of identical virtual machines.

### Definition and Purpose

- Single Entity Management: Virtual Machine Scale Sets (VMSS) are an Azure resource that allows you to create and manage a group of identical VMs as a single entity.
- Scalable Solution: VMSS provides a scalable and highly available solution for deploying and managing applications that require automatic scaling based on demand.
- Benefits: VMSS offers several benefits, including flexibility, automation, simplified management, customization, high availability, and autoscaling, enabling you to build resilient and efficient solutions.

### Autoscaling Functionality

- Dynamic Adjustment: The autoscaling capability of VMSS allows you to dynamically grow and shrink the number of VMs (such as FortiGate VMs) to match current traffic and performance requirements.
- Capacity Control: You can set a minimum and maximum number of devices and scale out as needed.
- Key Benefits of Autoscaling: The main benefits derived from using autoscaling are fault tolerance, availability, and cost management.

### Integration with Networking Components

- Load Balancer Integration: You can add a scale set to the list of back-end pool members for an Azure Load Balancer, along with individual VMs or an availability set. Devices added to an availability set are automatically included as target members of the load balancer.

## Azure VPN Gateway

The Azure VPN Gateway, also known as the Virtual Network Gateway, is a crucial networking service or feature provided by Microsoft Azure. Its primary purpose is to establish **secure and reliable connectivity** for integrating various network environments.

### Primary Functions and Hybrid Cloud Support

The Azure VPN Gateway facilitates two main types of connectivity: connecting internal networks to Azure, and connecting Azure virtual networks to each other.

**A. Connecting On-Premises Networks (Hybrid Cloud)** The VPN Gateway allows you to securely connect your on-premises (internal) networks to Azure.

- **Secure Extension:** This feature enables secure communication and provides a **seamless extension of your on-premises network to Azure**.
- **Hybrid Scenarios:** It facilitates **hybrid cloud scenarios**, allowing you to securely access resources and services deployed in Azure as part of your overall infrastructure.
- **Reliability:** The use of VPN gateways establishes secure and reliable connectivity between your on-premises network and Azure VNets.

**B. Connecting Virtual Networks (VNet-to-VNet)** VPN gateways can also be used to connect two Azure Virtual Networks (VNets) to each other.

- Azure VPN Gateway is one of the two main methods available to achieve connectivity between different VNets (the other being VNet peering).

### Types of VPN Connections Supported

The gateway supports different VPN approaches to connect resources across boundaries:

| Connection Type   | Description & Characteristics                                                                                                                                                                                                                                                                                                                                                                                                   |
|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Site-to-Site VPN**  | Established between your on-premises VPN device and an Azure VPN Gateway. This connection uses an **IPSec/IKE VPN** to provide encrypted communication over the internet. This type enables any authorized on-premises resource to access a VNet. The latency is generally **unpredictable** because the traffic must traverse the public internet.                                                                                     |
| **Point-to-Site VPN** | Established between a single PC connected to your network and a VNet. This is ideal for developers or when you are just starting with Azure, as it requires few or no changes to your existing network. The connection uses the **Secure Socket Tunneling Protocol (SSTP)** to provide encrypted communication over the internet. Similar to site-to-site, the latency is **unpredictable** because the traffic traverses the internet. |

*(Note: A third option, Azure ExpressRoute, provides a dedicated, private connection that does not traverse the internet, resulting in predictable latency, but this connection is established through an ExpressRoute partner, not solely via the VPN Gateway itself.)*

### Technical Requirements and Integration

When configuring the VPN Gateway, specific rules and protocols apply:

- **Subnet Requirement:** VPN gateways must always connect to a special subnet called **GatewaySubnet**, and this name is mandatory.
- **VNet-to-VNet Setup:** To connect two VNets, you must create a VPN gateway in each VNet. The connection is finalized by specifying the two VPN gateways and configuring a **shared key**.
- **Routing Protocols:** The Virtual Network Gateway is one of the two main scenarios where the **Border Gateway Protocol (BGP)** can be used, alongside ExpressRoute. BGP can be implemented to override the default routes Azure creates.
- **FortiGate Integration:** Azure VPN Gateway can be configured on one side of a connection while a Network Virtual Appliance (NVA) like FortiGate is configured on the other side.

## Network traffic filtering

Network traffic filtering in Azure is crucial for enforcing security policies and controlling the flow of traffic within and between resources. Azure offers several native services and features to achieve this.

The sources identify the following main options used to filter network traffic, particularly between subnets:

### 1. Network Security Groups (NSGs)

**Network Security Groups (NSGs)** are Azure's built-in layer 3 (IP) and layer 4 (TCP/UDP) **stateful firewall service**. They provide a foundational layer of network security allowing you to define fine-grained network access controls.

Key features of NSGs include:

* **Rules:** NSGs are a list of access control rules that either permit or deny traffic based on various criteria. These security rules allow or deny **inbound network traffic** to, or **outbound network traffic** from, Azure resources based on criteria such as source and destination IP addresses, ports, and protocols.
* **Statefulness:** NSGs are stateful, meaning bidirectional policies are not required.
* **Application:** NSGs can be applied either at the network interface card (NIC) level or at the full subnet level.
* **Limitations:** NSGs only work if a resource is connected to a Virtual Network (VNet); they do not work for other resources like Platform-as-a-Service (PaaS) services.
* **Security Implications:** Misconfigured NSGs might inadvertently block or allow unwanted traffic. NSGs are required to restrict traffic for Basic public IP addresses, which are open by default. Standard public IP addresses, conversely, are secure by default and require NSG configuration to allow inbound traffic.

### 2. Azure Firewall

Azure Firewall is a **managed network security service** offering a fully stateful firewall solution.

Characteristics of Azure Firewall include:

* **Functionality:** It provides centralized network traffic filtering and threat protection for Azure resources. It is designed with built-in high availability and unrestricted cloud scalability.
* **Management:** Administrators can create, enforce, and log application and network connectivity policies, and the service is fully integrated with Azure Monitor for logging and analytics.
* **SKUs:** Azure Firewall is available in Basic, Standard, and **Premium** SKUs.
* **Premium Features:** The Premium SKU is distinct because it offers **Advanced Threat Protection features**, such as fully managed Intrusion Detection and Prevention System (IDPS), URL filtering, and inbound/outbound TLS termination.

### 3. Network Virtual Appliances (NVAs)

Network virtual appliances (NVAs) are virtual machines running specialized networking software, such as FortiGate or FortiWeb, which provide advanced network functionality and services.

NVAs can be used to implement network traffic filtering and enhance network security within Azure deployments. You can use an NVA to filter traffic between VNets, as well as traffic to and from the internet.

NVAs can be customized for filtering by being configured to:

* Act as firewalls to filter inbound and outbound network traffic based on rules and policies.
* Incorporate IDS/IPS capabilities to detect and prevent network-based attacks.
* Include web application firewall (WAF) functionality to protect against common web-based attacks.
* Perform Network Address Translation (NAT).
* Act as load balancers or offload SSL/TLS encryption and decryption processes.

NVAs, such as **FortiGate VMs**, provide advanced network security and next-generation firewall capabilities. They are typically used in conjunction with **User-defined routes (UDRs)** or Border Gateway Protocol (BGP).

### Controlling Network Traffic Flow

While Azure provides default routing capabilities, administrators often need to explicitly force traffic through a specific filtering resource:

* **Default Traffic:** By default, all VMs have access to the internet through an Azure-managed Source Network Address Translation (SNAT) service. If a VM has a public IP address, it connects directly to the internet using that address.
* **Using UDRs for Inspection:** To force traffic to a Network Virtual Appliance (such as a FortiGate VM) for inspection, an administrator must configure **User-defined routes (UDRs)**. UDRs allow custom routing tables to override the default routing behavior.
* **Inspection Scenarios:** UDRs can be used to force traffic destined for the internet or traffic moving between subnets to go through the security device first for inspection.

---

# IP Forwarding

**When deploying a network device, such as FortiGate VM, in Azure, IP forwarding is a critical setting for the virtual network card.**
Here is a detailed explanation of IP forwarding settings in the context of Fortinet solutions in Azure:
### **Purpose and Necessity**
- **Traffic Forwarding Requirement:** You **must enable IP Forwarding** for any network interface attached to a VM that is intended to forward network traffic to an address other than its own.
- **Azure Check Bypass:** This setting is essential because it __prevents Azure from checking the source and destination for a network interface__, allowing the security appliance (like FortiGate) to operate correctly.
- **Source IP Generation:** With IP forwarding enabled at its internal interface, a __FortiGate VM is able to generate traffic using a source IP address that is different from the IP address assigned to the virtual network interface.__
### **Spoofing Prevention**
- **Disabled Status Consequence:** If IP __forwarding is disabled__, the packets are identified as **__spoofing packets__**.
- **Action Required:** To __prevent this spoofing situation__, you must verify that IP forwarding is enabled on the appropriate network interfaces.
### **Deployment Status**
- **Default Behavior:** __IP forwarding is enabled by default__ when you deploy your Fortinet VM either __from Azure Marketplace or by using a template obtained from Fortinet GitHub.__
## Before deploying a FortiGate VM in Azure
Before deploying a FortiGate VM in Azure, you must consider several important aspects related to licensing, support, feature support, networking requirements, and performance.
The key aspects to consider include:
### 1. Licensing Model Selection
- **Choose the correct licensing model** __because the licensing model **cannot be changed** after the FortiGate VM is deployed__. To __change the license__ type (between Bring Your Own License (BYOL) and Pay-As-You-Go (PAYG)), you must __deploy a new instance of FortiGate__.
- **__BYOL and PAYG are not interchangeable__**__.__
### 2. FortiGate Features and Support
- **Virtual Domains:** Note that FortiGate using the **PAYG** license model does **__not support virtual domains__**. This must be considered when deciding which configuration to implement.
  - **__VDOM support__** is **available only for __BYOL (Bring Your Own License)__** models on Azure.
  - The "V" license types such as **FG-VMxxV** (for example, VM02V) **do not include VDOM support** unless additional VDOM licenses are purchased.​
  - Default PAYG (pay-as-you-go) marketplace images usually include **one root VDOM** only.
- **Support Level:** Verify that you will receive the **support level needed** because the support level depends on the license type. 
  - If using **PAYG** licensing, you must **register your VM** before you can receive support from Fortinet.
  - If using **BYOL**, the customer gets Fortinet 24/7 support with the enterprise bundle.
### 3. VM Sizing and Networking
- **VM Capabilities:** Verify that the VM supports your needs, including the number of virtual Network Interface Cards (NICs) required.
- **IP Forwarding:** Ensure that the correct **IP forwarding settings** are in place on the virtual network card. 
  - You **must enable IP Forwarding** for any network interface attached to the VM that forwards network traffic to an address other than its own.
  - Enabling IP forwarding prevents Azure from checking the source and destination for a network interface.
  - IP forwarding is **enabled by default** when deploying the VM from Azure Marketplace or a template from Fortinet GitHub.
### 4. Performance
- **Accelerated Networking:** You should **enable accelerated networking** to increase the performance of your VMs.
- **SR-IOV:** Accelerated networking is the term Microsoft uses for single root I/O virtualization (SR-IOV), which significantly improves network performance.
- **Applicability:** This feature is supported by several general-purpose and compute-optimized VMs with two vCPUs without hyper-threading support, but it is **most often used in VMs with four or more vCPUs**.
- ---
## Understand SLAs in Azure
When designing a reliable architecture in Azure, you must factor in resiliency and High Availability (HA). You can achieve different levels of availability depending on the deployment type you choose.
The sources provide the following specific SLAs offered by Microsoft for **Virtual Machines (VMs)**:
### SLAs for Multi-Instance Deployments
The highest availability is guaranteed when deploying multiple instances across availability zones or within availability sets/dedicated host groups:
- **99.99% Connectivity:** Microsoft guarantees VM connectivity to at least one instance at least **99.99%** of the time for all VMs that have **two or more instances deployed across two or more availability zones** in the same Azure region.
- **99.95% Connectivity:** Microsoft guarantees VM connectivity to at least one instance at least **99.95%** of the time for all VMs that have **two or more instances deployed in the same availability set or in the same dedicated host group**.
### SLAs for Single-Instance VMs (Based on Disk Type)
For single instance VMs, the guaranteed connectivity depends on the type of managed disk used:
- **99.9% Connectivity:** Guaranteed for any single instance VM using **premium SSD or ultra disk** for all operating system disks and data disks.
- **99.5% Connectivity:** Guaranteed for any single instance VM using **standard SSD-managed disks** for operating system disk and data disks.
- **95% Connectivity:** Guaranteed for any single instance VM using **standard HDD-managed disks** for operating system disks and data disks.
## Options available for deploying highly available environments
Here are the primary options available for deploying highly available environments with Fortinet in Azure:
### FortiWeb Solutions
**FortiWeb: Active-Active Load Balanced with External/Internal Load Balancer (ELB/ILB)**

This solution is designed for enhanced availability and performance by distributing incoming web traffic across multiple FortiWeb instances.
- **Components:** It deploys an environment that includes an Azure load balancer (with a public IP address) and two FortiWeb VMs added to the load balancer’s back-end pool.
- **Availability Mechanism:** The two FortiWeb VMs communicate with each other, and the Azure fabric uses probes to check system availability.
- **Failover:** If an instance goes down, the load balancer stops distributing traffic to it. If the failed instance was the primary node, the secondary instance immediately assumes the role of the new primary.
- **Configuration:** It is crucial to synchronize configuration and session data between the FortiWeb instances to ensure consistency for client sessions and security settings.
### FortiGate Solutions
FortiGate offers several distinct HA configuration options:
#### A. FortiGate: Active-Passive HA with Fabric Connector Failover
This setup configures a redundant pair of FortiGate devices to ensure failover in case of hardware or software failures.
- **Components:** The deployment includes two FortiGate VMs in an active-passive configuration, a VNet with subnets (external, internal, HA management, HA sync, and one protected subnet), and three public IP addresses (one for data traffic access through the active FortiGate and two for management).
- **Failover Mechanism:** The two FortiGate VMs use **unicast FortiGate Clustering Protocol (FGCP) HA** to synchronize the configuration. On failover, the passive FortiGate takes control and shifts the public IP address and updates internal User-Defined Routing (UDR) via API calls to Azure.
- **Suitability:** This setup is suitable when HA and failover are crucial, offering an efficient response to failures and less complexity in managing active/standby devices.
#### B. FortiGate: Active-Passive HA with ELB/ILB
This solution utilizes Azure load balancers to distribute traffic across the redundant pair of FortiGate devices.
- **Components:** It involves two FortiGate VMs in an active-passive deployment, one external Azure standard load balancer, and one internal Azure standard load balancer.
- **Failover Mechanism:** The FortiGate VMs communicate using **unicast FGCP HA**. The Azure load balancer handles traffic failover using a **health probe** sent to the VMs. Failover times are based on the health probe, typically occurring in a maximum of 15 seconds (two failed attempts per 5 seconds).
- **Suitability:** This setup is ideal for environments with varying traffic loads that require load distribution for efficient utilization and improved performance during traffic spikes. The load balancer directs incoming traffic to the active device while the passive unit remains in standby.
#### C. FortiGate: Active-Active Load Balanced with ELB/ILB
This setup involves deploying multiple independent FortiGate devices to handle traffic simultaneously, enhancing both performance and redundancy.
- **Components:** This uses two independent FortiGate VMs, one external Azure standard load balancer, and one internal Azure standard load balancer.
- **Failover/Redundancy:** The Azure load balancer manages traffic failover using a health probe directed toward the FortiGate VMs.
- **Configuration Synchronization:** Since the VMs are independent, configuration can be synchronized either by using **FortiManager** or through the **system autoscaling setup**.
#### D. FortiGate Autoscale using VM Scale Sets (VMSS)
This solution is designed for highly efficient clustering during high workloads by leveraging Azure's native autoscaling features.
- **Mechanism:** Multiple FortiGate-VM instances form a VMSS and are scaled in and out automatically according to predefined workload levels and traffic demand.
- **HA Features:** Autoscaling utilizes FortiGate native HA features, such as `config-sync`, which synchronizes OS configurations across instances during scale-out events.
- **Management:** An Azure Functions app handles all autoscaling features, including primary and secondary role assignment, license distribution, and failover management.
## FortiGate HA with Fabric Connector and FortiGate HA with ELB/ILB

Your query highlights the two main active-passive High Availability (HA) configurations available for FortiGate solutions in Azure: **Active-Passive HA with Fabric Connector Failover** and **Active-Passive HA with External Load Balancer (ELB)/Internal Load Balancer (ILB)**.
These solutions are designed to ensure service continuity and resilience in case of hardware or software failures.
### 1. FortiGate Active-Passive HA with Fabric Connector Failover
This configuration uses the Fabric Connector to integrate security services and manage failover.
- **HA Mechanism and Suitability:** This setup delivers an HA solution with **less complexity** in managing active and standby devices and provides an **efficient response to failures**. Failover happens when the active device becomes unavailable, and the passive device immediately assumes the active role. This configuration is suitable for scenarios where **HA and failover are crucial**, and the main objective is maintaining service uptime.
- **Deployment Components:** The environment includes two FortiGate VMs in an active-passive deployment. It requires one Virtual Network (VNet) containing one protected subnet and four FortiGate-specific subnets: external, internal, HA management, and HA sync.
- **IP Addresses:** Three public IP addresses are used: the first is for access through the active FortiGate, and the other two are for management access.
- **Failover Process:** The two FortiGate VMs use **unicast FortiGate Clustering Protocol (FGCP) HA** to synchronize their configuration. On failover, the passive FortiGate takes control and issues **API calls to Azure** to shift the public IP address and update the internal User-Defined Routing (UDR) to itself.
- **Traffic Inspection:** Traffic to be inspected is received using UDRs and public IP addresses, and the specific traffic requiring inspection can be customized by adapting the UDR routing.
### 2. FortiGate Active-Passive HA with ELB/ILB

This configuration utilizes Azure load balancers (External and Internal) to manage traffic distribution and failover.
- **HA Mechanism and Suitability:** This solution distributes incoming traffic to the active FortiGate device while the passive unit remains in standby mode. Failover occurs when the load balancer detects the failure of the active device using **health probes**, automatically redirecting traffic to the passive device.
- **Suitability:** This setup is suitable for environments with **varying traffic loads** that require load distribution for efficient utilization and improved performance during traffic spikes.
- **Deployment Components:** The environment includes two FortiGate VMs in an active-passive deployment, one **external Azure standard load balancer** for internet communication, and one **internal Azure standard load balancer** to receive and forward internal traffic.
- **Failover Process:** The FortiGate VMs communicate using the **unicast FGCP HA protocol**. The Azure load balancer manages traffic failover via a **health probe** sent to the VMs. The failover times are based on the health probe, typically resulting in a maximum of **15 seconds** (based on two failed attempts per 5 seconds).
- **Traffic Inspection:** UDRs for the protected subnets are used to redirect traffic to the internal load balancer. The public IP address for data traffic is configured on the Azure load balancer.
## Azure Route Server

The Azure Route Server is an important networking service designed to **simplify and automate the management of routes** in complex network environments. By demonstrating competence in understanding the Azure Route Server, you can successfully integrate it with solutions like the FortiGate Virtual Machine (VM).
### Core Functionality and Design
The Azure Route Server is characterized by the following features:
- **High Availability:** It is designed as a networking service with built-in **high availability**.
- **Central Hub for Routes:** It acts as a central hub that exchanges routes between your virtual networks, your on-premises networks, or peered virtual networks.
- **Control Plane Operation:** Azure Route Server operates exclusively in the **control plane**. It handles decisions regarding how traffic should be routed, including routing protocols and route advertisements.
- **Data Traffic Exclusion:** It is **not designed to process user data traffic**, unlike a traditional network device such as a firewall or load balancer.
- **Topology Agnostic:** The service is network topology-agnostic, meaning it makes no assumptions about the network structure, providing flexibility for routing on irregular networks.
### Benefits and Route Management
The use of Azure Route Server provides significant benefits for route management:
- **Simplification and Automation:** It simplifies the process of managing routes, especially in scenarios involving multiple virtual networks, VPN gateways, and ExpressRoute connections. This improves network performance by streamlining the propagation of network routes.
- **Elimination of Manual Updates:** Using the Azure Route Server **removes the need for manual routing table updates** on your Network Virtual Appliance (NVA) when virtual network addresses change. This also eliminates the need to manually update User-Defined Routes (UDRs) for the announcement of new routes or the withdrawal of old routes.
- **Traffic Direction:** It helps manage routing decisions within the network, ensuring that network traffic is directed along the appropriate paths.
### Technical Requirements and Limitations
Users planning to implement Azure Route Server must be aware of its operational constraints:
- **Deployment Limit:** Only **one route server** can be created in a virtual network.
- **Protocol Support:** Azure Route Server supports **BGP only** as the dynamic routing protocol.
- **IPv6 Support:** Currently, Azure Route Server **doesn’t support IPv6**.
- **Public IP Requirement:** A **public IP address is required** for the Azure Route Server to maintain communication with the back-end service that manages the route server configuration.
- **Limits and Restrictions:** It is important to be aware of limits, such as the number of peers supported and the number of routes each BGP peer can advertise to Azure Route Server. These values can be hard limits or recommendations for better performance, and they may change over time.
### Integration with FortiGate VM and Use Cases
Azure Route Server supports integration with third-party Network Virtual Appliances (NVAs), such as the FortiGate VM:
- **NVA Integration:** If your NVA supports BGP, you can configure it to peer with the Azure Route Server to simplify and centralize routing configurations for traffic passing through the NVA.
- **FortiGate Integration:** FortiGate VM integration with Azure Route Server typically occurs in a **FortiGate active-passive HA configuration**.
- **Routing Consistency:** When integrated, active Fortinet VMs receive consistent and up-to-date routing information.
- **Route Propagation:** When an Azure Route Server is deployed in a Virtual Network (VNet) acting as a hub, the FortiGate VM advertises network prefixes to the Route Server. The Route Server then injects these prefixes so they appear in the effective routes of any virtual machine deployed in the hub VNet or spoke VNet that is peered with the hub VNet (provided the peering is configured to use the remote virtual network's gateway or Route Server).
- **UDRs Not Required (Hub Scenario):** With an Azure Route Server deployed in a VNet working as a hub, **you do not need to use user-defined routes (UDRs)**.
- **Other Use Cases:** Azure Route Server can also integrate Azure VPN Gateway and ExpressRoute, propagating the routing tables of both services.
## Azure Route Server Limitaion

### Key Technical Limitations

- **Deployment Limit:** Only **one route server** can be created in a virtual network.
- **Protocol Support:** The Azure Route Server supports **BGP only** as the dynamic routing protocol.
- **IPv6 Support:** Currently, the Azure Route Server **doesn’t support IPv6**.
- **IP Address Requirement:** A **public IP address is required** for the Azure Route Server. This IP address is needed to maintain communication with the back-end service that manages the route server configuration.
### Operational and Performance Constraints

- **Traffic Handling:** The Azure Route Server is **not designed to process user data traffic**. It operates exclusively in the **control plane**, handling decisions regarding how traffic should be routed (such as routing protocols and route advertisements).
- **Performance Limits:** Users should be aware of limitations concerning the number of **peers supported** and the number of **routes each BGP peer can advertise** to the Azure Route Server. These values can be **hard limits** or **recommendations** for better performance.

It is important to note that the Azure Route Server service and its limits and restrictions **can change over time**. Therefore, it is recommended to refer to the official Azure documentation for the most up-to-date information.