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

### Basic Connectivity and Network Settings

These commands help verify if the FortiAnalyzer and logging devices can communicate over the network.

- **`execute ping <remote_IP>`**: 
	- Tests basic network connectivity between FortiAnalyzer and another device.
- **`get system interface`**: 
	- Displays the status, speed, and IP addresses of network interfaces.
- **`get system status`**: 
	- Shows the general status of the device, including firmware version and system time, which are critical for log compatibility.
- **`show system route`**: 
	- Displays configured static route entries.
- **`diagnose system print route`**: 
	- Shows the complete routing table, including directly connected routes.
- **`show system dns`**: 
	- Displays configured DNS server addresses.
- **`show system ntp`**: 
	- Shows network time protocol settings.

### Device and ADOM Status

Use these commands to determine which devices are registered and if the Administrative Domains (ADOMs) are correctly configured.

- **`diagnose dvm device list`**: 
	- Lists all registered and unregistered devices or VDOMs.
- **`diagnose dvm adom list`**: 
	- Lists which ADOMs are currently enabled and configured.
- **`diagnose test application oftpd 3`**: 
	- Displays which devices and specific IP addresses are currently connected to the FortiAnalyzer.
- **`diagnose log device`**: 
	- Provides a summary of device log usage and is used to verify that ADOM disk quotas are correct.

### Communication and Log Reception Troubleshooting

These commands allow you to monitor log traffic in real-time and verify that the system is actively receiving data.

- **`diagnose sniff packet <interface> <filter> <level> <count>`**: A built-in packet sniffer used to see if log packets are reaching the FortiAnalyzer interfaces.
- **`diagnose debug application oftpd 8`**: 
	- Provides detailed information on current log activity and is used to verify that FortiAnalyzer is receiving logs from a device.
- **`diagnose fortilogd lograte`**: 
	- Displays the current raw log receive rate.
- **`diagnose fortilogd msgrate`**: 
	- Shows the log message receive rate per second.
- **`diagnose fortilogd lograte-device [filter]`**: 
	- Breaks down the log receive rate for a specific device.
- **`diagnose fortilogd lograte-adom {all | adom-name}`**: 
	- Calculates the log rate for all ADOMs or a specific one.

### Process Debugging and System Health

If communication is established but logs are still not appearing, use these commands to check the health of the background daemons.

- **`get system performance`**: 
	- Monitors CPU, memory, and disk usage to ensure the system is not nearing capacity, which can halt log collection.
- **`diagnose debug enable`**: 
	- Enables the display of debug messages on the CLI.
- **`diagnose debug application <process> <level>`**: 
	- Enables debugging for a specific process (e.g., `oftpd`, `sqlplugind`, `uploadd`) at a specified verbosity level.
- **`diagnose debug info`**: 
	- Displays the current active debug levels.
- **`execute tac report`**: 
	- Generates a comprehensive dump of all diagnostic commands for troubleshooting with technical support.
- **`diagnose sql status sqlplugind`**: 
	- Displays the status of SQL database insertions.

### Troubleshooting from the FortiGate Side

Log collection issues often require running commands on the sending FortiGate device.

- **`show log fortianalyzer setting`**: 
	- Verifies if the FortiGate is correctly configured to send logs to the FortiAnalyzer's IP.
- **`show log fortianalyzer filter`**: 
	- Checks if the logging filters are enabled.
- **`diagnose log test`**: 
	- Generates dummy logs to test if the FortiGate is capable of sending them and if the FortiAnalyzer receives them.
- **`diagnose test application miglogd 6`**: 
	- Displays FortiGate logging statistics, including cache sizes.
- **`diagnose log kernel-stats`**: 
	- Shows if logs are being dropped because the cache is full.
- **`set faz-disk-buffer-size <integer>`**: 
	- Configures the size of the disk buffer on FortiGates with SSDs to handle periods when the FortiAnalyzer is unreachable.

### ADOM Migration

When moving devices between Administrative Domains (ADOMs) in FortiAnalyzer, there are several critical factors to consider to ensure data integrity and proper disk management.

The primary considerations include:

