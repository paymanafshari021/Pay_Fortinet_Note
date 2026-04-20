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
+ An average indexed (analytics) log is approximately 400 bytes, while an average compressed (archive) log is approximately 50 bytes — roughly an 8:1 ratio.
<mark style="background: css
yellow;color:#FFFFFF;">roughly an 8:1 ratio</mark>
+ This difference must be considered when specifying the storage ratio for Analytics and Archive logs in the ADOM disk quota settings.

### Data Policy Scope

Logs and files are stored on the FortiAnalyzer hard disks and are also temporarily stored in the SQL database. When a SIEM license is added, a separate SIEM database is created to store normalized Fabric logs. When ADOMs are enabled, data policy settings can be specified per ADOM and apply only to the devices in it. Both global and per-ADOM log storage settings are always simultaneously active.

### Archive Logs and Database Rebuilds

FortiAnalyzer's database rebuild is performed using Archive logs — if Archive logs are trimmed before performing a device database rebuild, Analytic logs will not be restored. It is therefore important to review your storage data policy before performing any rebuild.

---

## 🏭 Real-World Application

**Why this matters in a production SOC:**

Understanding the log workflow directly impacts three operational areas:

**1. Incident Response Speed:** Only **Analytics (online) logs** are immediately searchable in Log View and usable in FortiView dashboards. If an incident happened 3 months ago but your ADOM data policy only retains analytics logs for 30 days, those logs have been purged from the database — you'll need to use **Log Browse** (which reads `.gz` archive files directly from disk) to investigate, which is significantly slower.

**2. Storage Planning:** Because analytics logs consume ~8x more disk space than archive logs, an admin must carefully tune the **Analytics-to-Archive disk ratio** in the ADOM settings. Setting the analytics retention too high will rapidly exhaust the indexed quota, causing older analytics logs to be prematurely purged.

**3. Compliance & Audit Readiness:** Regulations like PCI-DSS or ISO 27001 often require log retention for 12 months or more. The **ADOM data policy** must be configured to retain archive `.gz` files for the full required period — even after they have been purged from the analytics database — so they remain available for forensic investigation or audit.

---

> 💡 **Exam tips to remember:**
> 
> - **`.log`** = active, incoming, not yet in the database → **No** analytic support
> - **ClickHouse DB** = indexed analytics logs → **Yes**, immediately searchable (online)
> - **`.gz`** = compressed archive → **No** immediate analytic support (offline)
> - **ADOM data policy** controls BOTH analytics retention AND archive retention — two separate timers
> - **Analytics ≈ 400 bytes/log** vs **Archive ≈ 50 bytes/log** — know the 8:1 ratio
> - **ClickHouse replaced PostgreSQL** starting in FortiAnalyzer **7.6.0** — a very likely exam question for 7.6-specific content
> - Archive logs are the source for **database rebuilds** — never trim them before a rebuild

---

Ready for the next slide whenever you are! 🎯