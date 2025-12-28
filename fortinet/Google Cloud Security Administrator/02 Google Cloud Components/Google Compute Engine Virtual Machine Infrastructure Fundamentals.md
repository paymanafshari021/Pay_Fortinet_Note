Compute Engine is the Google Cloud service that allows you to create **Virtual Machine (VM) instances** in the cloud, and it is considered an **Infrastructure-as-a-Service (IaaS)** offering,. The VMs you deploy during labs are typically considered IaaS.

Key information and configurations for Compute Engine instances include:

**Creation Methods** You have several ways to create a Compute Engine instance:

- **Manually:** You can define the processor and memory configuration, operating system (OS), and networking settings.
- **Instance Templates:** This resource defines the configuration for VM instances and can be reused to create multiple instances with identical settings.
- **Machine Images:** This is a snapshot of an existing Compute Engine VM instance, capturing the entire configuration, state, and disk content of the VM.
- **Marketplace:** You can deploy images from third-party vendors, such as a FortiGate VM or FortiWeb VM, directly from the marketplace,.

**Configuration Essentials**

- **Zone Assignment:** Each instance must be assigned to a specific **zone** upon creation. Once created, an instance cannot be moved to another zone.
- **IP Addressing:** Instances are automatically assigned a **DHCP address** within their respective subnet.
- **Network Interfaces (NICs):** After an instance is created, **you cannot add or remove network interfaces**, regardless of the VM's processor and memory configuration.
- **Tags:** You can assign instance **tags** (labels) to gain granular control when creating custom routes and firewall rules,.

**Related Services**

- **Elastic IP Addresses (EIPs):** An EIP is a static, public IPv4 address that you can associate with any Compute Engine instance. EIPs can be used to mask the failure of an instance by rapidly remapping the address to another instance in your Virtual Private Cloud (VPC).
- **Persistent Disk:** Compute Engine instances use Persistent Disk, which is durable block storage, for both boot and data disks.

Since Compute Engine instances form the foundational infrastructure layer (IaaS), running a FortiGate VM on this service provides secure connectivity, firewall policy enforcement, and advanced security capabilities for your network workloads.