- **Necessity of the Move:** The general best practice is to **avoid moving devices between ADOMs unless it is absolutely necessary**.
- **Disk Quota Readiness:** You must verify the disk quota of the destination ADOM to **ensure there is sufficient allocated space** for the incoming device and its logs.
- **Log Migration Behavior:** It is important to understand that when a device is moved, **only the archive (compressed) logs are automatically migrated** to the new ADOM. The analytics (indexed) logs stay in the original ADOM database.
- **Analytics and Reporting in the New ADOM:** If you require the device’s historical analytics logs for reports in the new ADOM, you **must rebuild the destination ADOM's SQL database**.
```
execute sql-local rebuild-db
diag sql status rebuild-db
```
- **Analytics Logs in the Old ADOM:** If you prefer that the device’s historical logs no longer appear in the original ADOM, you must **rebuild the old ADOM’s SQL database** to remove them. Otherwise, they will remain in the old database until they are eventually purged according to that ADOM's data policy.
- **Log Rate Separation:** Moving devices can be beneficial if you need to **separate high-volume log rate devices from low-volume ones**. Placing them in different ADOMs prevents high-volume traffic from triggering quota enforcements that might prematurely delete logs from the low-volume devices.
- **Firmware Upgrades:** You do **not** need to move a device to a new ADOM specifically because of a FortiGate firmware upgrade, as it is not necessary to separate ADOMs by FortiOS version.

## FortiGate HA Cluster Management in FortiAnalyzer

FortiAnalyzer is designed to **automatically detect** if a registered FortiGate device is part of a High Availability (HA) cluster. If the individual FortiGate devices were registered to FortiAnalyzer before the cluster was formed, you must **manually add them together** within the system settings.

To manage this configuration, you should edit the registered device in the **Device Manager** and enable the **HA Cluster** option. During this process, you have the choice to either **select existing registered devices** to form the cluster or **manually enter the serial numbers** for each secondary node.

Key Characteristics of FortiGate HA Clusters on FortiAnalyzer:

* **Primary Communication:**
	* In a FortiGate HA cluster, **only the primary device communicates directly** with FortiAnalyzer.

* **Log Forwarding:** 
	+ The secondary nodes in the cluster do not send logs directly to FortiAnalyzer; instead, they send their logs to the **primary FortiGate**, which then forwards them to the FortiAnalyzer unit.

+ **Device Identification:** 
	+ FortiAnalyzer distinguishes between the different members of the cluster by identifying their **unique serial numbers**, which are included in the headers of every log message it receives.

+ **Fault Tolerance:** 
	+ This setup ensures that logging continues even if a failover occurs, as the new primary device will take over the responsibility of sending the aggregated cluster logs to FortiAnalyzer

## Storage Connector Service

The **Storage Connector Service** in FortiAnalyzer is a specialized feature that enables the system to **send logs to cloud platforms**. This service is essential for organizations looking to leverage cloud storage for log retention or off-site redundancy.

Key details regarding this service include:
### **Licensing and Constraints**

- **Separate License Required:** To use the Storage Connector Service, you must purchase a separate license, which typically has a **one-year validity**.
- **Data Transfer Limits:** The license specifies a **quota for the amount of data you can upload** to the cloud platform. It is important to note that this limit applies only to the **data transfer volume**, not to the total storage capacity used on the cloud provider's side.
- **Renewal:** If you exceed your data upload quota before the license expires, you must renew the license to continue using the service.
### **Configuration**

- **Activation:** Once the license is uploaded, the feature is enabled by going to the **Device Log Settings** page and toggling on **Upload logs to cloud storage**.
- **Platform Support:** Administrators can select from various supported cloud storage platforms (such as AWS, Azure, or Google Cloud) to receive the logs.
- **Permissions:** Configuring the connector requires an account with the appropriate permissions to access the targeted cloud storage.
### **Monitoring and Diagnostics**

You can monitor the status and usage of the Storage Connector Service using the following CLI commands:

- **`diagnose fmupdate dbcontract fds`**: Used to verify the **validity and expiration date** of the license.
- **`diagnose test application uploadd 63`**: Provides a detailed usage summary, including the **total gigabytes uploaded**, the **number of files sent**, the remaining quota, and the number of upload requests that were dropped.

## Log Redundancy Options

### 1. **FortiAnalyzer High Availability (HA) Cluster**

A FortiAnalyzer HA cluster provides **real-time redundancy** by ensuring continuous operation if the primary device fails.

