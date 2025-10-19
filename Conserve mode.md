FortiGate's conserve mode is a self-protection mechanism that activates when system memory usage exceeds a configurable threshold (default 88%) to prevent a system crash. This mode impacts performance by reducing functionality, and at a more critical threshold (default 95%), it will drop new sessions to free up memory. The device exits conserve mode once memory usage drops below another threshold (default 82%)
### How it works
- **Entry:** When memory usage crosses the "red" threshold (e.g., 88%), the FortiGate enters conserve mode. It begins to reduce memory-intensive processes, like some aspects of antivirus scanning, to conserve resources.
- **Session dropping:** If memory usage continues to rise and hits the "extreme" threshold (e.g., 95%), the FortiGate will start dropping new sessions to reclaim memory.
- **Exit:** The device will automatically exit conserve mode when memory usage falls below the "green" threshold (e.g., 82%)
### How to check and troubleshoot
- **Check status:** Use the command `diagnose hardware sysinfo conserve` in the CLI to see if conserve mode is on and to view the current memory usage and thresholds.
- **Identify the process:** To find the process consuming the most memory, use `diagnose sys top` and press `m` to sort by memory usage. Common culprits include the IPS engine or WAD (Web Application and URL Filtering).

https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/194558/conserve-mode
# Conserve mode

Each FortiGate model has a specific amount of memory that is shared by all operations. If most or all of that memory is in use, system operations can be affected in unexpected ways. To control how FortiOS functions when the available memory is very low, FortiOS enters conserve mode. This causes functions, such as antivirus scanning, to change how they operate to reduce the functionality and conserve memory without compromising security.

Three memory thresholds can be configured:

```bash
config system global
    set memory-use-threshold-extreme <integer>
    set memory-use-threshold-green <integer>
    set memory-use-threshold-red <integer>
end
```

| `memory-use-threshold-green <integer>`   | The threshold at which memory usage forces the FortiGate to leave conserve mode, in percent of total RAM (70 - 97, default = 82).        |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `memory-use-threshold-red <integer>`     | The threshold at which memory usage forces the FortiGate to enter conserve mode, in percent of total RAM (70 - 97, default = 88).        |
| `memory-use-threshold-extreme <integer>` | The threshold at which memory usage is considered extreme and new sessions are dropped, in percent of total RAM (70 - 97, default = 95). |
## Flow inspection in conserve mode

The FortiGate's flow-based inspection behavior while in conserve mode is configured with the IPS failopen command.

config ips global
    set fail-open {enable | disable}
end

- When disabled (default), the IPS engine drops all new sessions that require flow-based inspection.
    
- When enabled, the IPS engine does not perform any scans and allows new packets.
    

## Diagnostics

When in conserve mode, FortiOS generates conserve mode log messages and SNMP traps, and a conserve mode banner is shown in the GUI.

![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/2def7712-5e99-11f0-a9d0-d2b0d2e22f7d/images/a76a227aa5d80a86daa1c78125a4b9c2_conserve_mode_banner.png)

###### To view current information about memory conservation status:

# diagnose hardware sysinfo conserve
memory conserve mode:                        on
total RAM:                                          997 MB
memory used:                                        735 MB   73% of total RAM
memory freeable:                                    173 MB   17% of total RAM
memory used + freeable threshold extreme:           947 MB   95% of total RAM
memory used threshold red:                          877 MB   88% of total RAM
memory used threshold green:                        817 MB   82% of total RAM

###### To view logs:

1. Go to _Log & Report > System Events_ in the GUI.
    
2. If historical FortiView is enabled, select the _Logs_ tab.
    
3. If the GUI is unresponsive due to high memory usage, making the logs inaccessible, they can be viewed in the CLI:
    
    # execute log filter category 1
    # execute log display
    
    1: date=2022-11-02 time=16:58:37 eventtime=1667433517502192693 tz="-0700" logid="0100022011" type="event" subtype="system" level="critical" vd="root" logdesc="Memory conserve mode entered" service="kernel" conserve="on" total=997 MB used=707 MB red="877 MB" green="698 MB" msg="Kernel enters memory conserve mode
    

###### To view the crash log in the CLI:

# diagnose debug crashlog read

1: 2022-10-27 14:22:36 service=kernel conserve=on total="997 MB" used="720 MB" red="877 MB" 
2: 2022-10-27 14:22:36 green="817 MB" msg="Kernel enters memory conserve mode"