## Automation, IR and Advanced Threat Detection
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

## Deployment Options
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
## FortiGuard services
**FortiAnalyzer** leverages several **FortiGuard services** to enhance its threat intelligence, analysis, and reporting capabilities. These services provide real-time updates and automated content to help security teams identify and respond to threats more effectively.

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

## Database Language Support
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
## FortiAnalyzer Fabric
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
# Security Recommendations

### **Deployment and Network Security**
*   **Trusted Network Deployment:** 
    * Always deploy FortiAnalyzer in a **protected and trusted private network**; it should never be deployed outside the network.
*   **Secure Communication:** 
    *   Use only secure methods for administration, such as **HTTPS for the GUI** and **SSH for the CLI**, even within a private network. Avoid HTTP and Telnet, as they use plaintext and are vulnerable to packet sniffing.
*   **Restrict Access via Ports:** 
    *   Open only the **ports necessary** for your specific network requirements and consult the official documentation to minimize attack surfaces.
*   **Encrypted Log Transfer:** 
    *   Ensure log communication between FortiGate and FortiAnalyzer is protected through **encryption using OFTP over SSL** (port TCP/514).

### **Administrative Access Controls**
*   **Password Management:** 
    *   **Change the default administrator password** 
        *   immediately during initial setup.
    *   **Enable and enforce a global password policy** 
        *   that requires a minimum length and specific character types (numbers, special characters).
    *   Store your administrator password in a **secure location**, as there is no password recovery option without erasing system settings.
*   **Trusted Hosts:** 
    *   Configure **trusted hosts** for every administrative user to restrict logins to specific, authorized IP addresses or subnets.
*   **Principle of Least Privilege:** 
    *   Never give an administrator more privileges than they require. Use **Administrative Profiles** (such as Standard_User or Restricted_User) and **ADOMs** to limit what devices and settings an individual can access.
*   **Monitoring Activity:** 
    *   Regularly **monitor administrator sessions and event logs** to audit configuration changes and source them to specific individuals.

### **Operational Best Practices and Data Integrity**
*   **Prevent Log Tampering:** 
    *   Configure **log checksums** (MD5 or MD5-auth) to record hash values and authentication codes, ensuring logs have not been modified in storage or during transmission.

## Controlling and Restricting Access
### **Default Administrative Profiles**
FortiAnalyzer includes four preinstalled profiles to help categorize user roles:

* **Super_User**: 
  * Has full access to all system settings, device privileges, and all ADOMs.
* **Standard_User**: 
  * Provides read-write access to device-level privileges but no access to system settings.
* **Restricted_User**: 
  * Offers read-only access to device privileges and removes access to management extensions.
* **No_Permission_User**: 
  * Grants no privileges; used for temporarily disabling an account.
* You can allow the administrator to use the FortiAI feature by enabling FortiAI on the administrator profile. **FortiAI** can be applied only to local users and not single sign-on (SSO) users. You need a valid FortiAI license for this feature to be available on the administrator profile
## Administrative Domains (ADOMs)
**Administrative Domains (ADOMs)** in FortiAnalyzer interact with **FortiGate Virtual Domains (VDOMs)** to provide granular control over how log data is managed and accessed.

The way they operate together depends on the **global ADOM mode** configured on the FortiAnalyzer:

### **Normal Mode (Default)**
In this mode, the relationship between the physical device and the ADOM is strict.
*   **Single Assignment:** 
    *   You cannot assign individual VDOMs from the same FortiGate to different ADOMs.
*   **Grouping:** 
    *   You must assign the entire **physical FortiGate device and all of its VDOMs to a single ADOM**.
*   **Management:** 
    *   This provides a simpler management scenario but less granularity for multi-tenant environments.

### **Advanced Mode**
Advanced mode is designed for environments requiring more complex, granular data separation.
*   **VDOM Splitting:** 
    *   You can assign **individual VDOMs from the same physical FortiGate to different FortiAnalyzer ADOMs**.
*   **Granular Analysis:** 
    *   This allows administrators to use **FortiView, Event Management, and Reports** to analyze data for specific VDOMs independently of the rest of the physical device.
*   **Complexity:** 
    *   While more flexible, this mode results in more **complicated management scenarios**.

### **Key Considerations for Operation**
*   **Access Restriction:** 
    *   If your network uses VDOMs, enabling ADOMs on FortiAnalyzer allows you to **limit an administrator's access** to logs from only specific VDOMs rather than the whole device.
*   **Security Fabric Integration:** 
    *   All FortiGate devices (including those with multiple VDOMs) within a Security Fabric can be grouped into a single **Fabric ADOM** to facilitate rapid data processing and automatic log correlation.
