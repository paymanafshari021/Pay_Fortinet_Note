# Monitoring and Verifying the Upgrade Process on F5 BIG-IP Devices via CLI

The **software upgrade** (or update) on F5 BIG-IP systems typically involves importing an ISO image, installing it to a software volume (often a new one), and then rebooting to the new volume. This is done primarily using the **TMSH** (Traffic Management Shell) CLI.

#### Key Commands for Monitoring and Verification

1. **Check current software volumes, versions, and status**  
   This is the primary command to view installed volumes, active boot location, build versions, and installation status.  
   ```
   tmsh show sys software
   ```  
   Or for more detailed status (including build progress):  
   ```
   tmsh show sys software status
   ```  
   - Look for the **active** volume (marked with `*`).  
   - During installation, it shows progress percentages or statuses like "installing" or "complete".  
   - After reboot, confirm the new version is active.

2. **Monitor the live installation process in real-time**  
   The installation (via `tmsh install sys software ...`) runs in the background. Tail the log file:  
   ```
   tail -f /var/log/liveinstall.log
   ```  
   - This shows detailed progress, any errors, or completion messages.  
   - Common path: `/var/log/liveinstall.log` (mentioned consistently in F5 documentation and procedures).

3. **Before starting an upgrade: Verify no ongoing process**  
   ```
   tmsh show sys software status
   ```  
   - Ensures no prior installation is running.

4. **Typical upgrade installation command (for reference)**  
   To install to a new volume (recommended for major upgrades):  
   ```
   tmsh install sys software image <ISO-file-name>.iso volume HD1.X create-volume
   ```  
   - Replace `HD1.X` with the target volume (e.g., HD1.2).  
   - Monitor with the commands above.

5. **After installation: Reboot to new volume**  
   ```
   tmsh reboot volume HD1.X
   ```  
   - Post-reboot, run `tmsh show sys software` again to verify the new version is active.

6. **Additional verification post-upgrade**  
   - Check configuration load: `tmsh load sys config verify`  
   - View system version: `tmsh show sys version`  
   - Check logs for issues: `cat /var/log/ltm` or other module logs.

#### Best Practices
- Always perform upgrades during a maintenance window, especially for HA pairs (upgrade one unit at a time).  
- Backup first: `tmsh save sys ucs /shared/tmp/backup.ucs`  
- Use serial console access for monitoring reboots in case of network issues.  
- For HA systems, force failover if needed: `tmsh run sys failover standby`  

These commands are consistent across BIG-IP versions (11.x to 17.x+). For version-specific details, refer to official F5 documentation (e.g., my.f5.com articles like K92404240 or K60339442). If issues arise during liveinstall, the `/var/log/liveinstall.log` is the key troubleshooting resource.