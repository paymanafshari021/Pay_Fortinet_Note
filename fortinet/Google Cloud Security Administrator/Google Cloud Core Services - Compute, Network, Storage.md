Google Cloud offers a range of services categorized into Compute, Network, and Storage, designed to support different workloads and deployment use cases.

### Compute Services

The compute service category includes offerings across Infrastructure-as-a-Service (IaaS), Platform-as-a-Service (PaaS), and serverless computing:

- **Compute Engine (IaaS):** This service provides scalable and customizable Virtual Machines (VMs) in the cloud. Users can choose from predefined machine types or create custom ones to meet specific requirements. Labs mentioned in the study guide use VMs deployed during the process, which are considered Infrastructure-as-a-Service (IaaS).
- **App Engine (PaaS):** A fully managed Platform-as-a-Service (PaaS) solution intended for building and deploying web applications. It abstracts the underlying infrastructure, allowing developers to focus strictly on writing code.
- **Google Kubernetes Engine (GKE) (Containers):** This is a managed Kubernetes service used to run containerized applications. GKE simplifies the process of deploying, managing, and scaling these containerized applications.
- **Cloud Run functions (Serverless functions):** This service enables serverless computing, executing code in response to events without requiring server management. It is ideal for lightweight, event-driven applications and is similar to AWS Lambda.

### Network Services

Google Cloud's network offerings focus on connectivity, traffic management, and content delivery:

- **Virtual Private Cloud (VPC) networks (Virtual networks):** VPC provides a scalable and flexible global private network within Google Cloud. Customers can create multiple subnets across different regions within a single VPC.
- **Cloud Load Balancing (Load balancer):** This is a global load balancer that distributes incoming traffic among multiple instances, potentially across different regions, ensuring high availability and reliability.
- **Cloud Interconnect/Peering (Dedicated interconnect/peering):** This provides a direct physical connection between the customer’s on-premises network and Google’s network, resulting in higher bandwidth and lower latency compared to internet connections.
- **Cloud DNS (DNS):** A managed, scalable, and reliable DNS service that supports all standard DNS records.
- **Cloud CDN (Content Delivery Network):** This global content delivery service reduces latency and improves performance by caching content at over 90 locations worldwide.

### Storage Services

The storage service category provides different types of storage tailored to various needs:

- **Cloud Storage (Object storage):** This is scalable, durable, and secure storage designed for unstructured data, such as backups, videos, and images.
- **Persistent Disk (Block storage):** This durable block storage is used specifically for Google Compute Engine VMs, where it can be attached as a data disk or a boot disk.
- **Filestore (File storage):** A fully managed Network File System (NFS) file storage solution intended for applications that require shared storage and a file system interface.