- **Automatic Failover:** If the primary device fails, another node in the cluster is automatically selected to take over.
- **Secure Synchronization:** The primary device **securely synchronizes logs, data, and system settings** across multiple devices in the cluster.
- **Load Balancing:** Secondary devices can reduce the load on the primary by handling tasks like **running reports** or sharing the load for FortiView widgets.

### 2. **Identical Logging to a Second Server**

You can configure a FortiGate to send an **identical set of logs to a second logging server**, such as a secondary FortiAnalyzer or a syslog server.

- **Resource Impact:** This method increases the CPU and RAM load on the FortiGate because the log daemon must manage an additional TCP connection.
- **Constraint:** This option is **not supported on smaller FortiGate devices** that lack the capability to connect to a second logging device.

### 3. **Log Forwarding in Aggregation Mode**

This mode involves a collector sending only the **"delta" (incremental changes)** of logs, archives, and quarantined files to a central aggregation server.

- **Traffic Reduction:** It reduces the amount of traffic sent over the network by comparing stored data and sending only what the analyzer is missing.
- **Self-Healing:** If the analyzer device fails, the collector can **automatically repopulate the restored analyzer** with all stored data.
- **Constraint:** Aggregation mode is supported **only between two FortiAnalyzer devices**.

### 4. **Network and Local Device Redundancy**

Beyond system-level options, you can protect logs through network-path and local-buffering redundancy:

- **Link Aggregation:** You can combine two or more physical interfaces on FortiAnalyzer into an **aggregate link**. This ensures network redundancy, as the link remains active if at least one physical interface is functioning.
- **FortiGate Local Caching:** If FortiAnalyzer is unreachable, FortiGate uses its **miglogd process** to cache logs locally in memory. On FortiGate devices with an SSD, a **configurable disk log buffer** can be used to protect logs during longer outages until the connection is reestablished.
- **Secure Log Transfer:** You can enable **OFTP over SSL** (TCP port 514) to encrypt the log communication stream between FortiGate and FortiAnalyzer, protecting data integrity during delivery.

## Log Forwarding in FortiAnalyzer

Log Forwarding allows FortiAnalyzer to **send logs to another destination** — another FortiAnalyzer, a syslog server, or a CEF server (like FortiSIEM).

## Two Forwarding Modes

|Mode|How it works|
|---|---|
|**Forwarding**|Real-time or near real-time log delivery as logs are received|
|**Aggregation**|Logs and content files are stored locally first, then uploaded at a **scheduled time**|

## 3 Configuration Steps

### 1. Set the Log Forwarding Mode

```bash
config system log-forward
  edit <log forwarding ID>
    set mode <aggregation, forwarding, disable>
  end
```

### 2. Configure the Server (recipient)

```bash
config system log-forward-service
  set accept-aggregation enable
end
```

- Only required in **aggregation mode** — the server must be configured to accept client logs.
- In **forwarding mode**, only the client side needs configuration.

### 3. Configure the Client (the FortiAnalyzer sending logs)

- Done via **System Settings > Log Forwarding**
- Here you specify:
    - **Which device logs** to forward
    - **Log filters** — only send logs that match certain criteria
## Encrypted Log Communication: OFTPS

**Optimized Fabric Transfer Protocol (OFTP)** is used over **SSL** to synchronize information between FortiAnalyzer and FortiGate.
## Two Communication Streams

| Stream                 | Encrypted?        | Protocol/Port |
| ---------------------- | ----------------- | ------------- |
| **OFTP communication** | ✅ Yes (SSL)       | TCP/514       |
| **Log communication**  | ❌ No (by default) | UDP/514       |

> Once **secure log transfer is enabled**, logs also switch to **TCP/514**
## Enabling Secure Logs on FortiGate

```bash
config log fortianalyzer setting
  set reliable enable
end
```
## Encryption Algorithm Configuration

### On FortiGate:

```bash
config log fortianalyzer setting
  set enc-algorithm {high-medium | high* | low}
end
```

- Default: **high**
- Low encryption models can only do **low** level

### On FortiAnalyzer:

```bash
config system global
  set enc-algorithm {high* | medium | low | custom}
end
```

