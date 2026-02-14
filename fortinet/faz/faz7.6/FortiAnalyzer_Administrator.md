# Automation, IR and Advanced Threat Detection
**Automation**, **Incident Response (IR)**, and **Advanced Threat Detection** are identified as additional key features of FortiAnalyzer designed to enhance network security and streamline operations.
### **Automation**
The automation feature in FortiAnalyzer is designed to handle **routine tasks** to free up administrative resources. Key aspects include:
*   **Automated Responses:** 
    + It can automatically respond to security incidents and generate reports.
*   **Policy Adjustments:** 
    + It can adjust security policies based on observed network behavior.
*   **Security Automation Service:** 
    + This service provides updated content, including **predefined playbooks, event handlers, and connectors**, to streamline security operations and orchestration.
*   **Playbooks:** 
    + These are automated security workflows that enable proactive defense mechanisms.

### **Incident Response (IR)**
FortiAnalyzer provides **real-time detection and response capabilities** to manage security incidents effectively.
*   **Comprehensive Visibility:** 
    + It offers a consolidated view of network traffic and security events by correlating and analyzing data generated from all Fortinet devices.
*   **Alert Generation:** 
    + The system generates alerts when specific security events occur, such as a user attempting to access a restricted website.
*   **Fabric Integration:** 
    + Integration through **Security Fabric connectors** enriches incident response by allowing automated actions, such as triggering playbooks to quarantine infected hosts.
*   **ITSM Integration:** IT 
    + ervice management (ITSM) connectors allow FortiAnalyzer to send notifications to external platforms (like ServiceNow or MS Teams) when new incidents are created or updated.

### **Advanced Threat Detection**
This feature focuses on identifying sophisticated and emerging threats using global intelligence.
*   **FortiGuard Labs Integration:** 
    + FortiAnalyzer integrates with FortiGuard Labs to receive **real-time information on emerging threats, zero-day exploits, and rapidly spreading malware**.
*   **Data Correlation:** 
    + It analyzes and correlates threat data from multiple sources, including **third-party threat feeds**.
*   **Outbreak Detection:** 
    + Through this service, FortiAnalyzer receives critical outbreak reports and event handlers to quickly identify and act upon systems affected by current outbreaks.
*   **Indicators of Compromise (IoC):** 
    + This service provides a constantly updated feed of malicious artifacts (IPs, domains, file hashes) to help identify compromised users or systems within the network.
*   **SOC Automation Service:** 
    + This service includes advanced analytics, third-party log parsers, and premium reports to empower security teams to investigate incidents swiftly.Based on the sources provided, FortiAnalyzer offers several flexible **deployment options** to suit different organizational needs, network topologies, and infrastructure requirements.

# Deployment Options
### **Physical Appliances**
Hardware appliances are designed for organizations that prefer **on-premises deployments** and have specific requirements for storage and processing. 
*   **Scalability:** 
    *   Options range from entry-level models to high-end appliances.
*   **FortiAnalyzer Big Data:**
    * A specialized higher-end model designed specifically to handle **exceptionally high log volumes** - 500GB/day to 20,000 GB/day
    .

### **Virtual Machines (VMs)**
FortiAnalyzer VMs allow organizations to leverage their **existing virtualized infrastructure**.
*   **Platform Support:** 
    * Supported on various **public and private cloud infrastructures**. Supported platforms include VMware and kernel-based VM (KVM), though members of a High Availability (HA) cluster must stay on the same platform.
*   **Flexibility:** 
    * These deployments offer flexibility in allocating resources such as **CPU, memory, and storage** based on specific needs.

### **FortiAnalyzer Cloud**
This is a **Software-as-a-Service (SaaS)** offering from Fortinet.
*   **Management:** 
    * It allows organizations to use FortiAnalyzer capabilities **without managing the underlying infrastructure**.
*   **Key Features:** 
    * It offers **easy deployment and management**, as well as **auto-scaling** functionality to adjust to changing deployment needs.

Based on the sources provided, **FortiAnalyzer** leverages several **FortiGuard services** to enhance its threat intelligence, analysis, and reporting capabilities. These services provide real-time updates and automated content to help security teams identify and respond to threats more effectively.

### **Key FortiGuard Services in FortiAnalyzer**

*   **Outbreak Detection Service:** 
    *   This service provides critical **outbreak reports and corresponding event handlers** from FortiGuard Labs. Administrators can use these event handlers to quickly identify systems within their network that are affected by current global outbreaks and take immediate action.
*   **Indicators of Compromise (IoC) Service:** 
    *    It delivers a constantly updated feed of **known malicious artifacts**, such as IP addresses, domains, file hashes, and URLs associated with cyberattacks. FortiAnalyzer uses this feed to enrich real-time and historical logs, allowing for the swift identification of compromised devices or users.
*   **Attack Surface Rating and Compliance Service:** 
    *    This service evaluates your organization’s **security posture in real-time**. It continuously assesses security health, identifies unpatched vulnerabilities, and monitors critical settings. Administrators receive alerts for changes and best-practice recommendations to improve configurations.