*   **Registration:** 
    *   When a FortiGate with VDOMs is registered, it initially appears under the **root ADOM** until it is authorized and assigned to its designated custom ADOM.
*   **Permissions:** 
    *   Configuring these relationships requires **Super_User** access, as ADOMs are disabled by default.

## Disk Quota

The FortiAnalyzer disk quota includes two types of logs: 
+ **Archive logs:**
	+ These are logs compressed on hard disks and offline. 
+ **Analytics logs:**
	+ These are the logs stored and indexed in the SQL database and online
### Reserved Disk Quota
The **reserved disk quota** in FortiAnalyzer is a portion of the total system storage that is set aside and unavailable for storing logs. This space is used for **system operations, compressed files, uploaded files, and temporary report files**, and it acts as a buffer for **unexpected quota overflows**.
### Key Characteristics of Reserved Space:

- **Capacity:** FortiAnalyzer typically reserves between **5% and 20%** of total disk space. This leaves approximately 80% to 95% of the disk available for allocation to device logs.
- **Variation by Disk Size:** The amount of reserved space varies depending on the device's storage capacity:
    - **Small (< 500 GB):** 20% or 50 GB, whichever is smaller.
    - **Medium (500 GB–1000 GB):** 15% or 100 GB, whichever is smaller.
    - **Large (1000 GB–3000 GB):** 10% or 200 GB, whichever is smaller.
    - **Very Large (3000 GB–5000 GB):** 5% or 500 GB, whichever is smaller.
- **RAID Influence:** If RAID is being used, the **RAID level** directly influences the total disk size and, consequently, the determined reserved quota level.

### Monitoring and Troubleshooting

You can view the specific amount of reserved space on your device by using the CLI command `diagnose log device`. This is particularly useful for troubleshooting, as space assigned to a hard disk quota is not fully available for logs due to this reservation.

### Disk Quota Daemons

In FortiAnalyzer, disk quota enforcement is handled by three primary background processes (daemons) that work together to ensure storage usage remains within configured limits.

### Primary Processes for Quota Enforcement

- **`logfiled`**: This is the central monitoring process. It **monitors other processes** to enforce overall log file sizes and disk quotas. It acts as the coordinator by sending commands to other daemons for processing.
- **`sqlplugind`**: This process specifically **manages the size of the SQL database**, which contains the indexed analytics logs.
- **`oftpd`**: This daemon is responsible for **overseeing the size of archive files**, which are the compressed, offline log files stored on the disk.

### Enforcement Logic and Monitoring

The **`logfiled` daemon** performs regular checks on storage levels and coordinates the cleanup efforts:

- **Check Frequency:** It typically checks these processes and estimates SQL database space **every two minutes**, though this may be delayed if system resources are heavily strained.
- **Automatic Deletion Policy:** If the estimated disk quota usage exceeds **95%**, the system automatically begins removing the oldest files.
- **Cleanup Target:** The deletion process continues until disk usage is reduced to **85%**.

### How to Modify the ADOM Disk Quota (GUI)

1. Navigate to **System Settings > ADOMs**.
2. Select the ADOM you wish to change and click **Edit** (or double-click the ADOM).
3. Scroll to the **Disk Utilization** section.
4. In the **Allocated** field, enter the new storage amount in **MB** or **GB**.
5. Click **OK** to apply the changes.

### Key Constraints and Defaults

- **Default Allocation:** 
	- By default, each ADOM is allocated **1000 MB** (approximately 1 GB).
- **Minimum Limit:** 
	- The storage limit cannot be set lower than **100 MB**.
- **Maximum Limit:** 
	- The maximum value you can assign depends on the total available disk space on your specific FortiAnalyzer device.
- **Reserved Space:** 
	- Keep in mind that **5% to 20%** of total disk space is reserved for system operations and is not available for ADOM allocation.

### Important Considerations

- **Retention Objectives:** 
	- If the quota is too small, FortiAnalyzer will overwrite the oldest logs to stay within the limit, which may prevent you from meeting your log retention goals.
- **Performance Impact:** 
	- An insufficient quota can consume unnecessary **CPU resources** because the system must frequently run log deletion and database trimming processes.
- **Analytics vs. Archive Ratio:** 
	- Within the ADOM settings, you can also modify the storage ratio between **analytics logs** (online/indexed) and **archive logs** (offline/compressed). The default ratio is **70% for analytics** and **30% for archives**.
- **Device-Specific Quotas:** 
	- While ADOM quotas are managed via the GUI, you can configure disk quotas for **individual devices** within an ADOM using the **CLI**.
