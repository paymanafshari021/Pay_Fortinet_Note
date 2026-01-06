# Firmware upgrade best practices

- Stay current on patch releases for your current major release.
- Only upgrade to a new major release or version when you are looking for specific functionality in the new major release or version. For more information, see the _[FortiAnalyzer Release Notes](https://docs.fortinet.com/document/fortianalyzer/7.4.8/release-notes/723553/)_ on the Fortinet Document Library ([https://docs.fortinet.com/](https://docs.fortinet.com/)), or contact Fortinet Customer Service & Support ([https://support.fortinet.com/](https://support.fortinet.com/)).
- Upgrade FortiAnalyzer before upgrading FortiOS, and be sure to maintain release version compatibility at all times.
### Preparing to Upgrade FortiAnalyzer
#### Summary of Preparation Tasks
- Download release notes, firmware images, and SNMP MIB files (see page 7).
- Review FortiAnalyzer 7.4.8 Release Notes (see page 10).
- Plan upgrade timing (see page 10).
- Review managed device status (see page 10).
- Check system resources and license info (see page 12).
- Back up configuration files and databases (see page 13).
- Back up logs (see page 14).
- Check reports (see page 14).
- Clone VM instances (see page 14).
#### Downloading Files from Customer Service & Support
- Access via https://support.fortinet.com.
- **Release Notes & Firmware (Hardware):**
  - Log in → Download > Firmware Images → Select FortiAnalyzer.
  - Release Notes: Release Notes tab → 7.4.8 Build link → Download.
  - Firmware: Download tab → v7.00 > 7.4 > 7.4.8 → HTTPS link; verify MD5 checksum.
- **Firmware (VM):**
  - Download > VM Images → Select FortiAnalyzer → Platform → Version → Download .out file.
- **SNMP MIB Files:**
  - Download > Firmware Images → v7.00 > 7.4 > 7.4.8 > MIB folder → Download FORTINET-FORTIMANAGER-FORTIANALYZER-MIB.mib via HTTPS.
#### Firmware Image Naming & Details
- **Hardware:** Specific to model (e.g., FAZ_2000E-v7.4.0-build****-FORTINET.out).
- **VM:** Ends in .out; platform-specific (e.g., FAZ_VM64_HV-v7-build2201-FORTINET.out).
- **ESX VM Note:** Post-upgrade, remap network interfaces (port1 = Adapter 1, etc.) for FortiAnalyzer 5.6.0+ on ESX.
- **Build Numbers:** Verify via Dashboard or `get system status` CLI (check Branch Point).
#### Reviewing FortiAnalyzer 7.4.8 Release Notes
- Examine special notices, upgrade info, integration/support, resolved/known issues.
#### Planning When to Upgrade
- Schedule maintenance window; consider test environment.
#### Reviewing Status of Managed Devices
- Ensure 0 devices show Status = Down in Device Manager.
- Use CLI: `diagnose log device` (run pre/post-upgrade to verify quotas).
  - **Example Output Highlights:**
    - Device usage (logs/quarantine/content/IPS), allocated space.
    - ADOM quotas, retention, database usage.
    - Total: logs, database, quota allocation, system storage.
#### Reviewing System Resources and License Information
- Check via System Settings widgets (details on page 12).
#### System Review Requirements
**Before upgrading, check:**
- **License Information widget** in System Settings
- **System Resources widget** for high memory and CPU usage
- **Alert Message Console widget** and toolbar notifications
#### VM-Specific Requirements
**For FortiAnalyzer VMs:**
- Ensure VM partition has **>1024MB**
- Verify VM server is up to date
- Check flash disk size using CLI command: `diagnose system print partitions`
- If needed, increase disk size by shutting down VM, editing hardware, and restarting
#### Backup Procedures
**Configuration Backup:**
- Create system backup file (.dat extension) and save locally
- Verify backup integrity using MD5 checksum in Event Log
- For databases >2.8GB, use FTP/SFTP/SCP via CLI command
**Log Backup:**
- Back up logs before upgrade using: `execute backup logs`
- Consider sending logs to temporary FortiAnalyzer or syslog server during upgrade
#### Additional Precautions
**Reports:**
- Wait for all running reports to complete
- Ensure no reports scheduled during upgrade
- Check status using: `dia report status running` and `dia report status pending`
**VM Snapshots:**
- Stop VM instance before taking snapshot/clone
- Avoid snapshots during active communications
- Enables rollback if upgrade issues occur