*   **Security Automation Service:** 
    *    This service provides the content needed for automation and orchestration features. It includes **predefined playbooks, event handlers, and connectors** to streamline security operations. Details of these content packs are released every month on the FortiGuard Labs website and are applied automatically if you have a valid license.
*   **SOC Automation Service:** 
    *    Building on the automation features, this service offers advanced tools like **third-party log parsers**, advanced correlation rules, premium reports, and incident response playbooks. This service requires a **separate license**.
*   **Safeguarding Service:** 
    *    This feature uses a database of **over 6,000 keywords** (automatically updated from FortiGuard) to identify harmful content on the network, such as cyberbullying, self-harm, or extremism. These keywords are utilized in reports and event handlers to alert relevant personnel (e.g., teachers in a school setting) to potential risks.
*   **FortiAI for FortiAnalyzer:** 
    *    A generative **AI-powered assistant** that helps analysts work faster. It uses natural language prompts to interpret security events, provide detailed summaries of potential impacts, and suggest specific remediation actions.

### **Service Management and Licensing**
*   **Automatic Updates:** 
    *    When a valid license is active, services like the Security Automation content packs are **applied automatically** when they become available from the FortiGuard distribution server.
*   **License Monitoring:** 
    *    You can verify the validity and expiry details of these service contracts using specific CLI commands, such as `diagnose fmupdate dbcontract fds`.

# Database Language Support
**SQL (Structured Query Language)** is the primary database language supported and used by FortiAnalyzer for logging and reporting.

### **Database Engine and Migration**
Starting with version **7.6.0**, FortiAnalyzer has shifted its underlying storage architecture:
*   **ClickHouse Database:** 
    *    Logs are now stored in a ClickHouse database rather than the previously used **PostgreSQL (PSQL)** database.
*   **Automatic Migration:** 
    *    When upgrading to version 7.6.0 or later, FortiAnalyzer automatically migrates historical logs from the PostgreSQL database to the ClickHouse database. 
*   **Core Functionality:** 
    *    All **FortiView data and reports** in version 7.6.0 are based on the data tables stored within ClickHouse.

### **Operational Considerations**
*   **Collector Mode:** 
    *    By default, the **SQL database is disabled** when FortiAnalyzer is in collector mode. Logs requiring the database are unavailable unless SQL is manually enabled via the CLI.
*   **Knowledge Requirement:** 
    *    To take full advantage of advanced reporting, administrators need a basic understanding of SQL syntax and datasets. SQL queries in FortiAnalyzer are **not case sensitive**.
# FortiAnalyzer Fabric
The **FortiAnalyzer Fabric** is a specialized architecture that enables **centralized viewing** of devices, incidents, and events across multiple FortiAnalyzer devices. It is specifically designed for high-volume environments that require multiple FortiAnalyzer nodes to handle the log load while maintaining a single management interface.

### **Operation Modes**
The FortiAnalyzer Fabric operates using a root-and-branch structure with two distinct modes:

*   **Supervisor Mode:**  
    *    This acts as the **root device** of the Fabric. There is only one supervisor per Fabric, and it provides SOC administrators with a consolidated view of:
            + All member devices and their Administrative Domains (ADOMs).
            + Authorized logging devices across the entire Fabric.
            + Incidents and events generated by any member.
*   **Member Mode:** 
    *    These are individual FortiAnalyzer devices that perform the actual log processing and incident generation. They send their metadata and event information to the supervisor for centralized viewing.

### **Key Operational Requirements**
*   **Mode of Operation:** 
    *    All members in the Fabric **must be in analyzer mode**.
*   **Data Syncing:** 
    *    Members **do not forward their logs** to the supervisor. Instead, incident and event information is synchronized from members to the supervisor using an **API**.
*   **Time Synchronization:** 
    *    All member devices must be configured with the **same time zone settings** as the supervisor.
*   **High Availability (HA):** 
    *    While FortiAnalyzers in an HA cluster can join as **members**, the supervisor node itself **does not support HA**.

### **Integration with Security Fabric ADOMs**
To maximize the efficiency of the Fabric, FortiAnalyzer allows you to group all Fortinet devices from a single Security Fabric into a **Security Fabric ADOM**. This provides several benefits:
*   **Concise Logging:** 
    *    It prevents duplicate logs by ensuring only the first FortiGate to handle a session logs the traffic (unless NAT is performed by an upstream device).
*   **Automatic Correlation:** 
    *    FortiAnalyzer automatically correlates traffic logs with corresponding UTM logs to provide a clear record of threats and sessions.
*   **Centralized Reporting:** 
    *    Combined results from the entire Fabric are presented in one place, including FortiView, Log View, and generated reports.

### **Benefits of the Fabric Architecture**
*   **Scalability:** 
    *    It is ideal for large-scale environments with **high log volumes** that exceed the capacity of a single device.
*   **Visibility:** 
    *    It provides an accurate, consolidated picture of security threats across the entire network under a single interface.
*   **Redundancy:** 
    *    Logging remains independent; if a root FortiGate or the supervisor is down, leaf devices continue to log to their respective FortiAnalyzer members.


---
# Data Sheet
```bash
# verify the validity and expiry details of these service contracts
diag fmupdate dbcontract fds
# monitor the migration progress on the GUI banner
diag sql status migrate-db
```