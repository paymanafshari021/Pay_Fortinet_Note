## Debug DHCP-Relay Application Logs

```bash
diag debug reset
diag debug enable
diag debug console timestamp enable
diag debug application dhcprelay -1
```

(stop it with)

```bash
diag debug disable
diag debug reset
```

---

## Sniffer for DHCP (ports 67/68)

```bash
diag sniffer packet any 'port 67 or port 68' 4
```

To narrow down to a specific DHCP server/relay:

```bash
diag sniffer packet any 'host 194.187.94.103' 4
```

---

## Debug Flow for DHCP (UDP/67)

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

---