- Default: **high**
- ⚠️ FortiAnalyzer encryption level must be **equal to or less than** FortiGate's level
## Preventing Log Modification

To ensure logs are not altered (tampered with) after they are generated, **FortiAnalyzer** provides mechanisms to verify log integrity during storage, archiving, and transmission.

### Enable Log Checksums

You can configure FortiAnalyzer to add a **checksum** to each log file. This allows you to detect if a log has been modified.

### Why use checksums

- Detect tampering in stored logs
- Verify integrity when logs are rolled/archived
- Protect logs during upload (for example, to an SFTP server)
- Help prevent man-in-the-middle (MITM) attacks during transmission

### Configuration Command

```bash
config system global
    set log-checksum md5-auth {md5 | md5-auth | none}
end
```

### Available Options

|Option|Description|
|---|---|
|**md5**|Records only the MD5 hash of the log file.|
|**md5-auth**|Records the MD5 hash **and** an authentication code (more secure).|
|**none**|No checksum is recorded.|

**Best Practice:** Use `md5-auth` for stronger integrity verification.


## Change the OFTP Certificate (Secure Log Transmission)

If logs are transmitted using OFTP, you can replace the default certificate with a custom one for improved security.
### Requirements

- PEM-formatted certificate
- Associated PEM-formatted private key
### Configuration Command

```bash
config system certificate oftp
    set mode custom
    set certificate <your PEM format certificate>
    set private-key <your PEM format private key>
end
```

### Why change the certificate

- Strengthens trust between systems
- Secures log uploads
- Reduces risk of interception or impersonation
## Rolling & Auto-Deleting Logs

**Location**  
System Settings > Advanced > Device Log Settings

**Log Rolling** (closes & compresses current log file, starts new one)  
- **Size-based**: Roll when file > X MB (default example: 200 MB, range 10–1000 MB)  
- **Time-based**: Roll at scheduled time (e.g., every Sunday 00:00 or daily at specific hour)  
→ Both can be active; whichever hits first triggers the roll.  
Optional: Upload rolled files to SFTP/SCP/FTP or cloud storage.

**Automatic Deletion** (global policy – deletes oldest files by age)  
Applies to:  
- Device archive logs  
- Reports  
- Content archive files  
- Quarantined files  

Each type:  
- Enable/disable  
- Age (e.g., 365 days)  
- Delete daily at set time (e.g., 00:00)

**Key Warning**  
Global auto-delete policy **and** per-device log quotas run independently:  
- If device quota fills first → deletes oldest logs for that device.  
- If global age limit hits first → deletes oldest files system-wide (ignores device settings).  
→ Misconfiguration can cause logs to be deleted earlier than expected.

**Quick Recommendation**  
- Use size + daily rolling for clean organization.  
- Set global deletion age carefully to match compliance needs and avoid surprises.

## SQL Hard Cache (hcach / hcache)

## What Is hcache?

**SQL Hard Cache (hcache)** is a proprietary caching mechanism in **FortiAnalyzer** that improves report performance by storing **precompiled log data** as **persistent SQL database tables on disk**.

Unlike temporary caches, hcache tables:

- Are stored persistently on disk
- Are not automatically removed after a set time
- Must be updated or rebuilt when new logs arrive

**Reports > Report Definitions > All Reports**
## How hcache Improves Performance

### Report Generation

- When generating reports, FortiAnalyzer builds charts and datasets from **hcache tables**.
- If the required hcache already exists and no new logs were added:
    - The report runs **significantly faster**.

### First-Time Report Execution

- If hcache does **not exist**, FortiAnalyzer must:
    
    1. Build the hcache table
    2. Then generate the report

This increases the initial report generation time.

### Extended Log Filtering

- When enabled, specific log fields are cached.
- This improves filtering speed for complex report queries.
# Auto-Cache Feature

## What Is Auto-Cache?

**Auto-cache** automatically keeps hcache updated when new logs arrive.

When enabled:

- FortiAnalyzer builds and updates hcache automatically
- New log tables are generated proactively
### Important Notes

- ✅ **Scheduled reports automatically use hcache**
- ⚠️ Building hcache consumes:
    - CPU
    - Memory
    - Disk I/O

Complex reports that process large datasets can significantly impact system resources.
# Optimization Techniques

## Report Grouping (CLI)

If similar reports run across multiple devices, use:

