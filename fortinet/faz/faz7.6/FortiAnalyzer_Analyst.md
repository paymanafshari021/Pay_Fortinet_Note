# FortiView 

## Key Characteristics

### Real-time and Historical Data
- FortiView simultaneously displays **live traffic/threats** happening right now AND **historical trends** over time, giving analysts a complete picture of network security posture.

### Analytics Logs Only (NOT Archive Logs)
- FortiView **only displays data from analytics logs** — meaning logs that have been parsed, normalized, and indexed into the **SQL database**.
- **Archive logs** (raw compressed .gz files) are **NOT visible** in FortiView. To view archive logs you must use Log Browse instead.
## Disabling FortiView via CLI
FortiView can be **disabled for performance tuning** using:
```
config system global
    set disable-module fortiview-noc
end
```
When disabled:
- The **GUI hides the FortiView section entirely**
- **Background processing stops** for FortiView features
- This frees up system resources when FortiView is not needed
---
# Retrieving Archived Logs Through Log Fetching

## What is Log Fetching?

Log Fetching is a FortiAnalyzer feature that allows one FortiAnalyzer to **retrieve archived logs from another FortiAnalyzer** device, index that data locally, and then run queries or reports on it for forensic analysis.
## Why Use Log Fetching?

### Key Benefits:

- **Forensic Analysis** — retrieve older archived logs from a remote FortiAnalyzer to investigate past security incidents
- **Selective Indexing** — choose specific **devices** and **time periods** to be indexed, avoiding unnecessary data transfer
- **Custom Retention Settings** — customize log retention settings for the fetched/indexed logs to suit report generation needs on older data
- **Avoid Log Duplication** — prevents duplicate logs that could occur when importing from external backup sources
## How It Works — Roles

|Role|Description|
|---|---|
|**Fetch Client**|The FortiAnalyzer that **requests and retrieves** the logs|
|**Fetch Server**|The FortiAnalyzer that **holds the archived logs** and sends them|

> Important: A single FortiAnalyzer can act as **both** fetch client and fetch server simultaneously — but with **different** FortiAnalyzer devices at each end.

> **Only ONE log-fetching session** can exist at a time between any two FortiAnalyzer devices.
## Step-by-Step Configuration Process

### Navigation Path:

**System Settings > Advanced > Log Fetch**
### Step 1 — Create a Fetch Profile (on the Fetch Client)

The fetch client must create a profile pointing to the fetch server:

- **Name** — profile name (e.g., Fetch-Profile)
- **Server IP** — IP of the remote FortiAnalyzer fetch server (e.g., 10.0.1.210)
- **User** — admin credentials
- **Password** — authentication password

> ⚠️ The user account must have a **Super_User or Standard_User profile** — limited profiles will not work.
### Step 2 — Send the Fetch Request (on the Fetch Client)

After creating the profile, the fetch client sends a fetch request by clicking **"Request Fetch."**

During this step you can specify:

- **Source and target ADOMs** — which ADOM the logs come from and go to
- **Specific devices** — filter which devices' logs to fetch
- **Date ranges** — define the time window for log retrieval
- **Filters** — narrow down which logs to fetch
### Step 3 — Review and Approve/Reject (on the Fetch Server)

The fetch server receives the request and it appears in the queue as **"Waiting for approval."**

The fetch server administrator must then:

- **Review** the incoming request
- **Approve** it (logs are sent to the fetch client) or **Reject** it (request is denied)
---
# Considerations for Using Log Fetching 
## 1. Firmware Version Compatibility

- The **fetch client and fetch server must run the same firmware version**
- This ensures all **log fields match** between both devices
- Mismatched firmware versions can cause log field discrepancies, missing fields, or parsing errors when the fetched logs are indexed on the client
## 2. ADOM Type Compatibility

- The **source and destination ADOMs must be of the same type** (e.g., FortiGate ADOM → FortiGate ADOM)
- Mismatched ADOM types have different log structures and schemas, making the fetched logs incompatible for indexing and reporting
## 3. Destination ADOM Disk Space

- The **destination ADOM must have enough allocated disk space** to accommodate the incoming fetched logs
- Always verify available quota before initiating a fetch request to avoid failed or incomplete log transfers
## 4. Data Policy Constraints

- The **data policy on the fetch client** must be configured to **retain logs from the specified time period** being fetched
- ⚠️ If the fetched logs fall **outside the data policy's retention window**, they will be **automatically deleted** after being received
- Always verify the client's data policy time frame matches or covers the period of the logs being fetched
## 5. Device Manager Registration Requirement

- Fetched logs will only be **visible on the client** if the corresponding source devices are **added to Device Manager** first
- ⚠️ You **can** perform the log fetch **before** adding the devices to Device Manager — but you **won't be able to see the logs** until the devices are registered
- Best practice: add devices to Device Manager **before** initiating the fetch
## 6. Available Fetch Filters

During the fetch request you can apply filters to retrieve only what you need:

|Filter Type|Description|
|---|---|
|**Specific devices**|Fetch logs only from selected devices|
|**Specific log types and values**|Filter by log type, severity, action, etc.|
|**Specific time frame**|Define the exact date/time range to fetch|

> Using filters reduces unnecessary data transfer, speeds up the fetch process, and avoids log duplication.
