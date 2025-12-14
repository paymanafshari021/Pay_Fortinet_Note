
https://docs.fortinet.com/document/fortianalyzer/7.4.8/administration-guide/347290/raid-management
### Supported RAID Levels – Summary (FortiAnalyzer 7.6.4)

FortiAnalyzer units with multiple drives support several RAID configurations. Refer to your device’s datasheet for exact compatibility.

---

![](repo_github/faz/faz7.4/attachments/02.png)
![](fortinet-repo/faz/faz7.4/attachments/01.png)
#### **Linear RAID**

- **Function:** Combines all disks into one large virtual disk.
- **Capacity:** Sum of all drives.
- **Protection:** None — single drive failure causes total data loss.
- **Performance:** Minimal change.

---

#### **RAID 0 (Striping)**

- **Drives:** Minimum 2    
- **Capacity:** Sum of all drives.
- **Protection:** None — data lost if any drive fails.
- **Performance:** High (parallel writes).
- **Note:** Not fault-tolerant; avoid for critical systems.

---

#### **RAID 1 (Mirroring)**

- **Drives:** Minimum 2
- **Capacity:** Equivalent to one drive.
- **Protection:** Survives single-drive failure.
- **Performance:** One write / two reads per mirrored pair.
- **Notes:** Simple and redundant; no rebuild required on failure.

---

#### **RAID 1s (RAID 1 + Hot Spare)**

- **Function:** Includes a standby disk that automatically replaces a failed drive.
- **Capacity:** Total disks minus two.
- **Protection:** Automatic rebuild using hot spare.

---

#### **RAID 5 (Striping with Parity)**

- **Drives:** Minimum 3
- **Capacity:** Total disks minus one (for parity).
- **Protection:** Single-drive failure tolerance.
- **Performance:** Fast reads, slower writes; degraded performance during rebuild.

---

#### **RAID 5s (RAID 5 + Hot Spare)**

- **Function:** Hot spare replaces failed drive automatically and rebuilds data.
- **Capacity:** Total disks minus two.

---

#### **RAID 6 (Dual Parity)**

- **Drives:** Minimum 4
- **Protection:** Up to two simultaneous drive failures.
- **Performance:** Similar to RAID 5, slightly slower writes.

---

#### **RAID 6s (RAID 6 + Hot Spare)**

- **Function:** Same as RAID 5s but with dual parity.

---

#### **RAID 10 (1+0)**

- **Drives:** Minimum 4
- **Structure:** Striped mirrors (RAID 0 of RAID 1 arrays).
- **Capacity:** Half of total disks.
- **Protection:** One drive per mirrored pair can fail safely.
- **Performance:** High; ideal when both speed and redundancy are required.

---

#### **RAID 50 (5+0)**

- **Drives:** Minimum 6 (typically ≥9).
- **Structure:** Striped RAID 5 arrays.
- **Capacity:** Total disks minus number of RAID 5 sub-arrays.
- **Protection:** One drive failure per sub-array.
- **Performance:** Better than RAID 5, more efficient than RAID 0.
- **CLI:** Use `diagnose system raid status` to view configuration.

---

#### **RAID 60 (6+0)**

- **Drives:** Minimum 8
- **Structure:** Striped RAID 6 arrays.
- **Protection:** Up to two drive failures per sub-array.
- **Performance:** High read rate, medium write rate; slightly slower than RAID 50.

---
### Configuring the RAID Level – Summary

**Warning:**

- Changing the RAID level **erases all data** on the device.

**Steps to Configure RAID Level:**

1. Navigate to **System Settings > RAID Management**.
2. Click **Change** in the _RAID Level_ field to open the RAID Settings dialog.
3. Choose a new RAID level from the list and click **OK**.
4. The device **reboots** and begins generating the new RAID array, which may take significant time depending on the level selected.
---
### Adding Hard Disks – Summary

**Purpose:**  
Increase FortiAnalyzer storage capacity by installing additional hard disks.

**Recommendations:**

- Use **only Fortinet-supplied disks**; third-party disks are **not supported**.
- Contact a **Fortinet reseller** to purchase extra disks.

**Procedure:**

1. **Obtain compatible disks** (same as Fortinet originals).
2. **Back up log data** or **migrate data** to another FortiAnalyzer to minimize downtime and data loss.
3. **Install new disks**:
    - Hot-swap if supported, otherwise **shut down the unit** before installation.
    - Refer to the _Unit Operation widget_ for guidance.
4. **Configure RAID level** (see related documentation).
5. **Restore log data** from backup if applicable.

---
### RAID Management – Disk Status Summary

**Purpose:**  
The RAID Management page displays disk status and space usage for FortiAnalyzer devices.

**Disk Status Types:**

- **Good:** Disk operates normally.
- **Rebuilding:** Data is being restored to a new disk; array not yet fault-tolerant.
- **Initializing:** Data is written to all disks to establish fault tolerance.
- **Verifying:** System checks parity data integrity on redundant drives.
- **Degraded:** Disk removed from use by RAID controller.
- **Inoperable:** One or more drives missing; data inaccessible.

---
# Viewing RAID Failures