- **Moving Devices:** 
	- If you move a device to a new ADOM, ensure the new ADOM has an adequate disk quota to handle the incoming log volume.
### High Availability Principles and Configuration Rules

A FortiAnalyzer high availability (HA) cluster can consist of a **maximum of four devices**, comprising one primary device and up to three secondary devices. All cluster members must belong to the **same FortiAnalyzer series**, operate on the **same firmware version**, and be visible to each other within the network.
### Operational Requirements

- **Operational Mode:** 
	- Every device in the cluster must run in the same mode, either as an **analyzer** or a **collector**.
- **VM Platform:** 
	- If the cluster uses virtual machines (VMs), all members must reside on the **same platform**; for instance, a cluster cannot mix VMware and KVM instances.
- **Licensing:** 
	- If devices with different licenses are combined, the license with the **smallest number of managed devices** dictates the limits for the entire cluster.

### Storage and Synchronization

While the available disk space on each member does not need to match exactly, it is essential that every device has **sufficient storage** to handle the expected logs, and it is recommended that they have similar available capacity. To maintain consistency, the cluster uses two synchronization states: **initial synchronization** to align logs when a new device is added and **real-time synchronization** for ongoing data. Furthermore, configuration settings for modules such as the Device Manager, Reports, and Incidents and Events are synchronized across all members.

### Election and Failover Rules

If the primary device fails, a new one is elected based on the following criteria:

1. **Priority:** 
	+ The device with the highest configured priority (ranging from **80 to 120**) is selected.
2. **IP Address:** 
	+ If priorities are equal, the device with the **highest primary IP address** becomes the new primary.
3. **Database Health:** 
	+ While network reachability is the default failover trigger, you can also configure the cluster to monitor the **Postgres database process** and initiate a failover if it fails.
### Cluster Benefits and Limitations 

An HA cluster provides real-time redundancy and improves responsiveness through **load distribution**. For example, secondary devices can share the load for **generating reports** and processing **FortiView widgets**. Regarding larger architectures, a FortiAnalyzer HA cluster can act as a **member** of a FortiAnalyzer Fabric, but a device acting as a **Fabric supervisor** cannot be configured for HA.

### HA Operation Modes

FortiAnalyzer high availability (HA) provides real-time redundancy and load distribution by grouping up to **four devices** (one primary and up to three secondary devices) into a cluster. To configure these options, administrators use the **Cluster Settings** section under **System Settings > HA**.

FortiAnalyzer offers three primary HA operation modes:

- **Standalone:** The default mode where the device operates independently without being part of a cluster.
- **Active-Passive:**
    - **Connectivity:** 
	    - Requires **Layer 2 connectivity** between members to establish a cluster virtual IP address.
    - **Log Handling:** 
	    - Only the **primary device** receives logs and archive files from connected devices; it then forwards them to secondary members.
    - **Forwarding:** 
	    - Only the primary device can forward logs to a remote server.
- **Active-Active:**
    - **Connectivity:** 
	    - Members can reside on **different subnets** or in different geographic locations.
    - **Heartbeat:** 
	    - Instead of a virtual IP, a **heartbeat interface** is defined to communicate across different networks.
    - **Log Handling:** 
	    - All members can **directly receive logs** and archive files from connected devices and forward them to peers or remote servers.

### Key Cluster Configuration Settings

For a cluster to function, all members must share the same **Group Name, Group ID, and Password**. Key settings include:

- **Preferred Role:** 
	- Determines the initial role (Primary or Secondary) when a device first joins a new cluster.
- **Priority:** 
	- A value from **80 to 120** (default 100) used to elect a new primary if the current one fails.
- **Cluster Virtual IP:** 
	- Optional in active-active mode but required for active-passive to provide a single point for devices sending logs.
- **Unicast/Multicast Heartbeat:** 
	- Heartbeat packets use VRRP (default multicast address 224.0.0.18), but can be configured to use **unicast** via the CLI.

### Primary Election Process

If the primary device becomes unavailable, a new one is selected based on these criteria:

1. **Highest Priority:** 
	1. The secondary device with the highest assigned priority is promoted.
2. **Highest IP Address:** 
	1. If priorities are equal, the device with the highest primary IP address is selected. For instance, 123.45.67.124 will be chosen over 123.45.67.123.
3. **Synchronization Requirement:** 
	1. A secondary device cannot become primary until it has finished synchronizing with the current primary.
4. **Health Checks:** 
	1. While network reachability is the default failover trigger, you can configure the CLI to monitor the **Postgres database process** (`set healthcheck DB`) and trigger failover if it fails.

### Synchronization States

- **Initial Synchronization:** 
	- Occurs when a new device joins the cluster. The secondary device reboots and rebuilds its log database once complete.
