#cheatsheet #ha #tshoot #Fortinet 

diagnose sys ha reset-uptime

| **Category**                            | **Command**                                    | **Description**                                                       |
| --------------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------- |
| **General HA Status and Configuration** | `get system ha statu`                          | Shows HA mode, roles, sync status, and failover reasons.              |
|                                         | `get system ha`                                | Displays HA configuration (group-id, group-name, hbdev, priority).    |
|                                         | `get system ha summary`                        | Provides a brief HA cluster overview.                                 |
| **HA Synchronization Status**           | `diagnose sys ha checksum show`                | Shows configuration checksums for global, root, and VDOMs.            |
|                                         | `diagnose sys ha checksum cluster`             | Compares checksums across all cluster members.                        |
|                                         | `diagnose sys ha checksum recalculate`         | Recalculates checksums to fix false sync errors.                      |
|                                         | `execute ha synchronize start`                 | Forces configuration sync from primary to secondary.                  |
|                                         | `execute ha synchronize config`                | Synchronizes configuration settings only.                             |
|                                         | `diagnose sys ha sync-stat`                    | Details synchronization status and file sync failures.                |
| **Heartbeat and Communication**         | `diagnose sys ha heartbeat`                    | Monitors heartbeat sends/receives and failure counts.                 |
|                                         | `diagnose debug application hatalk 7`          | Debugs heartbeat communication (use with `diagnose debug enable`).    |
|                                         | `diagnose debug application hasync -1`         | Debugs HA synchronization process (use with `diagnose debug enable`). |
| **Failover and History**                | `diagnose sys ha history read`                 | Shows historical HA events and failover reasons.                      |
|                                         | `diagnose sys ha dump-by group`                | Details HA group status, link/ping server failures, uptime.           |
|                                         | `diagnose sys ha dump-by vcluster`             | Displays virtual cluster status for multi-VDOM setups.                |
| **Interface and Link Monitoring**       | `diagnose hardware deviceinfo nic <interface>` | Checks status of heartbeat and monitored interfaces.                  |
|                                         | `get system interface`                         | Lists interface configurations, including heartbeat ports.            |
| **Advanced Diagnostics**                | `diagnose sys ha status`                       | Detailed HA status, including member states.                          |
|                                         | `diagnose sys ha check`                        | Verifies HA configuration settings.                                   |
|                                         | `get system ha-nonsync-csum`                   | Shows checksums for non-synchronized items (e.g., hostname).          |
| **Accessing Secondary Units**           | `execute ha manage <id> <username>`            | Connects to secondary unit CLI (use `execute ha manage ?` for IDs).   |
| **Reset and Recovery**                  | `execute ha synchronize stop`                  | Stops ongoing synchronization process.                                |
|                                         | `execute ha disconnect <serial_number>`        | Disconnects a unit from the HA cluster.                               |
|                                         | `diagnose sys ha reset-uptime`                 | Resets HA uptime to trigger failover.                                 |
| **Log Analysis**                        | `execute log filter category 0`                | Filters logs for system events (use with `execute log display`).      |
