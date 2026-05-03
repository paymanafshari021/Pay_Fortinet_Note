# FortiAnalyzer 7.6 Troubleshooting Playbook

**CLI Commands, Outputs, and Diagnostic Workflows for the Field Engineer**

Compiled from:
- *FortiAnalyzer 7.6 Administrator Study Guide*
- *FortiAnalyzer 7.6 Analyst Study Guide*

For Fortinet Expert Troubleshooters.

---

## Table of Contents

1. [How to use this playbook](#how-to-use-this-playbook)
2. [Section 1 — System health baseline](#section-1--system-health-baseline)
3. [Section 2 — Network and system inspection](#section-2--network-and-system-inspection)
4. [Section 3 — Disk quota and log retention](#section-3--disk-quota-and-log-retention)
5. [Section 4 — Device and ADOM discovery](#section-4--device-and-adom-discovery)
6. [Section 5 — Communication troubleshooting (FAZ ↔ FortiGate)](#section-5--communication-troubleshooting-fortianalyzer--fortigate)
7. [Section 6 — Logging issues on FortiAnalyzer](#section-6--logging-issues-on-fortianalyzer)
8. [Section 7 — HA monitoring and troubleshooting](#section-7--ha-monitoring-and-troubleshooting)
9. [Section 8 — Generic debugging framework](#section-8--generic-debugging-framework)
10. [Section 9 — Reports troubleshooting](#section-9--reports-troubleshooting)
11. [Section 10 — Alert email troubleshooting](#section-10--alert-email-troubleshooting)
12. [Section 11 — Upload troubleshooting](#section-11--upload-troubleshooting-logs--reports--cloud)
13. [Section 12 — Database migration (PSQL → ClickHouse)](#section-12--database-migration-postgresql--clickhouse)
14. [Section 13 — Playbook troubleshooting](#section-13--playbook-troubleshooting)
15. [Section 14 — Quick reference card](#section-14--quick-reference-card)

---

## How to use this playbook

This playbook is organized as a workflow: start at Section 1 (system health baseline), narrow down by symptom, and escalate to deeper sections as needed. Each command appears with its purpose, exact syntax, the actual sample output you will see on a live FortiAnalyzer, and a field-by-field explanation of what each value means. Where a command exists on FortiGate (the log source) rather than on FortiAnalyzer, this is called out explicitly.

**Conventions used in this document:**

- `FAZ #` prompt prefix means the command is run on FortiAnalyzer.
- `FortiGate #` prompt prefix means the command is run on the log-source FortiGate, not on FortiAnalyzer.
- **Bold** is used for command names, parameters, and field names that appear in actual command output.
- `Monospace` is used for full command lines and output snippets.

> **Source mapping:** References like `[Admin p.134]` or `[Analyst p.140]` point to the original page in the relevant Fortinet study guide where the command and its sample output are documented.

---

## Section 1 — System health baseline

Always start with the four commands in this section before going deeper. They tell you whether FortiAnalyzer is healthy in itself before you accuse logging, registration, reports, or HA of being the problem.

### 1.1 Basic CLI commands at a glance

These four commands are the universal first sweep on any FortiAnalyzer ticket [Admin p.128]:

| What to investigate | CLI command to use |
|---|---|
| What is the status of FortiAnalyzer? | `get system status` |
| What are the performance statistics on FortiAnalyzer? | `get system performance` |
| What are the hardware statistics for CPU, memory, disk, and RAID? | `diagnose hardware info` |
| Which processes are using the most resources? | `execute top` |

### 1.2 get system status

Returns the general status of the FortiAnalyzer device. Sample output [Admin p.129]:

```
FAZ # get system status
Platform Type             : FAZVM64-KVM
Platform Full Name        : FortiAnalyzer-VM64-KVM
Version                   : v7.6.2 build3415 241212 (GA.F)
Serial Number             : FAZ-VM0000065040
BIOS version              : 04000002
Hostname                  : FAZ
Max Number of Admin Domains : 5
Admin Domain Configuration  : Enabled
FIPS Mode                 : Disabled
HA Mode                   : Stand Alone
Branch Point              : 3415
Release Version Information : GA.F
Current Time              : Tue Mar 18 14:33:00 PDT 2025
Daylight Time Saving      : Yes
Time Zone                 : (GMT-8:00) Pacific Time (US & Canada)
x86-64 Applications       : Yes
Disk Usage                : Free 53.15GB, Total 58.99GB
File System               : Ext4
License Status            : Valid
```

**Critical fields to check when troubleshooting:**

- **`Version`** — confirm the FortiAnalyzer firmware version is compatible with the registering device. Cross-reference with the FortiAnalyzer Release Notes for the supported FortiGate firmware versions.
- **`Admin Domain Configuration`** — must be `Enabled` if you are trying to register a non-FortiGate device (FortiMail, FortiWeb, FortiSandbox, etc.). If this says `Disabled` you cannot use product-specific ADOMs.
- **`Current Time`** — must be accurate. Wrong time breaks scheduling, log timestamps, SSL/TLS sessions to FortiGate, and certificate validation. NTP is strongly recommended over manual setting.
- **`Disk Usage`** — confirm free space exists. If `Free` is near zero, FortiAnalyzer will start dropping or overwriting logs depending on policy.
- **`License Status`** — for FortiAnalyzer-VM only. `Valid` is required for the VM to accept logs at the licensed daily volume.
- **`HA Mode`** — `Stand Alone`, `Master`, or `Slave`. Sets your expectations for the rest of the troubleshooting session.

### 1.3 get system performance

Shows live capacity utilisation. Sample output [Admin p.130]:

```
FAZ # get sys performance
CPU:
    Used:                  37.77%
    Used(Excluded NICE):   37.77%
              %used  %user  %nice  %sys  %idle  %iowait  %irq  %softirq
    CPU0      42.29  18.54  0.00   22.92 57.71  0.62     0.00  0.21
    CPU1      37.32   9.64  0.00   27.46 62.68  0.00     0.00  0.21
Memory:
    Total:                10,264,016 KB
    Used:                  5,222,084 KB    50.9%
    Total (Excluding Swap): 8,166,868 KB
    Used (Excluding Swap):  5,222,084 KB   63.9%
Hard Disk:
    Total:                61,857,580 KB
    Used:                  6,577,288 KB    10.6%
    Inode-Total:          3,932,160
    Inode-Used:               39,714        1.0%
    IOStat: tps  r_tps  w_tps  r_kB/s  w_kB/s  queue  wait_ms  svc_ms  %util
              6.4   0.2    6.3     4.6   115.0    0.0      1.7     0.0    0.0
Flash Disk:
    Total:                 1,007,512 KB
    Used:                    416,460 KB    41.3%
```

**How to read this output:**

- Look at `%used` for CPU, memory, hard disk, and flash disk. If any one of these is **nearing** capacity (not just at 100 %), expect log-collection problems. The space assigned to a hard-disk quota is **not** fully available for logs — some is reserved for system usage and unexpected quota overflow.
- For FortiAnalyzer-VM, **16 GB of memory is the recommended minimum**. Less than that and you will see logging stalls and SQL slowdowns even when CPU looks fine.
- `%iowait` rising while `%user`/`%sys` are normal usually means a slow disk subsystem (RAID rebuild, failing disk, slow datastore on a VM). Cross-check with `diagnose hardware info` RAID section.
- `Inode-Used` can fill up before block usage does — e.g. very many tiny rolled-log fragments. If `Inode-Used %` approaches 100 % the filesystem refuses new files even with free blocks left.

### 1.4 diagnose hardware info

Deep view of CPU, memory, disks, and RAID. Sample output (truncated) [Admin p.131]:

```
FAZ # diagnose hardware info
### CPU info
processor   : 0
vendor_id   : GenuineIntel
cpu family  : 6
model name  : Intel(R) Xeon(R) CPU E5-2680 v3 @ 2.50GHz
cpu MHz     : 2500.000
cache size  : 16384 KB
cpu cores   : 1
flags       : fpu vme de pae tsc msr pae mce cx8 ...
bugs        : cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit

### Memory info
MemTotal:       16412504 kB
MemFree:         7129748 kB
MemAvailable:    9675996 kB
Buffers:           82232 kB
Cached:          4421640 kB
SwapCached:            0 kB
Active:          5799316 kB
Inactive:        2726196 kB
SwapTotal:       2097148 kB
SwapFree:        2097148 kB
AnonPages:       4439328 kB
Mapped:           698592 kB

### Disk info
major  minor  #blocks  name
   1      0    4096    ram0
   1      1    4056    ram1
   1      2    4096    ram2
   1      3    4096    ram3
   7      0   10240    loop0
 254      0  4199424   vda
 254      1  1048576   vda1
 254     16 62914560   vdb
 253      0 62910464   dm-0

### RAID info
n/a

### System time
local time : Tue Mar 18 10:25:55 2025
UTC time   : Tue Mar 18 17:25:55 2025
```

**What this gives you that `get system performance` does not:**

- **Memory info** — granular breakdown vs. the single number from `get system performance`. The `get system performance` total memory includes total + swap. Use this command when you suspect a memory leak or want to confirm whether `SwapFree` is being eaten — heavy swap activity = physical RAM is full and inactive pages are being moved to disk.
- **RAID info** — if RAID is your storage backend, the RAID level affects total disk size and reserved quota. Watch for degraded states here when log writes slow down.
- **Disk info** — confirms physical/virtual disk presence. On VMs, `vdb` / `dm-0` is typically your log disk; a missing entry here is a hypervisor/datastore issue, not a FortiAnalyzer issue.
- **System time** — both local and UTC are shown. A mismatch between these and the device timezone is a red flag for log timestamp problems.

### 1.5 execute top

Real-time process view, similar to Linux `top`. Sample output [Admin p.132]:

```
top - 11:03:59 up 19:30,  0 users,  load average: 0.29, 0.27, 0.22
Tasks: 245 total,   1 running, 244 sleeping,   0 stopped, 0 zombie
%Cpu(s):  1.8/1.0   3[||| ]

  PID  USER       PR  NI  VIRT    RES   %CPU  %MEM   TIME      S COMMAND
 2585  root       20   0  165.3m  133.2m 2.6   0.8    34:19.03  S /bin/python /us
 1028  root       20   0   97.6m   39.0m 1.3   0.2    11:04.17  S fortioldd.main
 1032  root       20   0  833.3m  105.2m 1.3   0.7    12:36.29  S oftpd
 3486  postgres   20   0 2844.2m   30.5m 1.0   0.2     0:20.11  S postgres
  249  root       20   0   87.6m   35.1m 0.7   0.2     5:06.77  S cmdbsvr
 1028  root       20   0  395.1m   39.2m 0.7   0.2    10:54.60  S logfwd.main
 2607  root       20   0  158.6m  128.2m 0.7   0.8     7:22.42  S airflow schedul
    8  root       20   0    0.0m    0.0m 0.3   0.0     1:27.66  I rcu_sched
```

**Workflow:** first run `get system performance` to confirm CPU or memory is high; **then** run `execute top` to find which process is responsible.

**Key processes to recognise in the COMMAND column:**

- `oftpd` — Object File Transfer Protocol daemon. Receives logs from FortiGate. Always-on; high `%CPU` here = heavy log ingest or a sender flooding.
- `postgres` — PostgreSQL backend (event/incident/config DB). Note: starting with 7.6.0 the log database is ClickHouse, not Postgres — Postgres still runs for the event/config data.
- `cmdbsvr` — configuration database server.
- `logfwd.main` — log forwarding to syslog/CEF/SIEM destinations.
- `fortioldd.main` — FortiAnalyzer device management and admin login handling.
- `airflow` — playbook execution engine; should only spike when playbooks run.

**Interactive shortcuts:** press `h` while `execute top` is running to bring up the help window. From there you can sort by `%CPU`, `%MEM`, time, or toggle different summaries.

---

## Section 2 — Network and system inspection

Once you know FortiAnalyzer is alive, confirm its network configuration is what you expect.

### 2.1 Basic CLI commands for system and network settings

Reference table from [Admin p.34]:

| What to investigate | CLI command to use |
|---|---|
| General status of your FortiAnalyzer device | `get system status` |
| Network interfaces (port status, speed, IP) | `get system interface` |
| Configured DNS server addresses | `show system dns` |
| Time setting using NTP server | `show system ntp` |
| Configured static route entries | `show system route` |
| Test connectivity between FortiAnalyzer and another device | `execute ping <remote>` |

**First-word convention in FortiAnalyzer CLI:**

- `get` — view information in a more readable, formatted way.
- `show` — view the exact CLI configuration for that section, with proper indentation (suitable for copy/paste).
- `execute` — perform an action (ping, reboot, format disk, etc.).
- `diagnose` — internal diagnostic commands; deeper than `get`.

Use `?` at any point to see syntax options, and `Tab` to autocomplete or cycle through possible commands.

#### Sample interface output

```
FAZVM64-KVM # get system interface
== [ port1 ]
name: port1   status: disable   ip: 10.0.1.210   255.255.255.0   speed: auto
== [ port2 ]
name: port2   status: enable    ip: 172.16.100.6 255.255.255.0   speed: auto
```

If a port shows `status: disable` but you expect logs to arrive on it — that is your problem, full stop. Re-enable with `config system interface` → `edit portN` → `set status enable`.

### 2.2 diagnose system commands

Detailed system-related information [Admin p.35]:

| What to investigate | CLI command to use |
|---|---|
| NTP server info: IP, stratum, poll time, latency | `diagnose system ntp status` |
| CPU information: vendor, family, model, cores, etc. | `diagnose system print cpuinfo` |
| File system disk space: blocks, used, available, mount points | `diagnose system print df` |
| Static table lookup for host names | `diagnose system print hosts` |
| Network statistics for active connections (proto, addresses, state) | `diagnose system print netstat` |
| Complete routing table (incl. directly connected routes) | `diagnose system print route` |

#### Sample diagnose system print route

```
FAZVM64-KVM # diagnose system print route
Destination     Gateway         Genmask         Flags  Metric  Ref  Use  Iface
10.0.1.0        0.0.0.0         255.255.255.0   U      0       0    0    port1
10.200.1.0      0.0.0.0         255.255.255.0   U      0       0    0    port3
10.200.3.0      10.200.1.254    255.255.255.0   UG     0       0    0    port3
172.16.100.0    0.0.0.0         255.255.255.0   U      0       0    0    port2
```

**Flags:** `U` = up, `G` = gateway. A `UG` entry is a route through a gateway; `U` only is a directly-connected subnet.

### 2.3 Configuration reset commands

Use these very carefully — each is destructive in a different way [Admin p.33]:

- `execute reset all-settings` — erases the show configuration on flash, **including** IP addresses and routes.
- `execute reset all-except-ip` — same as above but **preserves** IP addresses and routes. Useful when you want to keep remote access but blank everything else.
- `execute format disk` — erases all device settings, images, databases, and log data. Preserves IP addresses and routing. **Best practice: run this after resetting the configuration.**
- `execute format disk deep-erase` — low-level format that overwrites the disk with random data, optionally multiple times. Used for compliance or RMA. Can take **days** depending on disk size and rounds specified.

> **Best practice:** Run all of these from the **console port** connection, never SSH. Resetting via SSH risks losing access mid-command if the IP gets cleared.

---

## Section 3 — Disk quota and log retention

Most "my logs are missing" tickets are actually quota tickets. Master these three commands and you will resolve them in minutes.

### 3.1 Reserved disk quota — the math FortiAnalyzer uses

FortiAnalyzer reserves 5–20 % of the disk for system usage and unexpected quota overflow. Only 80–95 % is available to allocate to devices [Admin p.86]:

| Disk size | Reserved system disk quota |
|---|---|
| Small (< 500 GB) | 20 % or 50 GB, whichever is smaller |
| Medium (500 GB – 1000 GB) | 15 % or 100 GB, whichever is smaller |
| Large (1000 GB – 3000 GB) | 10 % or 200 GB, whichever is smaller |
| Very large (3000 GB – 5000 GB) | 5 % or 500 GB, whichever is smaller |

**RAID matters:** the RAID level determines the disk size category. Example — a FortiAnalyzer 1000C with four 1 TB disks in RAID 10 has a usable size of 2 TB → falls in the "Large" bracket → 10 % reserved.

### 3.2 diagnose log device — the master quota view

Quota math at a glance, for every ADOM. Sample output [Admin p.87]:

```
FAZ # diagnose log device

Total Quota Summary:
   Total Quota   Allocated   Available   Allocate%
       48.0GB      19.5GB      28.5GB      40.7%

System Storage Summary:
   Total   Used    Available   Use%
   59.0GB  4.7GB    54.3GB     8.0 %
Reserved space: 11.0GB (10.6% of total space).

                                    Archive                              Database
AdomName     AdomOID  Type Retention Quota   Used / logs/Quaranti/content/ IPS Used%  Retention Quota Used SilenDB hcache Used%
ADOM1            185  FSF  365days   600.0MB 10.7MB(10.7MB/0.0KB/0.0KB/0.0KB)  1.5%   60days    1.4GB 5.5MB 927.4KB 0.0KB 0.4%
ADOM2            207  FGT  365days   300.0MB 104.0KB(104.0KB/0.0KB/0.0KB/0.0KB) 0.0%  60days    700.0MB 0.0KB 0.0KB 0.0KB 0.1%
FortiAnalyzer    122  FAZ  365days   300.0MB 0.0KB(0.0KB/0.0KB/0.0KB/0.0KB)    0.0%  60days    700.0MB 0.0KB 0.0KB 0.0KB 0.0%
FortiCache       126  FCH  365days   300.0MB 0.0KB(...)                        0.0%  60days    700.0MB 0.0KB 0.0KB 0.0KB 0.0%
FortiCarrier     119  FGT  365days   300.0MB 0.0KB(...)                        0.0%  60days    700.0MB 0.0KB 0.0KB 0.0KB 0.0%
... (output truncated) ...
Total usage: 19 ADOMs, logs=10.8MB(10.8MB/0.0KB/0.0KB/0.0KB) database=162.0MB(ADOMs usage:8.4MB(1.0MB,0.0KB) + Internal Usage:154.2MB)
```

**Field-by-field interpretation:**

- **Total Quota** = **System Total** − **Reserved space**. In this example: 59.0 − 11.0 = 48.0 GB.
- **Allocated** = sum of (Archive Quota + Analytics Quota) across **all** ADOMs. Here: 19.5 GB out of 48.0 GB → 40.7 % allocated.
- **Available** = Total Quota − Allocated. Free room you still have to grow ADOM quotas into.
- **Used** (in System Storage Summary) = archive logs + analytics logs + system files mounted on the drive. To get just the system-files component, run `diagnose system print df`.
- **Reserved space** — the 5–20 % carve-out from the table in section 3.1, expressed as GB.
- **Per-ADOM rows** — separated into Archive (compressed offline logs) and Database (analytics — indexed online logs). Each has its own Quota, Used, and Used%. The Database also tracks SilenDB and hcache (the report cache, see section 9).
- **Type** column — `FSF` = Fabric, `FGT` = FortiGate ADOM, `FAZ` = FortiAnalyzer self-logs, `FCH` = FortiCache, `FCT` = FortiClient, etc. Tells you what ADOM type each row is for.

> **Best practice:** Run `diagnose log device` **before and after** any firmware upgrade and save both outputs. After completing the upgrade, run it again to verify device and ADOM disk quotas are still correct [Admin p.105].

### 3.3 diagnose system print df

Linux-style filesystem view. Use it to see what is **actually** on the disks vs. what FortiAnalyzer thinks is there [Admin p.87]. Catches issues like a filesystem mounted read-only after a power loss.

This is also the canonical way to see the system-files share of the **Used** value in `diagnose log device`.

---

## Section 4 — Device and ADOM discovery

Before troubleshooting communication, confirm the device is even known to FortiAnalyzer and is in the ADOM you expect.

### 4.1 Device and ADOM status check commands [Admin p.133]

| What to investigate | CLI command to use |
|---|---|
| Which devices and IP addresses are connected to FortiAnalyzer? | `diagnose test application oftpd 3` |
| Which ADOMs are enabled and configured? | `diagnose dvm adom list` |
| Which devices or VDOMs are currently registered or unregistered? | `diagnose dvm device list` |

### 4.2 Reading diagnose test application oftpd 3

This is the **most useful early-triage command on FortiAnalyzer**. It shows every device that has an active OFTP session — i.e. that is currently connected and able to send logs. If your device is missing here, no log troubleshooting downstream will help; the device simply hasn't connected.

If a registered device **is** in the `diagnose dvm device list` output but **not** in `diagnose test application oftpd 3`, the device is registered but its OFTP session is down — almost always a network/certificate/firewall issue.

### 4.3 Reading diagnose dvm adom list

Lists ADOM name, OID, type, version, members, etc. Confirm:

- The ADOM exists and is **enabled**.
- Its **type** matches the device type you are trying to register (a FortiGate device cannot live in a FortiMail ADOM).
- Its **FortiOS major/minor version** range matches the device firmware. A FortiGate running 7.6 trying to land in a 7.0 ADOM will sit unregistered until you fix the ADOM version or move it.

### 4.4 Reading diagnose dvm device list

Lists every registered device with its serial number, IP, ADOM, status, and HA mode. Look for devices in status `Down` before troubleshooting their logs — and as a pre-upgrade check, confirm **no** device is in `Status = Down` [Admin p.105].

---

## Section 5 — Communication troubleshooting (FortiAnalyzer ↔ FortiGate)

This is the busiest part of any FortiAnalyzer engineer's day. The structured workflow below comes straight from the courseware.

### 5.1 The five-question checklist [Admin p.134]

| What to investigate | CLI command to use |
|---|---|
| Can the devices contact each other? | `execute ping` (FAZ or FGT) |
| Is FortiAnalyzer receiving logs? | `diagnose debug application oftpd 8` |
| Is FortiGate configured for remote logging to FortiAnalyzer? | FortiGate: `show log fortianalyzer setting` |
| Is the FortiAnalyzer source IP address set on FortiGate? | FortiGate: `show log fortianalyzer setting` |
| Are the logging filters for logs sent to FortiAnalyzer enabled on FortiGate? | FortiGate: `show log fortianalyzer filter` |
| Is FortiGate capable of generating logs? | FortiGate: `diagnose log test` |

**Other questions to keep in mind [Admin p.134]:**

- Is FortiGate configured for remote logging to FortiAnalyzer at all?
- Is the FortiAnalyzer **source IP** set on FortiGate? This matters when FortiAnalyzer is reached over a VPN that allows only a specific subnet — without setting source IP, FortiGate uses the egress interface IP and the VPN ACL drops the traffic.
- Can FortiGate even generate the logs you expect? If you don't see logs locally on FortiGate, fix that first — the FortiAnalyzer side is innocent.

### 5.2 execute ping

Run from FortiAnalyzer to FortiGate **and** from FortiGate to FortiAnalyzer. Both directions must work. ICMP must be permitted by every intermediate firewall — if `execute ping` works but logs don't arrive, ICMP is open but TCP/514 (or UDP/514, depending on protocol) isn't.

### 5.3 diagnose sniffer packet — FortiAnalyzer's built-in tcpdump

FortiAnalyzer has a built-in packet sniffer with versatile filters and verbosity 1–3 [Admin p.135]. Higher verbosity = more detail. For Fortinet TAC tickets, level-3 captures are typically required.

**Syntax:**

```
diagnose sniffer packet <interface> <filter> <level> <count> <timestamp>
```

**Example — capture 4 packets of UDP/53 (DNS) on port1 with timestamps:**

```
FAZ # diagnose sniffer packet port1 'udp and port 53' 1 4 l
interfaces=[port1]
filters=[udp and port 53]
2025-02-28 16:29:17.741947 192.168.42.210.14610 -> 208.91.112.52.53: udp 27
2025-02-28 16:29:17.742016 192.168.42.210.14610 -> 208.91.112.52.53: udp 27
2025-02-28 16:29:17.745001 208.91.112.52.53 -> 192.168.42.210.14610: udp 155
2025-02-28 16:29:17.745047 208.91.112.52.53 -> 192.168.42.210.14610: udp 195
```

**Capture characteristics in the example:**

- Interface: `port1`
- Filter: `'udp and port 53'` (BPF syntax — same as tcpdump)
- Verbosity level: `1` (basic header info)
- Count: `4` (stop after 4 packets — use `0` for unlimited until Ctrl-C)
- Timestamp: `l` = local time. Other options: `a` = absolute UTC, `3` = elapsed since first packet, etc.

**Verbosity levels:**

- `1` — IP/TCP/UDP header summary (one line per packet).
- `2` — adds payload as printable ASCII.
- `3` — adds payload as hex + ASCII (the format TAC wants for log-transport SSL/TLS issues).

If `diagnose sniffer packet` sees packets leaving FortiGate but FortiAnalyzer's sniffer doesn't see them arriving, an intermediate router or firewall is blocking or misrouting — the issue is in the network, not on either Fortinet device.

FortiAnalyzer also has a GUI capture tool at **System Settings > Network** — same engine, same filters, easier UI for one-off captures.

### 5.4 The three-command workflow for log-arrival problems

This is the canonical workflow for proving end-to-end logging works [Admin p.136]:

**Step 1 — On FortiAnalyzer:** enable OFTP debugging at level 8 to watch incoming sessions in real time:

```
FortiAnalyzer # diagnose debug application oftpd 8
```

**Step 2 — On FortiGate:** generate test logs of every type:

```
Local FortiGate # diagnose log test
generating a system event message with level - warning
generating an infected virus message with level - warning
generating a blocked virus message with level - warning
generating a URL block message with level - warning
generating a DLP message with level - warning
generating an IPS log message
generating an botnet log message
```

**Step 3 — On FortiAnalyzer:** verify the logs are received. The OFTP debug should show entries like:

```
FortiAnalyzer # diagnose debug application oftpd 8
OFTP_destroy_SSL_context:1898 FGVM010000064692] SSL socket[24] pid[988] ssl[0x162d260]
destroy_SSL_context
[OFTP_recv_SSL_packet:1792 FGVM010000064692] SSL socket[27] pid[988] ssl[0x1d60b30] received [12] bytes:
[oftpd_handle_session:3656 FGVM010000064692] handle KEEPALIVE (11)
[OFTP_send_SSL_packet:1852 FGVM010000064692] SSL socket[27] pid[988] ssl[0x1d60b30] sent [21] bytes:
```

**Reading the OFTP debug output:**

- `FGVM010000064692` — serial number of the FortiGate that opened the SSL session. Confirms which device is talking.
- `SSL socket[24]` — file descriptor; correlate the same number across log lines to follow one session.
- `pid[988]` — OS pid of the oftpd worker handling that session.
- `handle KEEPALIVE (11)` — periodic OFTP keepalive. Frequent KEEPALIVEs without data = device is connected but not logging anything.
- `received [N] bytes` / `sent [N] bytes` — payload sizes. A healthy logging device will show **received** growing over time.
- `OFTP_destroy_SSL_context` — the session ended. Frequent destroys for the same FGT = flapping (certificate, MTU, NAT keepalive issue).

> **Order matters:** The `diagnose debug application oftpd 8` command on FortiAnalyzer **must already be running** before you run `diagnose log test` on FortiGate. Start the debug, **then** generate the test logs.

**Disable the debug afterwards:** `diagnose debug disable` — high-level debugs eat CPU.

### 5.5 FortiAnalyzer temporarily unavailable — what FortiGate does

If FortiAnalyzer becomes unreachable, FortiGate uses its `miglogd` process to cache logs in memory. The cache has a maximum size; once it's full, oldest cached logs are dropped first. When FortiAnalyzer is reachable again, `miglogd` sends the cached logs [Admin p.137].

**Important:** the FortiGate buffer keeps logs long enough to sustain a **FortiAnalyzer reboot** (e.g. firmware upgrade) — it is **not** intended for lengthy outages.

#### Diagnose commands to run on FortiGate during the outage

**Cache stats:**

```
Remote-FortiGate # diagnose test application miglogd 6
mem=0, disk=9036, alert=0, alarm=0, sys=0, faz=3113, faz-cloud=0, webt=0, fds=0
interface-missed=170
```

- `disk=9036` — number of logs queued on disk buffer (only on SSD-equipped FortiGates).
- `faz=3113` — logs queued for FortiAnalyzer (in memory).
- `interface-missed=170` — packets the kernel logging interface couldn't process. Rising = the FortiGate is generating logs faster than miglogd can drain them.

**Per-FortiAnalyzer / cloud stats:**

```
Remote-FortiGate # diagnose test application fgtlogd 4
Queues in all miglogds: cur:0  total-so-far:23437
global log dev statistics:
faz=3115, faz_cloud=0, fds_log=0
faz 0: sent=3087, failed=0, cached=0, dropped=0
```

- `Queues in all miglogds: cur:0` — current depth across all miglogd instances. `total-so-far` is cumulative since boot.
- `sent` — successfully transmitted to FortiAnalyzer.
- `failed` — increases on bursts or when the link is overloaded.
- `cached` — increases when FortiAnalyzer is unavailable; this is your live indicator that the connection has been broken.
- `dropped` — logs lost (cache overflowed). Anything non-zero is a problem.

**Kernel statistics — has anything been dropped at the OS layer?**

```
Remote-FortiGate # diagnose log kernel-stats
fgtlog: 1
fgtlog 0: total-log=16838, failed-log=0, log-in-queue=0
```

- `failed-log` increases if the cache is full and logs must be dropped at kernel level. Always investigate when this is non-zero — adjust `faz-disk-buffer-size` or fix the log-rate problem at source.

#### Configuring the FortiGate disk buffer

FortiGate devices with an SSD have a configurable on-disk log buffer. If memory is full and FortiAnalyzer is unreachable, logs spill to disk and replay seamlessly when the connection returns:

```
config system global
    set faz-disk-buffer-size <integer>   ; disk queue size of FortiAnalyzer
                                          ; range 0-4506 MB (0 = disabled)
end
```

---

## Section 6 — Logging issues on FortiAnalyzer

Once you have confirmed FortiGate is sending and FortiAnalyzer is receiving, this section diagnoses logging **performance** problems — slow ingest, lag, ADOM imbalance.

### 6.1 Troubleshooting Logging Issues — fortilogd commands [Admin p.138]

| What to investigate | CLI command to use |
|---|---|
| What is the current log rate on FortiAnalyzer? | `diagnose fortilogd lograte` |
| What is the log rate per ADOM? | `diagnose fortilogd lograte-adom` |
| What is the log rate per device? | `diagnose fortilogd lograte-device [filter]` |
| What is the log message rate? | `diagnose fortilogd msgrate` |
| What is the status of the FortiLog daemon? | `diagnose fortilogd status` |

### 6.2 Analyst-flavour log rate commands [Analyst p.139]

The Analyst guide adds these for deeper investigation:

| What to investigate | CLI command to use |
|---|---|
| Log receive rate for each second? | `diagnose fortilogd lograte` |
| Log receive rate totals? | `diagnose fortilogd lograte-total` |
| Device log rate? | `diagnose fortilogd lograte-device` |
| Log rate for each log type? | `diagnose fortilogd lograte-type` |
| Message receive rate per second? | `diagnose fortilogd msgrate` |
| SQL insertion status? | `diagnose sql status sqlplugind` |
| Device log usage for all logging devices? | `diagnose log device` |

#### Sample lograte / msgrate output [Analyst p.139]

```
FAZVM64-KVM # diagnose fortilogd lograte
last 5 seconds: 0.6,  last 30 seconds: 2.2,  last 60 seconds: 1.7

FAZVM64-KVM # diagnose fortilogd msgrate
last 5 seconds: 0.2,  last 30 seconds: 0.4,  last 60 seconds: 0.4
```

**Critical distinction:**

- **Log rate** = number of **logs** received per second.
- **Message rate** = number of **messages** received per second.
- In FortiOS 7.x, one transport-level message can carry multiple logs in LZ4-compressed format. So `lograte` is typically higher than `msgrate`. When `msgrate` is high but `lograte` is low, your sender is using small packets — likely a tuning or MTU issue.

### 6.3 Log volume per ADOM [Analyst p.140]

These commands answer "how much disk are my ADOMs actually consuming?" — vital for MSSPs running multi-tenant FortiAnalyzers:

| What to investigate | CLI command to use |
|---|---|
| Log receive rate for all ADOMs or a specific ADOM | `diagnose fortilogd lograte-adom {all \| adom-name}` |
| Log volume for all ADOMs or a specific ADOM | `diagnose fortilogd logvol-adom {all \| adom-name}` |

#### Sample logvol-adom output

```
FAZVM64-KVM # diagnose fortilogd logvol-adom root
2025-04-22  2025-04-21  2025-04-20  2025-04-19  2025-04-18  2025-04-17  2025-04-16  average
adom 'root':
  8.77 MB     10.15 MB    15.65 MB    17.68 MB    22.89 MB    22.64 MB    22.71 MB    17.21 MB
```

**Reading this:** the rightmost value is the 7-day average. Sudden spikes from the average (e.g. one day at 50 MB when the average is 17 MB) indicate a misbehaving device or new logging configuration in that ADOM.

### 6.4 GUI dashboard widgets — Insert Rate vs Receive Rate, and Lag Time

On the GUI dashboard, three widgets are essential [Analyst p.141]. If they are not visible, click **Toggle Widgets** in the upper-left corner and add them.

**Insert Rate vs Receive Rate widget:**

- **Receive rate** = the rate at which raw logs reach FortiAnalyzer. Handled by the `fortilogd` daemon.
- **Insert rate** = the rate at which logs are indexed into the SQL database. Handled by the `sqlplugind` daemon.
- The difference between receive and insert should be small and consistent. Variations during the day are expected. **Establish a baseline during normal operation** and compare to spot performance regressions.
- If **receive >> insert** persistently, FortiAnalyzer is taking logs in faster than it can index them — eventually you will see growing lag and the SQL database falling behind.

**Log Insert Lag Time widget:**

- Shows the delay between when a log is received and when it is inserted into the database. Should be as small as possible, with only occasional spikes during heavy activity.
- Build a baseline so you recognise abnormal spikes that warrant investigation.
- Sustained high lag = `sqlplugind` can't keep up. Check CPU, disk I/O, and ADOM count — too many small ADOMs creates indexing overhead.

---

## Section 7 — HA monitoring and troubleshooting

FortiAnalyzer HA = up to four devices: one primary and up to three secondary. Configuration and logs sync between members. Failover is triggered by network reachability between members by default; you can also enable Postgres-process health checks [Admin p.97–101].

### 7.1 Cluster Status fields

Available in GUI under **System Settings > HA**, and via CLI with `diagnose ha status` [Admin p.101]:

- **Role** — Primary or Secondary.
- **Serial Number** — confirm correct unit.
- **IP** — heartbeat-interface IP of each cluster member.
- **Host Name** — sanity check.
- **Uptime / Downtime** — high downtime on a secondary = something to investigate.
- **Initial Logs Sync** — "Syncing 100 %" while initial sync runs; "In-Sync" once complete. A new secondary will reboot and rebuild its log database when initial sync finishes.
- **Configuration Sync** — "In-Sync" or "Config will be synced to secondaries" or an error.

### 7.2 HA diagnose commands [Admin p.101]

| What to investigate | CLI command to use |
|---|---|
| Show HA status (same info as Cluster Status page) | `diagnose ha status` |
| Show HA statistics | `diagnose ha stats` |
| Dump HA data log | `diagnose ha dump-datalog` |
| Force HA failover (run on master) | `diagnose ha failover` |
| Force HA configuration resync | `diagnose ha force-cfg-resync` |
| Show HA load balance status | `diagnose ha load-balance` |
| Restart HA initial sync (run on master) | `diagnose ha restart-init-sync` |

### 7.3 Common HA scenarios and the right command

- **Cluster appears split-brain (each unit thinks it's primary)** — check heartbeat IP reachability with `execute ping` between members; `diagnose ha status` on each unit; verify that the cluster ID and HA password match.
- **Configuration changes not propagating** — `diagnose ha force-cfg-resync` on the primary forces a resync push to all secondaries.
- **Secondary stuck at "Syncing 50 %"** for hours — `diagnose ha restart-init-sync` on the primary restarts the initial log sync from scratch.
- **Need to plan a failover** — `diagnose ha failover` on the master triggers a graceful failover. Useful before maintenance on the current primary.
- **Reports not load-balancing across members** — `diagnose ha load-balance` shows which unit took which job. Round-robin per report; each report syncs to all members so it remains visible cluster-wide.

> **Firmware-mismatch caveat:** During an HA upgrade, the firmware versions of cluster members will not match for a window. This **disables configuration sync** — but other sync operations continue to function. You will see "firmware version mismatch" messages until **all** cluster members reach the same version [Admin p.108].

### 7.4 Pre-upgrade checklist [Admin p.105]

Before any FortiAnalyzer firmware upgrade — standalone or HA — work through this checklist:

- Download release notes **and** firmware images from the Fortinet Customer Service & Support portal.
- Open Device Manager and confirm **no** registered device has Status = Down.
- Run `diagnose log device` and **save its output**. After upgrade, run it again and compare to verify ADOM/device disk quotas survived.
- Back up the FortiAnalyzer configuration files and databases.
- Back up logs (recommended).
- Wait for all running reports to finish, and ensure no reports are scheduled to run during the upgrade window.

**HA upgrade order:** upgrade **secondaries first**; upgrade the primary **last**, after all secondaries have synced. When you upgrade the primary, one of the synced secondaries is auto-selected as the new primary so the cluster stays online during the upgrade.

---

## Section 8 — Generic debugging framework

Every targeted debug in this playbook uses this framework underneath. Master it and you can debug any FortiAnalyzer process [Admin p.182].

### 8.1 The four core commands

```
diagnose debug enable                       ; turn on debug output to console
diagnose debug disable                      ; turn off debug output

diagnose debug application <process> <level>  ; enable debug for one process
                                              ; level 0–255 (-1 = disable)
                                              ; level 8 = most detailed for most processes

diagnose debug info                         ; show which level each process is at

diagnose debug timestamp enable             ; prefix each debug line with a timestamp
execute tac report                          ; full dump of every diagnostic command — for TAC
```

### 8.2 Sample diagnose debug info output

```
FortiAnalyzer # diagnose debug info
General
    cli debug level:                3
    console debug output:           enable
    debug timestamps:               disable
    terminal session debug output:  disable
    terminal session data masking:  disable

Application
    dlmd debug filter:              disable
    fazmaild debug level:           8
    fgfmsd debug filter:            disable

FortiAnalyzer #
```

**Reading the output:**

- **`cli debug level`** — global verbosity for CLI debug output. 3 is normal; raise it for deep traces.
- **`console debug output`** — when enabled, debugs print to the console you are connected on.
- **`debug timestamps`** — turn on with `diagnose debug timestamp enable` to make traces correlate-able with logs.
- **`terminal session data masking`** — when enabled, hides credentials and PII in the debug stream. Enable when sharing output with TAC.
- **Application section** — currently active per-process debug levels. In the example above, `fazmaild` is at level 8 (full email-sending trace).

### 8.3 Best practice for debug sessions

- Always pair `diagnose debug application <proc> <level>` with `diagnose debug enable` — the `application` line targets a process; the `enable` line turns the firehose on.
- Always end with `diagnose debug disable` **and** `diagnose debug application <proc> -1` to clear the per-process level. Stale debugs eat CPU.
- Capture to a log file from your terminal client (PuTTY logging, screen logging, etc.) — debug streams scroll fast.
- Use `execute tac report` to collect a full diagnostic snapshot whenever opening a Fortinet TAC ticket.

---

## Section 9 — Reports troubleshooting

Reports are the most common feature where users say "it's slow" or "it's empty". This section's commands answer both.

### 9.1 Why hcache matters

FortiAnalyzer pre-compiles report data into **hcache** — a proprietary persistent on-disk cache stored as database tables. Unlike normal caches, hcache **does not** expire on time; it stays until invalidated [Admin p.188 / Analyst p.182].

- If hcache exists when you run a report, the report builds quickly from cached aggregations.
- If hcache does not exist, FortiAnalyzer must **first** build it, **then** run the report. Slow first run is normal.
- **Auto-cache** — when enabled in report settings, hcache updates automatically as new logs arrive. **Scheduled reports have auto-cache enabled by default.** Enable it manually for ad-hoc reports if you can spare the system resources.
- **Extended Log Filtering** — caches specific log fields for faster filtering on subsequent runs.

### 9.2 Report Debugging — sqlreportd [Admin p.183]

The `sqlreportd` daemon is responsible for generating reports — it sends queries to the `sqlpugiund` daemon (the SQL execution layer).

| What to investigate | CLI command to use |
|---|---|
| Check sqlreportd process status | `diagnose sql status sqlreportd` |
| List all generated reports for an ADOM | `execute sql-report list <ADOM>` |
| View report content | `execute sql-report view report-data <ADOM> <report-name>` |

#### Sample execute sql-report list output

```
FortiAnalyzer # exec sql-report list ADOM1
No <days-range> input, search for recent 7 days's reports.
"Security Analysis-2025-06-06-0800-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_117"
"Daily Summary Report-2025-06-05-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_103"
"Daily Summary Report-2025-06-04-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_07"
"Daily Summary Report-2025-06-03-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_00"
"Daily Summary Report-2025-06-02-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_01"
"Daily Summary Report-2025-06-01-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_75"
"Daily Summary Report-2025-05-31-0300-0700_f6f85be6-4320-41a1-89cd-02d634d5e06f_67"
```

**Reading the report-name format:** `<title>-<YYYY-MM-DD-HHMM-TZ>_<UUID>_<seq>`. Use the full string with `execute sql-report view report-data` to retrieve the rendered content.

### 9.3 Report queue management [Admin p.184]

```
FortiAnalyzer # diagnose report status
Max pending rpts: 100000
Current pendings: 0
Max running rpts: 1
Current runnings: 0
Semaphore state : initialized (1)
Sem value       : 1 unlocked

Longest run report: adom[ADOM1], title:Security Analysis, duration:1m36s
```

**Reading this:**

- `Max pending rpts` — queue capacity.
- `Current pendings` — reports waiting to start. If this grows and isn't draining, you have a backlog.
- `Max running rpts` — concurrent execution slots.
- `Current runnings` — reports currently executing.
- `Semaphore state` / `Sem value` — internal locking state for the report engine. `locked` with current runnings = 0 is a stuck state — clear with `diagnose report clean`.
- `Longest run report` — useful for identifying the slow report that may be blocking the queue.

**Stuck or slow reports — wipe the queue:**

```
FortiAnalyzer # diagnose report clean
```

Too many reports waiting in the queue can hang or slow FortiAnalyzer. `diagnose report clean` wipes the queue. After the clean, restart any business-critical reports manually.

**Run a report from CLI with full debugging visible:**

```
FortiAnalyzer # exec sql-report run ADOM1
<name/title>     Please select one of the available SQL report schedule names or titles.
ADOM: ADOM1
NAME    TITLE
1       Security Analysis
10000   User Security Analysis
10002   Bandwidth and Applications Report
10003   Email Report
10004   Admin and System Events Report
10005   Threat Report
10007   IPS Report
10008   Detailed Application Usage and Risk
10009   Top 20 Categories and Applications (Bandwidth)
10010   Top 20 Categories and Applications (Session)
10011   Top Allowed and Blocked with Timestamps
10012   User Detailed Browsing Log
10013   Hourly Website Hits
10014   Top 20 Category and Websites (Bandwidth)
10015   Top 20 Category and Websites (Session)
10016   Top 500 Sessions by Bandwidth
10017   User Top 500 Websites by Bandwidth
```

Pick a report name or ID and run `execute sql-report run <ADOM> <name/title>`. The CLI streams **very useful** debug output you don't see when running the same report from the GUI.

### 9.4 Report Troubleshooting CLI Commands [Analyst p.188]

This is the canonical reference table from the Analyst guide for any "reports are slow" ticket:

| What to investigate | CLI command to use |
|---|---|
| What is the SQL insertion status? | `diagnose sql status sqlplugind` |
| What are the SQL query connections and hcache status? | `diagnose sql status sqlreportd` |
| What are log file-related activities (rolled, deleted, uploaded)? Is hcache creation able to catch up? | `diagnose test application logfiled 2` |
| What are the current SQL processes running (any log queries)? | `diagnose sql process list` |
| What is the configuration status of all configured reports? | `execute sql-report list-schedule <ADOM>` |
| Is the hcache creation able to catch up? | `diagnose test application sqlrptcached 2` |
| What is the state of the hcache? | `diagnose sql hcache status` |
| What is the hcache size on the file system? | `diagnose sql show hcache-size` |

### 9.5 Report performance — five-step checklist [Analyst p.187]

- Run diagnostics on the report and look at the report summary at the end. Inspect the **hcache time** — how long it took to build.
- Check log rates with `diagnose fortilogd lograte` / `lograte-adom` to get a picture of log volumes.
- Check insert rate, receive rate, and log insert lag. Receive rate >> insert rate is a red flag.
- Enable **auto-cache** in report settings to cut subsequent generation time.
- If many similar reports run on many FortiGates, configure `config system report group` — fewer hcache tables, faster auto-cache and report generation. After configuring grouping, **rebuild** the report hcache tables.

### 9.6 Empty or wrong-data reports [Analyst p.189]

- Check the time frame covered by the report (printed in the report header).
- Verify you have logs for that time frame **and** for the device the report ran for. Common cause: logs overwritten too quickly. Fix: increase ADOM disk quota and retention.
- Test the dataset alone in the GUI (Reports > Advanced > Dataset, the **Go** feature). If it returns wrong data the SQL query is the problem, not the report.
- Check Advanced Settings — **user obfuscation** can produce unrecognisable usernames. Check filters attached to the report — they may be filtering out the very logs you want.

---

## Section 10 — Alert email troubleshooting

All email-sending tasks on FortiAnalyzer (alerts, scheduled report delivery, event-management notifications) go through the `fazmaild` daemon [Admin p.185].

### 10.1 Email debugging commands

| What to investigate | CLI command to use |
|---|---|
| Watch all reports and event-management mails being sent | `diagnose debug application fazmaild 8` |
| Test SMTP connection to a specific mail server | `diagnose test connection mailserver "Mailserver:10.0.2.254"` |
| Show 0-level email statistics | `diagnose test application fazmaild 0` |
| Show 1-level email statistics | `diagnose test application fazmaild 1` |
| Show 2-level email statistics (count of emails sent) | `diagnose test application fazmaild 2` |

### 10.2 Sample fazmaild 2 output

```
FortiAnalyzer # diagnose test application fazmaild 2
total sent mail count:     0
total failed sendmail count: 0
total discard mail count:  0
last sendmail time:        Wed Dec 31 16:00:00 1969
last request time:         Fri Oct 11 12:56:20 2024
queue:                     0/5000
queue timeout:             1800 seconds
```

**Reading this:**

- `total sent mail count` / `total failed sendmail count` — running tallies. Failed counts above zero mean SMTP rejection. Use `diagnose debug application fazmaild 8` to capture the exact SMTP server response.
- `total discard mail count` — emails discarded (queue timeout, malformed, etc.).
- `last sendmail time = Wed Dec 31 16:00:00 1969` — Unix epoch zero. This is the **never sent any email** sentinel — confirms email has not been used since boot.
- `queue: 0/5000` — current depth / maximum. A growing queue means the SMTP server is not accepting outbound mail fast enough.
- `queue timeout: 1800 seconds` — emails older than 30 minutes are dropped from the queue.

### 10.3 Live email debug example workflow

- Open a console session with logging enabled.
- Run `diagnose debug application fazmaild 8` then `diagnose debug enable`.
- Trigger a test email — e.g. send a test mail from the System Settings > Mail Server page in the GUI, or wait for a scheduled report.
- Watch the SMTP transaction in the debug stream. Common rejections: 5xx "sender domain not allowed" (set up SPF/relay), 4xx "too many connections" (rate limit on the mail relay).
- Stop with `diagnose debug disable` and `diagnose debug application fazmaild -1`.

---

## Section 11 — Upload troubleshooting (logs / reports / cloud)

The `uploadd` process is responsible for uploading logs and reports to a remote server (FTP/SFTP/SCP) and to cloud storage [Admin p.186].

### 11.1 Upload diagnose commands

| What to investigate | CLI command to use |
|---|---|
| Debug rolled-log uploads or failed FTP uploads | `diagnose debug application uploadd 8` |
| See the upload queue | `diagnose upload status` |
| Clear the upload queue | `diagnose upload clear` |

### 11.2 Sample diagnose upload status

```
FortiAnalyzer # diagnose upload status
------upload log file stats------
upload mode: present, uploading 60 logs e 30 seconds 65 records later.

queue:    log[0] / bkup[0] / m1[0] / m2[0] / m3[0]
prepare:  done[53] / failed[0] / locate err[0] / time: total[0s], per file[0ms]
dispatch: done[53] / failed[0]
postpone: done[0]  / failed[0] / locate err[0]

Server1[10.0.11.50]: done[53] / abandoned[0] / retry[0] / hit[53] / miss[0] / overload[0]
Server2[0.0.0.0]:    done[0]  / abandoned[0] / retry[0] / hit[0]  / miss[0] / overload[0]
Server3[0.0.0.0]:    done[0]  / abandoned[0] / retry[0] / hit[0]  / miss[0] / overload[0]

------upload rpt file stats------
rptfiles: queue[0] / done[0] / abandoned[0] / retry[0]
cloud storage: upload[0], retry[0]
```

**Reading this:**

- `queue` — items currently waiting in each pipeline buffer. `log` is the main log queue; `bkup`/`m1`/`m2`/`m3` are backup buffers.
- `prepare` stage — packaging files. `locate err` = the file disappeared between scheduling and upload (rolled out, deleted).
- `dispatch` stage — actively transferring. `failed` here usually = network or auth issue with the destination.
- `Server1`/`2`/`3` — per-destination counters. `retry` rising = transient network issues, `abandoned` = exceeded retry count and gave up, `overload` = destination server cannot keep up.
- `rpt file stats` — separate counters for report uploads.
- `cloud storage` — S3 / Azure / Google bucket uploads. `retry` non-zero usually = expired credentials or quota.

### 11.3 Stuck queue example

If 10,000 log files have to be converted to CSV and then uploaded, the dispatcher can be busy for a long time. The fastest cure is to **clear the queue**:

```
FortiAnalyzer # diagnose upload clear
```

This abandons everything currently waiting. The next scheduled upload will pick up fresh logs only — older queued items will not be sent.

### 11.4 Cloud storage license [Admin p.163]

Sending logs to AWS / Azure / Google requires a **Storage Connector Service** license — a separate license from the FortiAnalyzer/FortiGuard one. The license has both a data limit and an expiry date (usually one year).

#### diagnose fmupdate dbcontract fds — license validity

```
FAZ # diagnose fmupdate dbcontract fds
FAZ-VMTM23008175 [SERIAL_NO]
   AccountID: ******@fortinet.com
   Industry:  Technology
   Company:   Fortinet
   Contract:  10
   ENHN-1-10-20240604
   FGSA-1-06-20240604
   FMWR-1-06-20240604
   FOAS-1-06-20240604
   FRVS-1-06-20240604
   ISSS-1-06-20240604
   PBDS-1-06-20240604
   SCPC-1-06-20230912     <-- Storage Connector PowerCloud
   SOAR-1-06-20240604
   SPRT-1-10-20240604
```

**Reading the contract codes:** each contract entry has the format `CODE-N-NN-YYYYMMDD` where the date is the expiry. Cross-reference codes against the Fortinet contract list — `SCPC` is the Storage Connector license. The line above shows it expiring 2023-09-12.

#### diagnose test application uploadd 63 — usage details

```
FAZ # diagnose test application uploadd 63
Cloud Storage Usage:
  Status:
      Expire in: 61 days 12 hours 30 minutes
  Usage:
      Total Gigabytes Uploaded:   37 GB
      Number of Files Uploaded:   10226 Files
      Quota:                      10 TB
      Number of Upload Requests Dropped: 0 Requests
```

**Field-by-field:**

- `Expire in` — days/hours/minutes until the cloud-storage license expires. Renew before this hits zero or uploads will stop.
- `Total Gigabytes Uploaded` — cumulative against your data quota.
- `Number of Files Uploaded` — informational.
- `Quota` — total contracted volume. Watch the ratio of Uploaded to Quota.
- `Number of Upload Requests Dropped` — anything non-zero means the cloud destination rejected attempts. Investigate credentials, bucket policy, network egress.

---

## Section 12 — Database migration (PostgreSQL → ClickHouse)

Starting in FortiAnalyzer 7.6.0, the log database engine changed from **PostgreSQL** to **ClickHouse**. When you upgrade from earlier versions, historical logs migrate automatically while real-time logs are inserted into ClickHouse [Admin p.12].

### 12.1 Monitor migration progress

On the GUI, watch the banner. On the CLI:

```
FortiAnalyzer # diagnose sql status migrate-db
```

During migration, FortiView and Reports may show partial data. Wait for migration to complete before evaluating performance regressions.

### 12.2 After migration

- All FortiView data and reports come from the new ClickHouse table.
- PostgreSQL is still present for **event/incident/configuration** data — it is no longer the log database, but it is still running. You will still see `postgres` processes in `execute top`.
- Custom datasets (the SQL queries that drive reports) need to be ClickHouse-compatible. Older PSQL-only syntax may need adjustment.

---

## Section 13 — Playbook troubleshooting

Playbooks are automated workflows on FortiAnalyzer. When they fail, the courseware's prescribed approach is to review the per-execution logs.

### 13.1 Playbook execution states

A playbook job has one of three statuses [Analyst p.219]:

- **Running** — currently executing.
- **Success** — all tasks completed successfully.
- **Failed** — at least one task failed. A playbook with three tasks is considered failed if **any one** of the three fails — not only when all three fail.

### 13.2 Reviewing playbook logs

Playbook logs are the canonical source when troubleshooting:

- Go to **Playbook Monitor** in the GUI.
- Select the desired entry.
- Click the **Details** icon, then **View Log** — this opens the per-task execution log including input parameters, output values, and any error returned by a connector.

### 13.3 Playbooks Dashboard — spotting misconfiguration

On the Playbooks Dashboard you can see **Total Playbooks Executed**, **Total Playbook Actions Executed**, and **Playbooks Executed** charts [Analyst p.219].

If a small number of playbooks dominate the chart by execution count, it may be normal — or it may be a **misconfiguration** causing the playbook to fire too often (over-broad trigger filter, runaway loop). Review the trigger and conditions for the over-firing playbook.

### 13.4 Playbooks-as-tasks pre-flight checklist

- To run a **report** as a playbook task, the report must already exist **and** have both auto-cache and extended log filtering enabled [Analyst p.208].
- Playbooks live **per ADOM** — not per device. A playbook in ADOM A cannot directly trigger on events in ADOM B.
- Connectors used in playbook tasks (FortiOS, EMS, FortiCare, etc.) must be configured **and** healthy. A failing connector is the most common cause of the Failed status.

---

## Section 14 — Quick reference card

Sorted by symptom — when the user reports the symptom on the left, run the commands on the right, in order.

| Symptom | Run these commands |
|---|---|
| FortiAnalyzer feels slow / laggy | `get system status`<br>`get system performance`<br>`execute top`<br>`diagnose hardware info` |
| A specific FortiGate isn't sending logs | `execute ping <FGT-IP>`<br>`diagnose test application oftpd 3`<br>`diagnose dvm device list`<br>`diagnose debug application oftpd 8`<br>(on FortiGate) `diagnose log test` |
| Logs arrive but very slowly / lag growing | `diagnose fortilogd lograte`<br>`diagnose fortilogd lograte-adom`<br>`diagnose fortilogd msgrate`<br>`diagnose sql status sqlplugind`<br>(GUI) Insert Rate vs Receive Rate widget |
| A FortiGate is going up/down repeatedly | `diagnose debug application oftpd 8`<br>`diagnose sniffer packet portN 'host <FGT-IP>' 3 0 l`<br>(on FortiGate) `diagnose test application miglogd 6`<br>(on FortiGate) `diagnose test application fgtlogd 4` |
| "Disk full" or logs being overwritten | `diagnose log device`<br>`diagnose system print df`<br>`get system performance` |
| Reports are slow | `diagnose report status`<br>`diagnose sql hcache status`<br>`diagnose sql show hcache-size`<br>`diagnose test application sqlrptcached 2`<br>`execute sql-report run <ADOM> <name>` |
| Reports are empty / wrong data | `diagnose log device` (check retention)<br>Verify dataset in GUI (Reports > Dataset > Go)<br>Review Advanced Settings (obfuscation, filters) |
| Email alerts not arriving | `diagnose test application fazmaild 2`<br>`diagnose test connection mailserver "<host>:<ip>"`<br>`diagnose debug application fazmaild 8` |
| Cloud / FTP / SFTP uploads failing | `diagnose upload status`<br>`diagnose debug application uploadd 8`<br>`diagnose fmupdate dbcontract fds`<br>`diagnose test application uploadd 63` |
| HA cluster looks sick | `diagnose ha status`<br>`diagnose ha stats`<br>`diagnose ha load-balance`<br>`diagnose ha force-cfg-resync` (last resort) |
| Pre / post firmware upgrade | `diagnose log device` (save before AND after)<br>`diagnose dvm device list` (no Status=Down)<br>back up config + databases<br>wait for running reports |
| TAC ticket / collect everything | `execute tac report`<br>`diagnose debug timestamp enable`<br>`diagnose sniffer packet portN 'host <ip>' 3 0 l` |

---

## End of Playbook

*Source material: FortiAnalyzer 7.6 Administrator Study Guide and FortiAnalyzer 7.6 Analyst Study Guide. Always cross-reference with the current FortiAnalyzer CLI Reference at docs.fortinet.com before running any destructive or production-impacting command.*