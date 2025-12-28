Elastic IP Addresses (EIPs) are a key networking concept used with Google Cloud Compute Engine instances.

Here is everything described about EIPs:

**Definition and Function**

- An EIP is a **static, public IPv4 address** that you can associate with any Compute Engine instance.
- The primary purpose of an EIP is to **mask the failure of an instance** by allowing you to rapidly remap the address to another instance within your Virtual Private Cloud (VPC).

**Types of EIPs**

- There are two types of EIPs: **regional** and **global**.
- A **regional EIP** cannot be moved to a different region, while a **global EIP** can.

**Role in High Availability (HA)**

- In a FortiGate Active-Passive (A-P) HA cluster deployed using the SDN connector architecture across two zones, the EIP is the **only IP address that is shared** by the cluster.
- The EIP is assigned to the active FortiGate VM's external network interface (e.g., `port1`).
- During a failover in this SDN architecture, the passive FortiGate uses Google Cloud APIs to send commands that cause the elastic IP (along with the internal default route) to **move to the old passive FortiGate VM** (which becomes the new active instance).
- You can observe this process where the EIP is removed from the old active instance and attached to the new active instance in the console output when using `gcpd` failover diagnostics.

EIPs are also referred to as **Service Public IP** or **Ephemeral External IP** in some diagrams related to HA deployments.

**Note on Usage:** When deploying an SDN HA cluster, the active FortiGate VM and the passive FortiGate VM each have different IP addresses in the public subnet, but they share the EIP as the single externally accessible IP address for the cluster.