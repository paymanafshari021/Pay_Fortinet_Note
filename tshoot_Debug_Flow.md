#Debug #Fortinet 

## ping
  
```bash
execute ping-options source 10.226.249.254
exe ping 10.124.12.10
```
_________________________________
## policy lookup
  ```bash
diagnose firewall iprope lookup <src_ip> <src_port> <dst_ip> <dst_port> <protocol> <src_interface>
```
_________________________________
## flow filter
```bash
diagnose debug flow filter negate <parameter>
diagnose debug flow filter addr <ip1> [<ip2> <and/or>]
diagnose debug flow filter saddr <ip>
diagnose debug flow filter daddr <ip>
diagnose debug flow filter port <port_number>
diagnose debug flow filter sport <port_number>
diagnose debug flow filter dport <port_number>
diagnose debug flow filter proto <number>
diagnose debug flow filter vd <index>
```
_________________________________
## debug flow tcp
  ```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr 89.37.120.250
diag debug flow filter proto 6
diag debug flow filter port 161

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```
_________________________________
## debug flow icmp
  ```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr 194.187.94.196
diag debug flow filter proto 6
diag debug flow filter port 22

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```
_________________________________
## debug DHCP-Relay
  ```bash
diag debug reset
diag debug enable
diag debug console timestamp enable
diag debug application dhcprelay -1

diag debug disable
diag debug reset
```

```bash
diag sniffer packet any 'port 67 or port 68' 4
diag sniffer packet any 'host 194.187.94.103' 4
```

```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr 172.16.163.235
diag debug flow filter proto 17
diag debug flow filter port 67

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```
_________________________________
