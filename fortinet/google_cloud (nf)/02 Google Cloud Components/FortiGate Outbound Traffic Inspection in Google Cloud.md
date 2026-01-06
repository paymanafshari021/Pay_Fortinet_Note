North-South traffic inspection, focusing on **outbound traffic**, involves securing communications originating from internal workloads and destined for external networks, such as the internet. In a FortiGate deployment in Google Cloud, the FortiGate Virtual Machine (VM) acts as a **secure web gateway** to protect this traffic.

This architecture relies on Google Cloud routing components, specifically custom routes and internal load balancers, to enforce traffic inspection by the FortiGate cluster.

### Traffic Flow for Outbound Inspection

The outbound traffic flow in this scenario is orchestrated as follows:

1. **Initiation:** A workload (e.g., Workload A at 10.0.0.10) initiates an outbound connection aimed at an external destination (e.g., 8.8.8.8).
2. **Internal Routing:** The internal Virtual Private Cloud (VPC) network utilizes a **custom route** to redirect this traffic toward the **internal load balancer** (ILB). The internal load balancer acts as the next hop in this custom route.
3. **Forwarding to FortiGate:** The internal load balancer receives the traffic and forwards it to the currently **active FortiGate cluster member**.
4. **Inspection and NAT:** The active FortiGate cluster member performs two key actions:
    - It **inspects the traffic** based on its security policies (such as Unified Threat Management or NGFW features).
    - It applies **Source Network Address Translation (SNAT)**, mapping the internal IP address of the workload (10.0.0.10) to the external IP address assigned to the **external passthrough network load balancer**.
5. **External Exit:** After inspection and SNAT, the traffic exits the VPC and proceeds to the internet, sourced from the external load balancer's public IP address.
6. **Web Server Receipt:** The external web server (e.g., 8.8.8.8) receives the traffic, seeing the external load balancer's public IP address as the source.

This mechanism ensures that all traffic leaving the internal network is securely inspected and that the external destination receives traffic sourced from a consistent public IP address managed by the external load balancer.

### Architecture Components

This architecture is typically structured as a "load balancer sandwich" using Network Load Balancers (NLBs) and involves:

- **Custom Routes:** Essential for directing outbound traffic away from the default internet gateway and toward the inspection point (the ILB).
- **Internal Load Balancer (ILB):** Routes the outbound traffic arriving from the internal hosts to the active FortiGate VM member.
- **External Load Balancer:** Provides the external public IP address that the traffic is SNATted to before leaving the Google Cloud environment.
- **FortiGate VM:** Acts as the inspection point for traffic originating from internal workloads.