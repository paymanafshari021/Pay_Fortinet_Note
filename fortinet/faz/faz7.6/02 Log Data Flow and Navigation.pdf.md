## The FortiAnalyzer Log Lifecycle Workflow

The log file workflow is an automated process that moves data from initial network activity to long-term storage and analysis within a Security Operations Center (SOC). This workflow can be broken down into five distinct phases:

### 1. Log Generation and Transmission

The process begins at the edge of the network when traffic passes through a device like a FortiGate. The device scans the traffic, applies firewall policies, and records the activity as a **log message** containing a standard header and body. Once registered and authorized, these devices transmit their logs to a centralized repository, such as FortiAnalyzer, acting as a data lake. To ensure data integrity during transmission, organizations can enable **reliable logging**, which switches the transport protocol from UDP to TCP.

### 2. Ingestion and Normalization

When FortiAnalyzer receives the logs, they enter an automatic workflow on the disk:

- **Decompression and Saving**: Logs are immediately decompressed and saved into an **active log file** on the disk with a `.log` extension (e.g., `tlog.log` for traffic or `elog.log` for events).
- **Normalization**: Because raw logs from different devices are often unstructured, FortiAnalyzer uses **predefined parsers** to translate them into a standardized format. This ensures consistent field names across all logging devices, making searching and investigation more efficient.

### 3. The Analytics Phase (Online Logs)

Simultaneously with the saving process, FortiAnalyzer **indexes** the logs into a SQL database.

- **Immediate Support**: These indexed logs are known as **analytics logs**. They are considered **online** because they offer immediate support for real-time monitoring, SIEM event handlers, dashboards, and complex reporting.
- **Correlation**: In this phase, FortiAnalyzer automatically correlates traffic logs with unified threat management (UTM) logs, creating a complete picture of network events.

### 4. The Archive Phase (Offline Logs)

As the active log files grow, they eventually undergo a process called **rolling over**:

- **Rolling and Compression**: When a log file reaches a specific size or a set schedule, it is renamed, timestamped, and compressed with a **.gz extension**.
- **Offline Status**: These compressed files are known as **archive logs**. They are considered **offline** because they are no longer indexed in the SQL database and do not offer immediate analytic support unless they are fetched and re-indexed.

### 5. Data Policy and Disposal

The final stage of the workflow is governed by the **Administrative Domain (ADOM) data policy**. This policy dictates the lifecycle of the data by determining:

- How long analytics logs remain in the SQL database before being **purged**.
- How long archive logs are kept on the disk before being **deleted**.

To understand this workflow, imagine a **busy courtroom**: The court reporter captures raw testimony in real-time (**Log Generation**). Those notes are immediately typed into a searchable digital database for the lawyers to use during the trial (**Analytics Logs/Online**). Once the trial is over, the records are bound into a heavy book and moved to a basement storage facility (**Archive Logs/Offline**). Eventually, after several years, the building manager follows a strict retention schedule to shred the old books to make room for new ones (**Data Policy/Disposal**).

## Universal Language: The Principles of Log Normalization

**Log normalization** is the process of transforming raw, disparate log entries from various sources into a **standardized, structured format** with consistent field names. When FortiAnalyzer ingests logs, they are typically in their native, unstructured format; normalization uses **predefined log parsers** to identify and extract key data points and map them to a common schema. These parsed and mapped entries are then inserted and indexed within a dedicated **security information and event management (SIEM) database** created for that specific Administrative Domain (ADOM).

Beyond standardizing field names, normalization can also **unify field values**; for instance, different devices may report a block action as either "block" or "deny," which a parser can convert into a single, consistent value like "blocked". This process is foundational for security operations because it provides a **unified view** across the network, making it significantly easier to perform **advanced correlation** between different device types, such as matching a firewall traffic block with a failed login event on an authenticator.

Normalization enhances the efficiency of a SOC in several ways:

- **Search Consistency**: Analysts can execute targeted queries across all devices using normalized fields rather than having to recall inconsistent, vendor-specific parameters.
- **Data Enrichment**: Standardized data allows for the addition of essential context, such as **user identity** or real-time **threat intelligence**.
- **Extensive Support**: FortiAnalyzer includes built-in parsers for Fortinet products and third-party applications like Apache and Nginx, with additional premium parsers available through a **Security Automation Service** license.
- **Validation Tools**: Analysts can use the **validate function** to test how specific parsers process raw log entries, which is particularly helpful when integrating third-party logs or troubleshooting parsing errors.

To understand log normalization, imagine a **large international summit** where every delegate speaks a different language. Without a system in place, communication is siloed and chaotic; however, normalization acts as a **team of expert translators** who take every unique speech and convert them all into a single **universal language**. This allows everyone to understand the proceedings instantly, spot patterns in what different delegates are saying, and document the entire event in one consistent record.
## Precision Parsing: Validating FortiAnalyzer Log Logic

The **validate function** in FortiAnalyzer allows analysts to test and verify how a specific log parser processes a raw log entry. This tool is found in the GUI under **Incidents & Events > Log parsers > Log Parsers**. Validation is a critical step when **troubleshooting unexpected parsing issues** or when integrating **third-party logs**, such as those from Apache or Nginx, to ensure the parser's logic correctly interprets the data.