- **Real-time Synchronization (Log Data Sync):** 
	- Enabled by default, this ensures logs received by the primary are immediately forwarded to secondaries. This can be disabled if a device is intended only as a standby unit.
### Synchronized Modules

The cluster performs **configuration synchronization** to provide redundancy and support load balancing. The following modules are synchronized across all members:

- **Device Manager** and **All ADOMs**.
- **Incidents and Events** configurations.
- **Reports** and custom report details.
- **System Settings**, including administrative users, CA certificates, and log forwarding settings.

### Critical Configuration Rules

- **Pre-promotion Requirement:** 
	- A secondary device is **prohibited from becoming the primary device** until it has fully completed synchronization with the current primary.
- **Load Balancing Sync:** 
	- Because logs and reports are synchronized, secondary devices can share the workload for generating reports and processing **FortiView widgets**. When a report is generated on one device, it is synced so it is visible to all cluster members.
- **Firmware Mismatch:** 
	- If cluster members have different firmware versions, **configuration synchronization is disabled**, though other operations may continue to function while the upgrade is in progress.

### Monitoring and Troubleshooting

Administrators can verify synchronization status through the **Cluster Status** window in the GUI, which displays specific columns for **Initial Logs Sync** and **Configuration Sync**. Alternatively, the CLI command `diagnose ha status` provides the same details, and `diagnose ha force-cfg-resync` can be used to manually trigger a configuration update if needed

### High Availability Cluster Upgrade Protocols

Upgrading a FortiAnalyzer High Availability (HA) cluster requires a specific sequence to ensure continuous operation and data integrity.

### Recommended Upgrade Sequence

- **Secondary Devices First:** 
	- You must always **upgrade all secondary devices first**.
- **Primary Device Last:** 
	- The **primary device should be upgraded only after** all secondary devices have successfully completed their upgrades and have synchronized with the primary.

### Cluster Behavior During Upgrade

- **Automatic Failover:** 
	- When the upgrade of the primary device begins, the system **automatically selects one of the upgraded secondary devices to become the new primary** based on the cluster's configured selection rules. This mechanism allows the HA cluster to **continue operating** without interruption during the upgrade process.
- **Firmware Mismatches:** 
	- It is normal to see **firmware version mismatch messages** while the upgrade is in progress. During this period of mismatch, **configuration synchronization is disabled**, although other synchronization operations will continue to function. These messages will disappear once all members are running the same version.
- **Multi-Step Paths:** 
	- If the recommended upgrade path involves multiple intermediate versions, you should **complete each step for both the primary and secondary devices** before proceeding to the next version in the path.

### Pre-Upgrade Preparation

Before initiating the upgrade, you should perform the following best practices:

- **Review Documentation:** 
	- Download and review the **release notes** and the recommended **upgrade path** from the Fortinet Support portal.
- **Perform Backups:** 
	- It is critical to **back up the FortiAnalyzer configuration and databases**; backing up logs is also highly recommended.
- **Check System Health:** 
	- Use the `diagnose log device` command to verify current disk quotas and ensure no logging devices have a status of "Down".
- **Manage Reports:** 
	- Wait for all **active reports to finish** and ensure no new reports are scheduled to run during the upgrade window.

### Post-Upgrade Verification

Once the upgrade is complete for all members, use the **Cluster Status** page in the GUI or the CLI command `diagnose ha status` to verify that all devices are "In-Sync" and that their roles are correctly assigned. You should also run `diagnose log device` again to confirm that **ADOM disk quotas** remain correct after the migration.

---
# Monitoring and Troubleshooting
```bash
# verify the validity and expiry details of these service contracts
diag fmupdate dbcontract fds
# monitor the migration progress on the GUI banner
diag sql status migrate-db
# viewing administrator login status
diag system admin-session status
# verify the upgrade history on your FortiAnalyzer 
diag cdb upgrade summary
# Displays a total quota summary, system storage summary, and reserved space.
diag log device
# Shows the value of system files mounted on the drive, which contributes to the "Used" space calculation
diagnose system print df
# This is the primary command for viewing the health of the cluster
diag ha status
# Displays real-time **HA statistics** for the cluster
diag ha stats
# Used to dump the HA data log, which provides a historical record of HA-related events for deep troubleshooting
diag ha dump-datalog
# This command is used to **force an HA failover**
diag ha failover
# Manually **forces the HA cluster to resynchronize its configuration**
diag ha force-cfg-resync
# Displays the **HA load balance status**
diag ha load-balance
# This command must be run on the **master (primary)** device to **restart the initial log synchronization process**
diag ha restart-init-sync
```