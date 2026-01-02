Subnets, or sub-networks, are key components of the Virtual Private Cloud (VPC) in Google Cloud, defining the IP address range for cloud resources, such as Compute Engine instances.

Here is everything described about Google Cloud Subnets:

**Scope and Location**

- **Region Restriction:** A subnet is contained within **one specific geographical region**.
- **Zone Spanning:** Although restricted to a single region, a subnet **can span multiple zones** within that region. For example, a subnet might span across `asia-east1-a` and `asia-east1-c` zones.
- **VPC Relationship:** Multiple subnets, located in different regions, can be configured under a single VPC, allowing a VPC to span across those multiple regions (e.g., `asia-east1` and `us-west2`).

**IP Address Management**

- **Definition:** The IP addresses within a subnet are defined by Classless Inter-Domain Routing (CIDR).
- **Instance Assignment:** When a Compute Engine instance is created, it is assigned a Dynamic Host Configuration Protocol (DHCP) lease within its assigned subnet.
- **Reserved IP Addresses:** Google Cloud automatically reserves four specific IP addresses within a subnet, and these addresses should not be used if you are assigning IP addresses statically:
    - **First IP Address (`X.X.X.0`):** Reserved as the network address.
    - **Second IP Address (`X.X.X.1`):** Reserved as the default gateway address.
    - **Second-to-Last Address (`X.X.X.254`):** Reserved for potential future use.
    - **Last Address (`X.X.X.255`):** Reserved as the broadcast address.

Resources like Compute Engine instances use the reserved default gateway IP address of their subnet, but traffic is then redirected according to the gateways defined in the routing table.