When performing validation, the analyst enters a raw log string into the tool; FortiAnalyzer then displays whether the entry was successfully **"Matched"** and provides a breakdown of the resulting **normalized log fields**. This confirms that the parser is correctly identifying and extracting key fields from raw, unstructured data and mapping them to a **consistent schema** with standardized field names. Beyond just names, validation helps confirm that the parser is **standardizing field values**, such as translating varying vendor terms like "deny" or "block" into a single, unified value like **"blocked"**.

This process is essential because it ensures that the data being inserted and indexed into the **SIEM database** is accurate, allowing for effective real-time monitoring and advanced correlation across the Security Fabric. Furthermore, viewing the results of a validated raw log provides **syntax guidance**, helping analysts identify the exact field names and properties required to build precise **generic text filters** or event handlers.

To understand validation, imagine a **currency exchange machine**: before trusting it to process a large bag of mixed international coins (**third-party logs**), you drop in a single coin to see if the machine recognizes it correctly, displays the right denomination (**field mapping**), and provides the correct value in your local currency (**standardized values**).
## FortiAnalyzer Log Fetching and Archiving Mechanics

**Log fetching** is a specialized feature that allows one FortiAnalyzer to retrieve **archived logs** from another FortiAnalyzer for the purpose of forensic analysis, queries, or report generation. This process effectively moves "offline" compressed logs from a remote unit and **indexes them** into the local unit's SQL database, making them available for immediate analytic support.

### Core Workflow and Roles

The log fetching process follows a client-server architecture:

- **Fetch Client**: The FortiAnalyzer device that initiates the request and receives the logs.
- **Fetch Server**: The remote FortiAnalyzer device that stores and sends the requested archived data.

Log fetching occurs exclusively between two FortiAnalyzer units, and while a single device can perform both roles simultaneously with different units, only **one fetching session** can exist between two specific devices at any given time.

### Implementation Steps

Setting up log fetching requires administrative intervention on both sides:

1. **Create a Profile**: On the fetch client, an administrator with a **Super_User or Standard_User profile** must create a fetch server profile under **System Settings > Advanced > Log Fetch**.
2. **Request Fetch**: The client sends a request where the administrator can specify the **source and target ADOMs, specific devices, date ranges, and filters** to avoid retrieving unnecessary data.
3. **Server Approval**: The request does not execute automatically; the administrator on the fetch server must **review and approve** the request before data transmission begins.

### Key Benefits

This method is superior to manual imports from external backups for several reasons:

- **Granular Selection**: It allows analysts to pinpoint exact devices and time periods to be indexed.
- **Customized Retention**: Fetched logs can have their own **data policy and retention settings** within the destination ADOM, which is helpful when generating reports on very old logs that would normally be purged.
- **Data Integrity**: It helps **avoid log duplication**, a common issue when importing from external backup sources.

### Critical Considerations for Success

To ensure the fetching process works correctly, several technical requirements must be met:

- **Firmware Alignment**: Both the client and server should run the **same firmware version** to ensure all normalized log fields match.
- **ADOM Consistency**: The source and destination Administrative Domains (ADOMs) must be of the **same type**.
- **Resource Management**: The destination ADOM must have **sufficient disk space** allocated to handle the sudden influx of indexed logs.
- **Device Registration**: Even if logs are successfully fetched, they will not be visible on the fetch client unless the corresponding devices have been **added to the local Device Manager**.

Log fetching is much like an **inter-library loan system**: instead of traveling to a remote branch to read a rare, archived book (**remote archived logs**), you request a copy be sent to your local branch. Your local library then catalogs it and puts it on the shelf (**indexing into the SIEM database**), allowing you to study it alongside your local collection until you no longer need it.
## Protocol and Prerequisites for FortiAnalyzer Log Fetching

When using **log fetching** to retrieve and index archived data from a remote FortiAnalyzer, several technical and administrative requirements must be met to ensure a successful session.

### Technical and Configuration Requirements

- **Firmware Alignment**: Both the fetch client and the fetch server should run the **same firmware version** to ensure that all normalized log fields match accurately.
- **ADOM Consistency**: The source Administrative Domain (ADOM) on the server and the destination ADOM on the client **must be of the same type**.
- **Resource Allocation**: You must ensure the destination ADOM has **sufficient disk space allocated** to handle the influx of incoming logs being indexed.
- **Data Policy Constraints**: The **data policy on the fetch client** must be configured to retain logs from the specific time period being requested. If the fetched logs fall outside the client's retention constraints, they will be **automatically deleted**.
- **Device Visibility**: Although you can perform log fetching before adding devices, the retrieved logs will only be **visible on the client** once the corresponding devices have been added to its local **Device Manager**.

### Operational Considerations

- **Session Limits**: You can establish only **one log-fetching session at a time** between any two specific FortiAnalyzer devices.
- **Efficiency and Filtering**: To optimize the process, administrators should use filters in the request window to **select only the specific log types, values, and time frames** required for their analysis,.
- **Administrative Profiles**: Creating a fetch server profile on the client requires an administrator with either a **Super_User or Standard_User profile**.
- **Authorization**: Requests sent by the client are not automatic; they must be **reviewed and approved** by an administrator on the fetch server before transmission begins.

To understand these considerations, imagine **transferring a large collection of rare books** between two library branches: if the new branch doesn't have enough shelf space (**disk allocation**) or uses a completely different filing system (**ADOM type/firmware**), the books cannot be properly cataloged. Furthermore, if the new library has a rule to discard any book older than 50 years (**data policy**), your rare 100-year-old arrivals will be sent straight to the bin as soon as they arrive.