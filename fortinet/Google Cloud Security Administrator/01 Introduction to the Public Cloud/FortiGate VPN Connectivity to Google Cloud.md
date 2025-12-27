Extending on-premises infrastructure to Google Cloud allows customers to connect their local environment to the cloud using a Virtual Private Network (VPN).

This typically involves running an **IPsec tunnel** between the FortiGate device deployed on-premises and a corresponding FortiGate Virtual Machine (VM) running in Google Cloud Compute Engine.

Key aspects of this extension include:

- **VPN Tunneling:** The connection is established via a high-speed IPsec VPN, or sometimes an SSL VPN, running between the on-premises Data Center Firewall (DCFW) or Next-Generation Firewall (NGFW) and the FortiGate VM in the cloud.
- **Security Recommendation:** While a customer could deploy a cloud vendor-native IPsec service, deploying a virtual FortiGate on the cloud side is highly recommended for secure connectivity.
- **Purpose:** This configuration extends the customer's network into the cloud, enabling communication between the on-premises network and resources deployed in the External VPC and Internal VPC subnets within Google Cloud.
