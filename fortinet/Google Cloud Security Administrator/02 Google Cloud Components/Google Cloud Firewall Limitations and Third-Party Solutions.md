The sources identify several limitations of the **native Google Cloud firewall** and security features, which often necessitate the deployment of third-party security solutions like FortiGate-VM.

The key limitations are:

### Feature Limitations

- **Limited Next-Generation Firewall (NGFW) Capabilities:** The Google Cloud firewall offers only **limited NGFW capabilities**.
- **Limited Advanced Threat Protection (ATP):** The native firewall's ATP feature set is limited. Basic TLS/SSL inspection is restricted solely to **Intrusion Detection System (IDS)** and **Intrusion Prevention System (IPS)** functionalities.

### Management and Policy Consistency

- **Lack of Hybrid Cloud Consistency:** There is **no consistency of firewall policy management across hybrid cloud** environments. Organizations cannot integrate their existing rule sets for on-premises firewalls directly with Google Cloud.
- **Restricted Integration:** This lack of consistency makes it difficult for users to uniformly apply security policies across their combined cloud and on-premises deployments.

### Cost Structure

- **Associated Costs:** The implementation of Google Cloud firewall features, particularly NGFW features which require Firewall Policies, involves multiple charges. Organizations are charged for every **VM that sends traffic** through the firewall. Additionally, there are charges for each **VPN tunnel** and based on **traffic volume**.

To overcome these limitations and ensure seamless security across hybrid environments, third-party solutions like FortiGate-VM are often recommended, as they can provide consistent security policies managed centrally (e.g., via FortiManager) across the entire infrastructure.