**System Settings > Dashboard > Alert Message Console**

---
# Hot Swapping

This means **removing or inserting a hard drive while the system is still running** — no need to turn it off.

| RAID Type         | Managed By                      | Can You Replace Disk While Running? | Example                        |
| ----------------- | ------------------------------- | ----------------------------------- | ------------------------------ |
| **Hardware RAID** | RAID controller (hardware chip) | ✅ Yes — supports **hot swapping**   | High-end FortiAnalyzer models  |
| **Software RAID** | Operating system (software)     | ❌ No — must **shut down first**     | Lower-end FortiAnalyzer models |

---
# Diagnosing RAID
You can check the RAID and disk status using the following commands for a hardware-based FortiAnalyzer:

| What to investigate                                                                     | CLI command to use                                                                                                                                                                                                                                                                                      |
| --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RAID status, including RAID level,RAID status, <br>RAID size, and hard disk information | `diagnose system raid status`<br>`Mega RAID`        <-- This output shows that the device uses hardware RAID.<br>`Storcli RAID`   <-- Newer models use Storcli RAID. This runs on Mega RAID and is also a hardware RAID.  <br>`Software RAID` <-- This output shows that the device uses software RAID. |
| RAID controller hardware information                                                    | `diagnose system raid hwinfo`                                                                                                                                                                                                                                                                           |
| SMART information                                                                       | `diagnose system disk info`                                                                                                                                                                                                                                                                             |
| SMART health status                                                                     | `diagnose system disk health`                                                                                                                                                                                                                                                                           |
| SMART error logs                                                                        | `diagnose system disk errors`                                                                                                                                                                                                                                                                           |
| Vendor-specific SMART attributes                                                        | `diagnose system disk attributes`                                                                                                                                                                                                                                                                       |

On FortiAnalyzer-VM, only the diagnose system disk usage command is available.

---
# Disk Configuration Recommendations

- **RAID Level**: Use default RAID 50 as per FortiAnalyzer data sheet.
- **Hardware Considerations**: Upgrade hardware if RAID 50 requirements are not met.
### Disk Space Planning
- **Future Needs**: Plan for future storage requirements.
- **Adding Disks**: Expanding an existing RAID array requires rebuilding the array and restoring backups.
- **Available Space**: Depends on RAID level and reserved space for temporary files (used for indexing, reporting, file management).
- **Log Quotas**: Consider both:
    - **Archive**: Original logs received by FortiAnalyzer
    - **Analytics**: Space needed for indexing and analysis
### Allocation Recommendations
- **Default Ratio**: Use Fortinet’s recommended Analytics : Archive ratio for most deployments.
- **Custom Allocation**: Increase Archive space if retaining logs longer than analytical data.
### Virtual Disk Options
- **Adding/Expanding**:
    - Add a new virtual disk for more space
    - Increase existing virtual disk size without reformatting
---
|RAID Level|Min. Drives|Structure / Function|Capacity|Protection|Performance|Notes|
|---|---|---|---|---|---|---|
|**RAID 0** (Striping)|2|Data striped across drives|Sum of all drives|**None** — any drive failure = total data loss|Very high (parallel reads/writes)|Not fault-tolerant; avoid for critical systems|
|**RAID 1** (Mirroring)|2|Full mirror of data across drives|Size of one drive|Survives **1 drive** failure|Good reads (2 reads), normal write|No rebuild needed after failure; simple redundancy|
|**RAID 1s** (RAID 1 + Hot Spare)|3|RAID 1 with automatic standby disk|Total disks − 2|Hot spare auto-replaces failed disk|Same as RAID 1|Adds automatic rebuild capability|
|**RAID 5** (Striping + Parity)|3|Striping with single parity|Total disks − 1|Tolerates **1 drive** failure|Fast reads, slower writes; degraded during rebuild|Common balance of speed + redundancy|
|**RAID 5s** (RAID 5 + Hot Spare)|4|RAID 5 with a hot spare|Total disks − 2|Auto-rebuild with spare|Same as RAID 5|Hot spare inclusion improves recovery time|
|**RAID 6** (Dual Parity)|4|Striping with **2 parity blocks**|Total disks − 2|Tolerates **2 drives** failing|Slightly slower writes than RAID 5|Higher protection than RAID 5|
|**RAID 6s** (RAID 6 + Hot Spare)|5|RAID 6 with hot spare|Total disks − 3|Auto-rebuild with spare|Same as RAID 6|Protects against dual failure + automatic spare|
|**RAID 10** (1+0)|4|Stripe of mirrored pairs|50% of total disks|One drive per mirror pair may fail|Very high; excellent for mixed workloads|Ideal for speed + redundancy|
|**RAID 50** (5+0)|6 (typically ≥9)|Striped RAID 5 arrays|Total disks − number of RAID 5 groups|One drive failure **per sub-array**|High read, faster than RAID 5|Efficient scaling; use `diagnose system raid status` to view|
|**RAID 60** (6+0)|8|Striped RAID 6 arrays|Total disks − 2×(# of sub-arrays)|Two drive failures **per sub-array**|High reads, medium writes|Slower writes than RAID 50; strong redundancy|