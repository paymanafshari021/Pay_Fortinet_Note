# 01 SOC Concepts and Security Fabric
## The Four Pillars of Strategic SOC Operations

A Security Operations Center (SOC) is a dedicated team responsible for real-time security monitoring across an organization's IT infrastructure, including networks, devices, and cloud environments. The SOC operates with **four key objectives** to maintain a strong security posture:

- **Threat Identification**: This objective involves establishing a comprehensive approach to **threat intelligence**. By staying updated on the latest cyberthreats, the SOC gains the knowledge required to recognize existing and emerging threats, which helps them refine detection strategies and enhance defensive capabilities.
- **Exposure and Validation**: The SOC must continuously **assess the network** to understand its degree of exposure to attacks. This involves using the **Continuous Threat Exposure Management (CTEM)** methodology to scope, prioritize, and validate threat exposure, allowing the team to plan how to best mobilize people and processes.
- **Monitoring and Detection**: This objective focuses on **real-time monitoring** of network activity to identify active and potential threats. The SOC must understand where to position detection technology and which specific threats to look for to ensure their actions align with organizational needs and **compliance requirements**. Tools such as SIEM and XDR systems are often used to analyze telemetry data for identifying incidents or outbreaks.
- **Response**: When an attack is detected, the SOC responds with a focus on **impact avoidance**. This includes performing **incident investigations** to ensure a compromise does not lead to further risks, isolating compromised systems to neutralize threats, and taking direct actions such as blocking malicious IP addresses, domains, or URLs.

To think of these objectives as a cycle, imagine a **castle guard tower**: the guards first study the tactics of nearby raiders (**Threat Identification**), check the castle walls for weak spots (**Exposure and Validation**), keep a constant watch on the horizon for movement (**Monitoring and Detection**), and immediately deploy defenses to stop any raider who manages to reach the gate (**Response**).

## Functional Foundations of the Security Operations Center

The Security Operations Center (SOC) is a dedicated team responsible for **real-time security monitoring** across an organization’s entire IT infrastructure, which includes applications, networks, devices, and cloud environments. Their duties are structured around several core functional areas designed to maintain a resilient security posture:

### 1. Threat Intelligence and Identification

The SOC must establish a proactive approach to **threat intelligence** by staying updated on the latest cyberthreats. This responsibility involves gaining the necessary knowledge to recognize both existing and emerging threats, which allows the team to refine their detection strategies and enhance defensive capabilities.

### 2. Continuous Threat Exposure Management (CTEM)

According to the sources, the SOC is responsible for assessing the network to understand its **degree of exposure** to attacks. By using the CTEM methodology, the team can **scope, prioritize, and validate** threat exposure. This allows them to effectively plan how to mobilize people and processes for security operations.

### 3. Real-Time Monitoring and Detection

SOC teams must manage the vast amounts of information required to watch the network for suspicious activity. This includes:

- **Data Aggregation**: Utilizing a **data lake** (such as FortiAnalyzer) to ingest and store raw logs from all devices in the Security Fabric.
- **Analytical Tools**: Using dashboards, reports, and **Security Information and Event Management (SIEM)** features to identify active and potential threats.
- **Telemetry Analysis**: Leveraging **Extended Detection and Response (XDR)** systems and third-party services to analyze data and quickly identify incidents or emerging outbreaks.

### 4. Incident Investigation and Response

Once a threat is identified, the SOC is responsible for preventing further compromise through:

- **Investigation**: Performing deep analysis of incidents to ensure they do not lead to further risks. This includes **incident reporting** to track all potentially affected systems.
- **Containment**: Implementing measures such as **isolating compromised systems** to neutralize threats and minimize damage.
- **Direct Response**: Taking active steps to stop attacks, such as **blocking malicious IP addresses, domains, or URLs**, and coordinating recovery efforts for any affected devices.

### 5. Compliance and Operational Maintenance

The SOC must adhere to **regulatory standards** specific to their industry and geographical location while maintaining meticulous records of threats and incident responses. Additionally, specific roles within the SOC, such as **SOC engineers**, are responsible for maintaining the uptime and performance of tools like SIEM and SOAR, while analysts ensure that **automation playbooks** are correctly configured to run only when required.

To visualize these responsibilities, consider a **hospital's emergency room**: the triage team continuously monitors incoming patients (**Real-time Monitoring**), specialists use diagnostic tools to identify specific illnesses (**Threat Detection**), the medical staff performs surgery to stop the immediate danger (**Incident Response**), and administrators ensure all treatments follow legal health standards (**Compliance**).