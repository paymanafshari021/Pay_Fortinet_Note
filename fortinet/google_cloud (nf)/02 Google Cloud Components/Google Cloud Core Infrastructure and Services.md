Google Cloud is a public cloud vendor that provides a broad portfolio of **infrastructure and services** accessible over the public internet, offering advantages such as **scalability, automated deployments, security, recovery, reliability, and cost optimization** based on use.

The services are categorized into models, including Infrastructure-as-a-Service (IaaS), Platform-as-a-Service (PaaS), and Software-as-a-Service (SaaS).

Here are the key Google Cloud components and services identified in the sources:

### Compute Services

The Compute service category provides resources for running scalable virtual machines and containers:

- **Compute Engine (IaaS):** This service offers scalable and customizable Virtual Machines (VMs) in the cloud, suitable for running various workloads. The VMs deployed during hands-on practice are considered IaaS.
- **App Engine (PaaS):** This is a fully managed platform designed for building and deploying web applications, allowing developers to focus primarily on writing code by abstracting the underlying infrastructure.
- **Google Kubernetes Engine (GKE):** A fully managed service for deploying, managing, and scaling containerized applications using Kubernetes.
- **Cloud Run functions (Serverless Computing):** Executes code in response to events without the need for server management.

### Networking Services

These services form the foundation for connecting resources:

- **Virtual Private Cloud (VPC) Networks:** This service defines your own logically isolated network within Google Cloud that is both scalable and flexible. A single VPC is **global** and can span multiple regions by configuring various subnets, though each subnet itself is contained within one specific region.
- **Cloud Load Balancing:** Distributes incoming traffic across instances to enhance availability and scalability. The **Passthrough Network Load Balancer (NLB)** operates at Layer 4 (TCP, UDP, other IP protocols) and is specifically recommended for **FortiGate High Availability (HA) clusters**.
- **Cloud Interconnect:** Provides a dedicated physical connection between your on-premises network and Google’s network, offering high bandwidth, lower latency, and secure links, making it ideal for hybrid cloud environments.
- **Cloud DNS:** A managed, scalable, and reliable DNS service that supports public DNS for external resolution and private DNS for internal resolution within VPC networks.
- **Subnets:** These contain the IP address range for instances and are limited to one specific region, though they can span multiple zones within that region.

### Storage and Management Services

- **Cloud Storage:** A highly scalable and durable **object storage** service used for unstructured data like backups, files, and images. It offers different storage classes (Standard, Nearline, Coldline, Archive) to optimize cost based on access frequency. FortiGate can use this service for firmware, configuration files, and log storage.
- **Persistent Disk:** Provides durable **block storage** for Compute Engine VMs and can be attached as a boot or data disk.
- **Identity and Access Management (IAM):** This crucial security service controls access to resources by defining and managing permissions (roles) for entities (principals). Permissions are inherited hierarchically (Organization -> Folder -> Project -> Resource).
- **Google Cloud Marketplace:** A platform where you can discover and deploy third-party software and services optimized for Google Cloud environments, including Fortinet products like FortiGate, FortiWeb, and FortiManager.