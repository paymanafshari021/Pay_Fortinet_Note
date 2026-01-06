The responsibility for security in the cloud is based on a **Shared Responsibility** model, meaning the duties are divided between the cloud provider and the customer. This topic is considered arguably the top challenge in the public cloud.

The division of responsibilities breaks down into two main areas:

1. **The Cloud Provider's Responsibility (The Lower Stack):** The cloud provider is responsible for securing the **infrastructure** itself. This includes the fundamental elements of the cloud environment:
    
    - Storage
    - Network
    - Compute
2. **The Customer's Responsibility (The Upper Stack):** The customer is responsible for securing the remaining elements, or the "upper stack," which are the resources they deploy and manage. **Most cloud security is the responsibility of the user, not the provider**. This includes:
    
    - Applications
    - Data and content
    - Encryption and network traffic protection
    - The operating system (OS)
    - Firewall and network settings, and configuration
    - Applications, platform, and user management
    - Customer network

A general principle is that **if you manage it, you are responsible for it**. This is why security misconfigurations are identified as the number one threat in the public cloud by more than 51% of surveyed cybersecurity professionals. Indeed, **Gartner estimates that 99% of cloud security failures through 2025 will be the customer's fault**.

The level of shared responsibility changes based on the cloud service model used (IaaS, PaaS, or SaaS):

- **Infrastructure-as-a-Service (IaaS):** Some services (like networking) are managed by the vendor, while others are managed by the customer.
- **Platform-as-a-Service (PaaS):** The vendor manages most services, and the customer is primarily responsible for programming applications.
- **Software-as-a-Service (SaaS):** The customer simply uses the services as a consumer for running applications.

For instance, when utilizing a specialized Software-as-a-Service (SaaS) solution like Dropbox, a customer uses the service as a consumer, and third-party security solutions can only provide "visibility and control," not secure connectivity such as an IPsec tunnel or Web Application Firewall (WAF).

In essence, the cloud vendor secures the environment it provides, but the customer secures everything they place _in_ that environment, from the virtual machine (VM) operating system up to the data itself.