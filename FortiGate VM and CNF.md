#Fortinet #cloud 

# FortiGate Cloud-Native Firewall (CNF)

FortiGate Cloud-Native Firewall (CNF) is a **Firewall-as-a-Service (FWaaS)** solution provided by Fortinet that is designed to reduce network security operations workloads in public cloud environments.

FortiGate CNF focuses on automation and securing specific cloud network security use cases. It leverages **FortiOS software** to provide consistent availability, scalability, and ease-of-use.

Here is a comprehensive overview of FortiGate CNF based on the sources:

### Core Identity and Delivery

- **Service Delivery:** FortiGate CNF is delivered as a service that scales to demand and does not require customer maintenance. Customers do not need to configure, provision, or maintain any firewall software infrastructure.
- **Target Environments:** It is designed to work seamlessly in cloud environments, supporting both AWS and Azure.
- **Instance Structure:** A FortiGate CNF instance is **dedicated to one customer**. A single FortiGate CNF instance can secure an entire AWS or Azure region, protecting multiple accounts, subnets, Virtual Private Clouds (VPCs), and availability zones, thus consolidating security.
- **Management:** The primary management interface is the **FortiGate CNF console**. This lightweight user interface and intuitive wizards are used to create instances, define and deploy security policies, and onboard cloud accounts. For hybrid cloud deployments, centralized management can be handled using FortiManager.

### Key Security Capabilities (Enterprise-Grade Protection)

FortiGate CNF supports the security inspection capabilities of a **Next-Generation Firewall (NGFW)**, providing deep visibility into the application layer, advanced detection, and comprehensive protection.

Specific protections and capabilities include:

- **Traffic Inspection:** It performs application control and deep inspection of network traffic.
- **Threat Prevention:** It includes **geo-IP blocking**, advanced filtering, and general threat protection.
- **Compliance:** FortiGate CNF enforces compliance using geo-location policies to prevent communication with restricted or prohibited regions or entities.
- **Risk Reduction:** This level of traffic inspection helps customers reduce the risk of unauthorized events on workloads caused by web-based threats, vulnerability exploits, and external and internal threat vectors.

### Primary Use Cases and Traffic Security

FortiGate CNF is capable of handling security for various traffic flows:

1. **Outbound Traffic Inspection (Primary Use Case):** This protects cloud workloads from threats associated with communication leaving the cloud network. Threats mitigated include:
    - **Malware:** Blocking connections to compromised servers that result in unintentional malware downloads.
    - **Data Exfiltration:** Preventing compromised systems from sending sensitive data to unauthorized external systems.
    - **Command and Control (C&C) Communication:** Stopping compromised workloads from communicating with C&C servers to receive malicious commands.
    - **Cryptomining:** Blocking connections to IP addresses that exploit cloud resources for cryptomining.
    - **Compliance Violations:** Preventing unauthorized communications with restricted or prohibited countries.
2. **Lateral Traffic Security (East-West):** It secures traffic flowing within cloud networks and between cloud workloads using dynamic objects.
3. **Inbound Traffic Protection:** It is capable of protecting assets hosted in the cloud from incoming traffic.

### Integration and Deployment

FortiGate CNF integrates seamlessly with existing cloud vendor infrastructure.

- **Integration with AWS:** FortiGate CNF uses cloud-native integration with the **AWS Gateway Load Balancer (GWLB)**. This helps network security teams move at the speed and scale of application teams by providing built-in High Availability (HA) and scaling.
    - The service creates a GWLB endpoint (GWLBe) in your protected VPCs.
    - Traffic is routed to the GWLBe, forwarded to the CNF service for policy enforcement, and if not blocked, returned to the GWLBe before passing out through the internet gateway.
- **Integration with Azure:** It connects a GWLB to an existing server load balancer or public IP address in protected Azure Virtual Networks (VNet).
    - Traffic is forwarded to the FortiGate CNF instance for policy evaluation.
    - If traffic is not blocked, it is returned to the GWLB and passes out through the Azure NAT Gateway.

### Benefits

Enterprises enjoy several benefits from using FortiGate CNF:

|Benefit|Description|
|:--|:--|
|**Zero Operations Overhead**|Eliminates the need for "do-it-yourself" automation and the costs associated with building, deploying, and operating security software infrastructure.|
|**Scalability and HA**|Scales quickly to accommodate changing workload demands and provides built-in HA and scaling.|
|**Simplified Management**|Uses intuitive wizards and lightweight user interfaces. Integration with AWS Firewall Manager streamlines security workflows.|
|**Lower Costs**|Reduces operational costs and saves on training/resourcing. It uses AWS Graviton instances for better price performance. Aggregating security across a region into a single instance avoids extra costs accrued by solutions that charge by cloud network or availability zone.|
|**Flexibility and Performance**|Works seamlessly in cloud environments and is optimized for high-performance security and networking capabilities.|

**Note on Selection:** FortiGate CNF is the preferred choice if your main requirement is **NGFW functionality**, combined with ease of management and scalability. If additional features like VPN, SD-WAN, or NAT functionality are required, FortiGate VM is the suggested alternative.