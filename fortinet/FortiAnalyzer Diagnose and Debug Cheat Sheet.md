
This cheat sheet compiles essential CLI commands for diagnosing and debugging FortiAnalyzer (FAZ) systems. Commands are categorized for quick reference. Use these in the FAZ CLI (via console, SSH, or GUI CLI console). Always enable debugging with `diagnose debug enable` before running application-specific debugs, and disable with `diagnose debug disable` afterward to avoid performance impact. Debug levels range from 0 (off) to 8 (verbose).

For official details, refer to the FortiAnalyzer CLI Reference (e.g., version 7.6). Commands are based on common usage across versions 6.x–7.x.

## General System Health
| Command | Description |
|---------|-------------|
| `get system status` | Shows general state: version, serial number, HA status, license status, mode (analysis/collector). |
| `get system performance status` | Displays performance stats: CPU, memory, disk I/O (more detailed on hardware appliances). |
| `diagnose sys top` | Real-time list of running processes with CPU/memory usage (press 'q' to quit). |
| `get system uptime` | Shows system load average (similar to Linux `uptime`). |
| `diagnose hardware sysinfo all` | Detailed hardware/system info: CPU, memory, disks, interfaces. |
| `diagnose sys top-summary` | Summary of top processes by CPU/memory. |
| `dmesg` | Kernel messages with syslog severity levels. |
| `diagnose sys kill <signal> <PID>` | Send signal to process (e.g., kill/restart a daemon; use with caution). |

## Disk and Storage
| Command | Description |
|---------|-------------|
| `diagnose sys diskinfo list` | Disk partitions and space used (like `df -h`). |
| `diagnose sys raid list` | RAID status (if applicable). |
| `get system storage-info diskusage` | Per-device log storage usage, including quotas. |
| `diagnose test application filed 9` | Flush file daemon cache (helps with disk sync). |

## Networking and Connectivity
| Command | Description |
|---------|-------------|
| `get system interface` | Interface status and IP config (like `ipconfig`). |
| `diagnose netlink interface list` | Kernel-level interface details. |
| `diagnose sniffer packet <interface> "host <IP>" 4 0 l` | Real-time packet sniffer for FAZ-FGT communication (e.g., port 514; press Ctrl+C to stop). |
| `get router info routing-table all` | IPv4/IPv6 routes (`diagnose sys route list` or `diagnose sys route6 list`). |
| `fnsysctl ifconfig` | Interface config (alternative to `get system interface`). |
| `diagnose netlink brctl list` | Bridge status (if bridging is used). |

## Logging Devices and Logs
| Command | Description |
|---------|-------------|
| `diagnose dvm device list` | List all connected logging devices: IP, serial, uptime, packets received (should increment). |
| `diagnose log device` | Log reception stats per device (last 5/30/60 seconds, logs/sec). |
| `diagnose test application logd 4` | Log daemon status and stats. |
| `execute log filter category <ID>`<br>`execute log display` | Filter and display recent logs (e.g., category 0 for traffic). |
| `diagnose debug application logd 8`<br>`diagnose debug enable` | Enable log daemon debug (level 8 for verbose; monitor for errors). |
| `get log setting` | Current log settings (e.g., SQL vs. flat files). |

## Communication Debug (FAZ ↔ FGT)
| Command | Description |
|---------|-------------|
| On FGT: `execute log fortianalyzer test-connectivity` | Test FAZ connectivity from FortiGate (shows Tx/Rx stats, registration). |
| On FGT: `diagnose sniffer packet any "port 514" 4` | Sniff syslog traffic to FAZ (port 514). |
| On FAZ: `diagnose sniffer packet any "host <FGT-IP>" 4` | Sniff traffic from specific FGT. |
| On FAZ: `diagnose debug application fazd 7`<br>`diagnose debug enable` | Debug FAZ daemon for device communication (level 7). |
| On FAZ: `diagnose test application fazd 1` | FAZ daemon status. |

## Licensing and VM
| Command | Description |
|---------|-------------|
| `get system status` | Basic license status. |
| `diagnose license list` | Detailed license info (e.g., PBDS, SCPC, ISSS expiry). |
| `diagnose debug vminfo` | VM-specific license validation (for VM deployments). |
| `get system limits` | System/VM resource limits (e.g., GB-days). |

## Reporting and SQL (Hard Cache, etc.)
| Command | Description |
|---------|-------------|
| `diagnose sql status sqlreportd` | SQL report daemon status and hard cache (hcache) info. |
| `diagnose sql show hcache-size` | Hcache size on filesystem. |
| `diagnose test application sqlrptcached <level>` | Hcache state (e.g., level 2 for details). |
| `diagnose test application sqlreportd 2` | Diagnose hcache creation process. |
| `diagnose debug application sqlrptd 8` | Enable SQL report daemon debug (verbose for troubleshooting reports). |
| `execute sql-local query "SELECT * FROM $log-traffic where devid='<serial>' LIMIT 10"` | Sample SQL query on logs (replace `<serial>`). |

## Debug Management
| Command | Description |
|---------|-------------|
| `diagnose debug enable` | Enable global debugging (required for app-specific). |
| `diagnose debug disable` | Disable global debugging. |
| `diagnose debug cli <level>` | Set CLI debug level (0–8). |
| `diagnose debug application <app> <level>` | Set debug for app (e.g., `alertmail`, `uploadd`, `logd`; list via `diagnose debug application ?`). |
| `diagnose debug reset` | Reset all debug levels to 0. |
| `diagnose debug info` | Show current debug levels. |
| `diagnose debug crashlog <date>` | View crash logs by date. |
| `diagnose debug coredump {list\|upload\|clear}` | Manage core dumps for crashed processes. |
| `diagnose debug timestamp {enable\|disable}` | Toggle debug timestamps. |
| `diagnose debug kernel <level>` | Kernel debug level. |
| `diagnose debug flow <filter>` | Debug log flows (similar to FGT). |

## Syslog and Backups
| Command | Description |
|---------|-------------|
| `diagnose debug sysinfo elogs` | Display system info event logs. |
| `diagnose debug sysinfo-log-backup <server> <path> <user> <pass>` | Backup sysinfo logs to FTP. |
| `diagnose debug backup-oldformat-script-logs <ip> <path> <user> <pass>` | Backup old script logs to FTP. |

## Example Debug Session
1. Connect via CLI: `ssh admin@<FAZ-IP>`
2. Check health: `get system status`
3. List devices: `diagnose dvm device list`
4. Enable debug: `diagnose debug enable`<br>`diagnose debug application fazd 7`
5. Trigger action (e.g., from FGT: `exec log fortianalyzer test-connectivity`)
6. Monitor output, then: `diagnose debug disable`

**Tips:**
- Use `?` for command help (e.g., `diagnose debug application ?` lists apps).
- High debug levels (6–8) generate verbose output; redirect to file if needed (e.g., via script).
- For HA: Add `diagnose sys ha status` to check cluster.
- Postgres check: `diagnose debug application sqlplugind 8` (look for "postgres is running").

For a printable PDF version, see Yuri Slobodyanyuk's [cheat sheet](https://yurisk.info/assets/fortianalyzer-debug-cheat-sheet.pdf). Additional commands from BOLL's [v7.4 cheat sheet](https://blog.boll.ch/wp-content/uploads/2025/01/CheatSheet-FAZ-FMGR-7.4-v1.2.pdf).