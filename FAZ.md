##  Log File Workflow

### The Three-Phase Log Lifecycle

|#|Log Phase|FortiAnalyzer Location|Immediate Analytic Support?|
|---|---|---|---|
|①|Logs received from registered device|Active log file saved with **`.log`** extension|❌ **No**|
|②|Indexed (analytics logs)|Added to **ClickHouse** database and indexed|✅ **Yes — considered _online_**|
|③|Logs rolled over and compressed (archive logs)|Compressed log file saved with **`.gz`** extension|❌ **No — considered _offline_**|
### Key Terminology from the Slide

|Term|Meaning|
|---|---|
|**Analytics logs**|Indexed in the ClickHouse DB, online, immediately searchable|
|**Archive logs**|Compressed `.gz` files on disk, offline, not immediately searchable|
|**ADOM data policy**|Governs both how long logs stay indexed AND how long archived files are kept on disk|
|**ClickHouse**|The SQL database engine FortiAnalyzer 7.6 uses for log indexing (replaces PostgreSQL)|
|**Roll-over**|The process of closing a full `.log` file, compressing it to `.gz`, and opening a new one|
### The Three Log States in Detail

Logs in FortiAnalyzer exist in one of three phases: 
+ a real-time log (just arrived, stored uncompressed in Archive, not yet in the database); 
+ an Archive log (when the real-time log file has been fully inserted, it is compressed and considered offline); 
+ and an Analytics log (indexed in the SQL database and online). Archive logs are critical — they are used to rebuild the database in the event of corruption or during upgrades.

### What Analytics Logs Can Do That Archive Logs Cannot

+ Analytic logs are the only logs used for analysis in Log View (excluding Log Browse), Incidents and Events, and Reports. Analytic logs are dissected during insertion and subtypes are stored as their own category — for example, web filtering logs are sent and stored as Traffic logs when archived, but Analytics extracts the relevant web filtering fields separately.

### The ClickHouse Migration — Critical 7.6 Change

+ Beginning in 7.6.0, FortiAnalyzer stores logs in a ClickHouse SQL database rather than a Postgres SQL database. 
+ If upgrading from an earlier version, the log database will automatically begin migration after upgrading. 
+ During migration, all historical logs insert from the Postgres database to the ClickHouse database — FortiView, Log View, Fabric View, Incidents & Events, and Reports are available during migration, but data will not be accurate until migration completes.

### Storage Size Difference — Analytics vs. Archive

+ Analytic logs indexed in the SQL database require significantly more disk space than Archive logs. 
+ ⚠️ An average indexed (analytics) log is approximately 400 bytes, while an average compressed (archive) log is approximately 50 bytes — roughly an 8:1 ratio.
+ This difference must be considered when specifying the storage ratio for Analytics and Archive logs in the ADOM disk quota settings.

### Data Policy Scope

+ Logs and files are stored on the FortiAnalyzer hard disks and are also temporarily stored in the SQL database. 
+ ⚠️When a SIEM license is added, a separate SIEM database is created to store normalized Fabric logs. 
+ When ADOMs are enabled, data policy settings can be specified per ADOM and apply only to the devices in it. Both global and per-ADOM log storage settings are always simultaneously active.

### Archive Logs and Database Rebuilds

+ FortiAnalyzer's database rebuild is performed using Archive logs — ⚠️ if Archive logs are trimmed before performing a device database rebuild, Analytic logs will not be restored. It is therefore important to review your storage data policy before performing any rebuild.

# Events and Event Handlers
+ In FortiAnalyzer, an event does not just appear automatically from a raw log — it is produced by a mechanism called an event handler. Without an event handler watching for specific conditions, a log entry will be stored but will not become an event.
+ Every log that arrives at FortiAnalyzer — from FortiGate, FortiClient, FortiMail, etc. — is automatically passed through the event handlers.
+ If logs match the conditions configured in an event handler, FortiAnalyzer generates an event

+ 🟡 "Subscription to the Security Automation Service provides FortiAnalyzer with real-time event handlers that can help detect zero-day attacks"

This is a critical note highlighted deliberately on the slide:
+ Security Automation Service (SAS) is a Fortinet subscription service.
+ Subscribing to it gives FortiAnalyzer access to real-time, cloud-updated event handlers.
+ These handlers are specifically designed to detect zero-day attacks — meaning threats that are brand new and may not yet be in traditional signature databases.
+ This keeps FortiAnalyzer's event detection capability current and up-to-date against emerging threats without requiring manual reconfiguration by admins.

+ ❌ Don't confuse logs with events — logs are raw data; events are generated from logs by event handlers when conditions match.
+ ❌ Don't assume FortiAnalyzer generates events from ALL logs — only logs that match event handler conditions become events.
+ ❌ The Security Automation Service is a subscription — it is NOT included by default without a license/subscription.
+ ❌ Zero-day detection requires SAS subscription — this will NOT work with default built-in event handlers alone.