```
configure system report group
```

Purpose:

- Group reports together
- Reduce total number of hcache tables
- Improve auto-cache completion time
- Reduce system resource usage
# Monitoring and Troubleshooting hcache

## Check hcache Status

```
diagnose sql hcache status
```

Shows the current state of hcache.
## Check hcache Size

```
diagnose sql show hcache-size
```

Displays total disk space used by hcache.
## Verify hcache Build Performance

```
diagnose test application sqlrptcached 2
```

Indicates whether hcache creation can keep up with incoming logs.
## Manually Trigger hcache Build

```
execute sql-report hcache-build <ADOM-name>
```

Manually starts hcache build for a specific ADOM.
## Key Points to Remember

- hcache = persistent precompiled SQL tables
- Speeds up repeated report generation
- First run is slower if hcache is not built
- Auto-cache keeps hcache updated automatically
- Scheduled reports always use hcache
- High CPU/memory usage during build
- CLI commands are commonly tested
# Debugging – Summary

## Enabling / Disabling Debug

```
diagnose debug enable
diagnose debug disable
```

## Debug Levels

- Range: **0–255**
- **Level 8** provides the most detailed output (commonly used for troubleshooting).
## Debugging a Specific Application/Process

```
diagnose debug application <process> <level>
```

You must specify:

- The process name
- The debug level
## Check Current Debug Settings

To see current debug status and levels:
```
diagnose debug info
```
This shows:

- CLI debug level
- Whether console output is enabled
- Timestamp settings
- Application debug levels

# Key Exam Points

- Debug must be **enabled** to see output.
- Level **8** = most detailed (commonly tested).
- `diagnose debug info` verifies current settings.
- `execute tac report` generates a full diagnostic dump.
- Timestamps should be enabled during troubleshooting.

# Report Debugging
## Core Process: `sqlreportd`

The **sqlreportd** process is responsible for all report generation on FortiAnalyzer. It sends queries to **sqlpugiund** to fetch data.
## Debugging Commands

### Check Process Status

```bash
diagnose sql status sqlreportd
```
### List Generated Reports

```bash
execute sql-report list <ADOM>
```

- Shows recent reports (last 7 days by default)
- Displays report name + unique ID
### View Report Content

```bash
execute sql-report view report-data <ADOM> "<report-name_ID>"
```
## Report Queue Management

|Command|Purpose|
|---|---|
|`diagnose report status`|Check the report queue (pending/running reports)|
|`diagnose report clean`|**Wipe the queue** if it's causing hangs or slowdowns|

> ⚠️ Too many queued reports can cause FortiAnalyzer to **hang or become slow**
### Queue Status Output Includes:
- Max pending reports
- Current pending/running counts
- Semaphore state
- Longest running report info
## Run a Report from CLI (for Debugging)

```bash
execute sql-report run <ADOM> <name/title>
```

- Prompts you to select from available report templates
- Useful for **capturing live debug output**
- Available reports include: Security Analysis, Threat Report, IPS Report, Bandwidth & Applications, Top 20 Categories, etc.
## Quick Reference Summary

```
sqlreportd         → Core report generation process
diagnose sql       → Check process health
sql-report list    → See generated reports
diagnose report status → Check queue
diagnose report clean  → Clear stuck queue
sql-report run     → Manually trigger + debug
```
# Alert Email Debugging
## Core Process: `fazmaild`

The **fazmaild** daemon handles **all email sending tasks** on FortiAnalyzer, including:

- Report emails
- Event Management alert mails
## Debugging Commands

### Enable Debug Logging for fazmaild

```bash
diagnose debug application fazmaild 8
```

- Shows **all reports and event management mails** sent by FortiAnalyzer
- Level `8` = detailed debug output
### Test Connection to Mail Server

```bash
diagnose test connection mailserver "Mailserver:10.0.2.254"
```

- Verifies FortiAnalyzer can **reach the mail server**
- Useful for troubleshooting email delivery failures
## Alert Email Statistics Commands

|Command|Purpose|
|---|---|
|`diagnose test application fazmaild 0`|Basic status|
|`diagnose test application fazmaild 1`|Additional info|
|`diagnose test application fazmaild 2`|**Full email count statistics**|

### Sample Output of `fazmaild 2`:

