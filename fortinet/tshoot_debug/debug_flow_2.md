#Debug #Fortinet

## Flow Filter

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

---

## Debug flow **UDP/PORT**

```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr <IP_ADDRESS>
diag debug flow filter proto 17
diag debug flow filter port <PORT>

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```

---

## Debug flow **TCP/PORT**

```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr <IP_ADDRESS>
diag debug flow filter proto 6
diag debug flow filter port <PORT>

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```

---

## Debug flow **ICMP (ping, traceroute, etc.)**

```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr <IP_ADDRESS>
diag debug flow filter proto 1

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```

---