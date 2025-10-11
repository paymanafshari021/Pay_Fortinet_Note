```bash
diagnose debug flow filter addr <target-addr>
diagnose debug flow trace start 100
diagnose debug console timestamp enable
diagnose debug enable

diagnose sniffer packet any "host <target-addr>" 4 0 l

diagnose sys session filter dst <target-addr>
diagnose sys session list

get router info routing-table all

show system sdwan
show firewall policy
show system interface
diagnose sys sdwan zone
diagnose sys sdwan member
diagnose sys sdwan service
diagnose sys sdwan health-check status Level3_DNS

get router info bgp community 65000:10
show router bgp
show router route-map
show router community-list

show system sdwan
show firewall policy
show system interface
diagnose sys sdwan zone
diagnose sys sdwan member
diagnose sys sdwan service
diagnose firewall proute list
diagnose sys sdwan health-check status Level3_DNS
diagnose sys sdwan health-check status VPN_PING
```