```
total sent mail count:        0
total failed sendmail count:  0
total discard mail count:     0
last sendmail time:           Wed Dec 31 16:00:00 1969
last request time:            Fri Oct 11 12:56:20 2024
queue:                        0/5000
queue timeout:                1800 seconds
```
## Quick Summary

```
fazmaild          → Email sending daemon
fazmaild 8        → Debug all sent emails
mailserver test   → Verify mail server connectivity
fazmaild 2        → View email send statistics & queue status
```

> ⚠️ If the queue fills up (max 5000) or timeout occurs (1800s), email delivery may fail — use `fazmaild 2` to monitor.


# Upload Process Debugging

## Core Process: `uploadd`

The **uploadd** process is responsible for **uploading logs and reports to a remote server**.
## Debugging Commands

### Enable Debug for uploadd

```bash
diagnose debug application uploadd 0
```

- Useful for debugging:
    - **Rolled log uploads**
    - **Failed FTP uploads**

### Check Upload Queue Status

```bash
diagnose upload status
```

Shows detailed info including:

- Upload mode (e.g., BACKUP)
- Schedule timing
- Queue stats: `log[0] / bkup[0] / m1[0] / m2[0]`
- Prepare / Dispatch / Reprepare states
- Per-server status: done / abandoned / retry / hit / miss / overload
- RPT file stats
- Cloud storage status
## Queue Operations

|Command|Purpose|
|---|---|
|`diagnose upload status`|View running upload queue status|
|`diagnose upload clear`|**Clear/wipe the upload queue**|

> ⚠️ **Real-world use case:** If 10,000 log files need to be converted to CSV and uploaded, the process can take a very long time. Running `diagnose upload clear` is the fastest way to resolve the bottleneck.

## Status Output Breakdown

```
upload mode: BACKUP
schedule: 23 hours 46 minutes 43 seconds later

queue:     log[0] / bkup[0] / m1[0] / m2[0]
prepare:   done[x] / failed[0] / locate err[0]
dispatch:  done[x] / failed[0]

Server stats per server:
  done / abandoned / retry / hit / miss / overload

RPT file stats:
  queue[0] / done[0] / abandoned[0] / retry[0]

Cloud storage: upload[0] / retry[0]
```
## Quick Summary

```
uploadd                        → Upload process daemon
diagnose debug uploadd 0       → Debug rolled/failed uploads
diagnose upload status         → Monitor upload queue
diagnose upload clear          → Clear stuck/overloaded queue
```

# Security Automation Content Packs

## What Is It?

A service that delivers the **latest security content** from **FortiGuard** to FortiAnalyzer — similar to how antivirus outbreak packages are automatically updated.

> ⚠️ Requires a **separate license** for the FortiAnalyzer SOC Automation Service

## What Content Is Delivered?

### Package 1 — Detection & Parsing

-  **Log Parsers** — third-party log parsing
-  **Detection Rules** (handlers)
-  **Report Templates**
-  **Compliance Mapping**

### Package 2 — Response & Connectivity

-  **Incident Response Playbooks**
-  **New Fabric Connectors**
## Key Benefits

|Feature|Benefit|
|---|---|
|**Decoupled from OS release**|Get new security content without upgrading FortiAnalyzer OS|
|**Automated distribution**|Like outbreak package updates — no manual intervention|
|**SIEM + SOAR integrated**|Covers detection, analytics, and response in one platform|

## Capabilities Enabled

- Third-party log parsers
- Advanced correlation rules
- Automation connectors
- Incident response playbooks
- Advanced analytics
- Premium reports

> These tools empower **SOC teams** to swiftly **detect, investigate, and respond** to security incidents.

# Security Automation Content Packs (Continued)

## Monthly Release Cycle

- New content packs are released **every month**
- Details available at: **https://www.fortiguard.com**
- With a valid license, updates are **applied automatically** when available from FortiGuard Distribution Network (FDN)
## What's Included in Each Pack

|Content Type|Purpose|
|---|---|
|**Report Templates**|Prebuilt & customizable, deep insights into security events/trends|
|**Event Handlers**|Automate incident-handling for faster response|
|**Playbooks**|Automated security workflows for proactive defense|
|**SIEM Parsers**|Advanced log parsing for better threat correlation & analysis|
## Log Parsers from Content Packs

