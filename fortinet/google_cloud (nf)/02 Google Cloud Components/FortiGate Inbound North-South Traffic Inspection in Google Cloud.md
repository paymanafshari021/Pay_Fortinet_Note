North-South traffic inspection, specifically focusing on inbound traffic, refers to the security measures used to protect applications from external traffic originating from the internet before it reaches the internal workloads.

In a FortiGate deployment in Google Cloud, inbound North-South inspection involves the following steps and components:

### Traffic Flow and Inspection Process

The inbound traffic flow starts from an external client and proceeds through the load balancers and the FortiGate VM cluster before reaching the internal workload:

1. **Client Initiation:** An external client initiates a connection, targeting the external load balancer's public IP address.
    - Example: A client at IP address 198.51.100.10 sends traffic destined for the external load balancer IP address 34.159.73.2.
2. **External Load Balancer Forwarding:** The external load balancer (a Network Load Balancer in this topology) forwards the traffic to the **active FortiGate cluster member**,.
3. **Google Cloud Firewall Evaluation:** Incoming connections are evaluated first against Google Cloud firewall policies or rules.
4. **FortiGate Processing (DNAT):** The active FortiGate cluster member performs **Destination Network Address Translation (DNAT)**, translating the external load balancer's IP address to the private IP address of the target workload,.
    - Example: FortiGate changes the destination from 34.159.73.2 to the workload A IP address, 10.0.0.10.
5. **FortiGate Policy and Threat Scanning:** Before routing, the traffic is subject to deep security inspection:
    - It is evaluated against **FortiGate firewall policies**.
    - It is **scanned for threats** using the FortiGate security profile.
6. **Routing to Workload:** The traffic is routed to the workloads using their internal IP addresses or Fully Qualified Domain Names (FQDN).
7. **Workload Reply:** The workload receives the connection and replies directly to the source IP address that initiated the traffic,.

### Handling Return Traffic (Egress)

The flow must pass back through the active FortiGate instance to maintain security and session state:

1. **Internal Load Balancer Redirection:** The reply traffic from the workload is routed using a custom static route in the internal VPC, pointing to the **internal passthrough network load balancer** as the next hop.
2. **Forwarding to FortiGate:** The internal load balancer receives the traffic and forwards it to the active FortiGate cluster member,.
3. **FortiGate Processing (SNAT):** The FortiGate performs **Source Network Address Translation (SNAT)**, mapping the internal workload IP address to the external load balancer's public IP address,.
4. **Client Receipt:** The client receives the response sourced from the external load balancer's public IP address,.

>**Best Practice Note:** As a best practice, the sources suggest that the FortiGate generally should **not translate the source IP address** upon ingress, so the original client IP address remains visible to the internal workloads. However, the described traffic flow scenario above includes SNAT on the return traffic (egress) to source the packet from the external IP address,.