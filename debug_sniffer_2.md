#Debug  #diagnose #sniffer #Fortinet 
## Debug 

```
diag sniffer packet any 'host 10.0.0.1 and host 10.0.0.2 and tcp port 80' 4 0 a
diagnose sniffer packet any "port 541 and host <FortiManager_IP>" 6 0 a
diagnose sniffer packet any 'host 10.200.1.10 and host 10.200.2.10 and icmp' 4 0 a 
diagnose sniffer packet any 'port 80 and host 172.20.120.18' 4 0 a 
diagnose sniffer packet any 'src host 192.168.10.1 and port (80 or 443 or 8080)' 4 0 a
```

```
diag sniffer packet <interface|any> '\[\[src|dst\] host <IP> | <network>\] \[and|or\] \[\[src|dst\] host <IP> | <network>\] \[and|or\] \[\[arp|ip|gre|esp|udp|tcp\] port <port>\] \[and|or\] \[\[arp|ip|gre|esp|udp|tcp\] port <port>\]' <verbose> <count> <tsformat> <framesize>
```

---

# Field-by-field explanation

`<interface|any>`

- Name of the interface to monitor (e.g. `port1`, `wan1`).
- Use `any` to capture on all interfaces. ([docs.fortinet.com](https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/680228/performing-a-sniffer-trace-or-packet-capture?utm_source=chatgpt.com "Performing a sniffer trace or packet capture"))

`'…filter…'` **(the quoted filter)**

- FortiGate uses a Berkeley Packet Filter (BPF)-style filter. You can match source/destination hosts (`src host`, `dst host`, or just `host`), networks (CIDR), combine expressions with `and`/`or`, and match protocols/ports (`tcp port 80`, `udp port 53`, `arp`, `ip`, `gre`, `esp`, etc.).
- Examples of valid fragments: `host 10.0.0.1`, `src host 10.0.0.1`, `host 10.0.0.1 and tcp port 443`, `(host A and host B) and icmp`. ([Gist](https://gist.github.com/cetinajero/1effb04ee9ae9fc6f65faaf43d4bff9b?utm_source=chatgpt.com "Usefull Fortigate CLI commands"))

`<verbose>` **(verbosity / level)**

- Numeric level controls how much is printed and whether data hex is shown. Typical meanings (FortiOS):  
  1 = print packet headers only  
  2 = header + IP payload data  
  3 = header + Ethernet data  
  4 = header + interface name  
  5 = IP header+data + interface name  
  6 = Ethernet header+data + interface name.
- Pick a higher number (5/6) to see hex payload; 1–4 are more compact. ([docs.fortinet.com](https://docs.fortinet.com/document/fortigate/7.6.4/cli-reference/303299696/diagnose-sniffer?utm_source=chatgpt.com "diagnose sniffer | FortiGate / FortiOS 7.6.4"))

`<count>`

- Number of packets to capture before the command exits.
- If `0` (or omitted in some versions) it will run until you stop it with `Ctrl+C`. ([community.fortinet.com](https://community.fortinet.com/t5/FortiGate/Technical-Tip-Capture-Bidirectional-traffic-using-NP7-sniffer/ta-p/326800?utm_source=chatgpt.com "Technical Tip: Capture Bidirectional traffic using NP7 sniffer"))

`<tsformat>` **(timestamp format)**

- Common values: 
  - `a` — absolute UTC timestamps (human date/time).
  - `l` — absolute LOCAL time (local unit time).
  - otherwise — relative time since the start of the sniff (delta/seconds.ms).
- Use `a` or `l` if you want wall-clock timestamps to match logs; otherwise you’ll see relative times. ([community.fortinet.com](https://community.fortinet.com/t5/FortiGate/Troubleshooting-Tip-Using-the-FortiOS-built-in-packet-sniffer/ta-p/194222?utm_source=chatgpt.com "Using the FortiOS built-in packet sniffer..."))

`<framesize>` **(also written** `framesize` **/** `frame size`**)**

- Number of bytes of each packet to print before truncation. Default is the interface MTU (i.e. full frames up to MTU) if you don’t set it. Use a smaller number to reduce output when you only need headers.

---