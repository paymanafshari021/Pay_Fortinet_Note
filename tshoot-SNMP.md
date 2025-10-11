### SNMP Diagnostic Cheat Sheet

#### Display SNMP System Information
```bash
show system snmp sysinfo
```
#### Display SNMP Community Configuration
```bash
show system snmp community
```
#### Display SNMP User Configuration
```bash
show system snmp user
```
#### Enable SNMP Debugging
```bash
diagnose debug reset
diagnose debug enable
diagnose debug application snmp -1
```

  ```bash
diagnose debug disable
  ```
#### Check SNMP Daemon Status
```bash
diagnose sys process pidof snmpd
```
#### Check SNMP Port (UDP 161)
```bash
diagnose sys tcpsock | grep 161
```
#### Debug SNMP Traffic Flow
```bash
diagnose debug flow filter clear
diagnose debug flow filter addr 172.18.202.31
diagnose debug flow show function-name enable
diagnose debug flow trace start 100
diagnose debug enable
```
#### Capture SNMP Packets
```bash
diagnose sniffer packet any "port 161" 4 0
```
#### Check SNMP Sessions
```bash
diagnose sys session list | grep 161
```