Log parsers are received directly from **FortiGuard Distribution Network (FDN)** and cover a wide range of vendors:

- Zscaler Firewall
- Cisco Meraki
- Microsoft Office 365
- Okta
- Juniper Firewalls
- Aruba CX
- Barracuda Firewall
- McAfee Anti-Virus
- CrowdStrike, SentinelOne, Check Point, and more

## How to Identify FortiGuard Content

|Indicator|Meaning|
|---|---|
|**Origin = FortiGuard**|Log parser came from a content pack|
|**"New" tag**|Latest installation from most recent content pack|
## Key Takeaway

```
Valid License
     ↓
FortiGuard releases pack monthly
     ↓
Auto-applied to FortiAnalyzer
     ↓
New parsers, handlers, playbooks, reports available immediately
```

> Content packs are **independent of OS upgrades** — you always get the latest security content without needing to update FortiAnalyzer firmware.
# Log Parsers

## What Are Log Parsers?

Built-in tools with **SIEM capabilities** that **parse, normalize, and correlate** logs from various sources — no manual configuration needed by administrators.
## Sources They Parse

|Source Type|Examples|
|---|---|
|**Fortinet Products**|FortiGate, FortiSwitch, etc.|
|**Web Servers**|Apache, Nginx|
|**OS Security Events**|Windows & Linux hosts (via Fabric Agent)|
|**Third-party Devices**|Cisco, Juniper, Aruba, Checkpoint, Barracuda, etc.|
## Three Types of Log Parsers

|Type|Origin|Description|
|---|---|---|
|**Predefined**|Built-in|Comes with FortiAnalyzer by default|
|**FortiGuard**|FDN Content Packs|Received automatically (Origin = FortiGuard)|
|**Custom/Imported**|Admin|Manually imported by administrator|
## Key Functions

- **Parse** — Read and decode log formats from different vendors
- **Normalize** — Standardize log data into a common format
- **Correlate** — Link related log events for analysis
- **Integrate** — Bring third-party device logs into FortiAnalyzer for **analysis and storage**
## Where to Manage Log Parsers

```
Incident & Events > Log Parsers > Log Parsers
```

- View all predefined, FortiGuard, and custom parsers
- Import new or custom parsers
- SIEM logs viewable at: **Log View > Logs > All**
## Key Takeaway

```
Third-party device logs
        ↓
   Log Parser
  (parse + normalize)
        ↓
FortiAnalyzer SIEM
  (correlate + store + report)
```

> Log parsers are essential for **extending FortiAnalyzer's visibility** beyond Fortinet products into a full multi-vendor SIEM environment.
# Safeguarding in FortiAnalyzer

## What Is It?

A feature that **identifies harmful content keywords** on the network, used in reports and event handlers to enable timely response to threats like cyberbullying or self-harm content.

> 💡 **Use Case:** In schools, if students browse harmful content, teachers can be immediately alerted to take action.

## Keyword Database

|Detail|Info|
|---|---|
|**Total Keywords**|6,553+|
|**Sources**|FortiGuard + Built-in|
|**Updates**|Automatic via FortiGuard downloads|
|**Admin customization**|❌ Cannot add new words manually|

### Keyword Categories

-  Pornography
-  Cyberbullying
-  Violence/Terrorism
-  Self-Harm
-  Extremism

## Language Support

- Majority (**~99%**) are in **English**
- Also includes: Japanese, Chinese (Simplified), Korean, German, Spanish, and more

## Visual Insights

Three donut charts display keyword breakdown by:

- **Category** — type of harmful content
- **Origin** — FortiGuard vs Built-in
- **Language** — distribution across languages

## Where to Access

```
Incident & Events > Safeguarding
```
 ## Key Points

- FortiAnalyzer must be **connected to FortiGuard** distribution servers to display all keywords
- Keywords are **automatically updated** — no manual intervention needed
- Used in both **reports** and **event handlers** for automated response
- Administrators **cannot add custom keywords** to FortiGuard categories
## Summary Flow

```
Harmful content browsed on network
        ↓
FortiAnalyzer detects keyword match
        ↓
Event Handler triggers alert
        ↓
Admin/Teacher notified immediately
        ↓
Timely action taken
```

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