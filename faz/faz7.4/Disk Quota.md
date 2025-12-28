| **Log Type**       | **Ratio** | **Disk Space Allocated** | **Log Size (Average)** | **Primary Use**                                                                   |
| ------------------ | --------- | ------------------------ | ---------------------- | --------------------------------------------------------------------------------- |
| **Analytics Logs** | **70%**   | **70 GB**                | **600 bytes**          | Quick searching and reporting (e.g., _What attacks happened in the last 7 days?_) |
| **Archive Logs**   | **30%**   | **30 GB**                | **80 bytes**           | Long-term compliance storage (e.g., _Do we have records from 3 years ago?_)       |

---

The FortiAnalyzer system reserves between 5% to 20% disk space for compression files, upload files, and temporary report files, leaving about 80% to 95% disk space for allocation to devices.

---

```
diag log device
```

![][diaglogdevice.png]

---
To make sure the disk (storage space) doesn’t get full, it uses a system called **disk quota enforcement**.  
That means it **monitors how much space is used** and **automatically deletes old files** if the disk is getting too full.
### **Main Processes Involved**

1. **`logfiled` process**
    - This is like the “watchdog” for the disk.
    - It checks how much space is used every 2 minutes.
    - It monitors other processes and makes sure they don’t exceed their space limits.
    - If the disk is more than **95% full**, it starts deleting old files until space usage is back to **85%**.
---

2. **`sqlplugind` process**
    - This process manages the **SQL database** inside FortiAnalyzer (where logs and reports are stored in a structured way).
    - It makes sure the database doesn’t grow too large.

---

3. **`oftpd` process**
    
    - This process manages **archive files** (older files that are compressed or stored for long-term use).
    - It makes sure these archive files don’t take up too much space.

---
### **How They Work Together**
- Every 2 minutes, `logfiled` checks:
    - How full the disk is.
    - How much space the SQL database and archives are using.
- If usage > **95%**, FortiAnalyzer starts cleaning up until it’s down to **85%**.  
    This automatic cleanup keeps the system running smoothly and prevents crashes from running out of disk space.

---
### Consequences of Insufficient Quota

If you allocate a quota that is too small (**insufficient quota**), it creates several problems:

#### A. Failure to Meet Log Retention Objective

The **log retention objective** is how long you need or want to keep your logs (often required for compliance or historical analysis).

- **Key Concept:** If the disk space allocated to the ADOM fills up too quickly, the system automatically overwrites the oldest logs. If the quota is insufficient, you cannot keep your logs for the required duration.
- **Example:** You have a policy to keep all logs for 90 days. If the ADOM quota is too small, and the space fills up in 60 days, the logs from days 61-90 cannot be stored, and the logs from day 1 start being deleted. You failed to meet your 90-day retention objective.

#### B. Wasting CPU Resources

FortiAnalyzer uses processes to monitor and enforce the disk quota.

- **Key Concept:** When the quota is consistently full, the system spends a lot of power (CPU resources) performing continuous maintenance tasks, such as **log deletion** and **database trims**, just to keep the log data volume below the set limit.
- **Example:** Instead of dedicating CPU power to analyzing data or running reports, the CPU is constantly busy deleting old data and shrinking the database, which is an unnecessary strain on the system.

#### C. Affecting Reporting

FortiAnalyzer relies on logs that are indexed in its database, known as **analytics logs**, to quickly generate reports.

- **Key Concept:** Insufficient quota can **adversely affect reporting** if the system, in its effort to enforce the quota limit, deletes this analytical data _before_ a report that requires it is finished running.
- **Example:** A complex, long-running quarterly report is calculating trends based on analytical data. If the ADOM runs out of space while the report is being compiled, the quota enforcement process might immediately delete the necessary indexed data, causing the report to fail or produce incomplete results.
