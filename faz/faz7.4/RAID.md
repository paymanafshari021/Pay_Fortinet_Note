### Supported RAID Levels – Summary (FortiAnalyzer 7.6.4)

FortiAnalyzer units with multiple drives support several RAID configurations. Refer to your device’s datasheet for exact compatibility.

---
![[Pasted image 20251109152534.png]]
![[Pasted image 20251109152503.png]]
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