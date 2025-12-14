```bash
get router info routing-table all
diagnose sniffer packet any "icmp and host 100.64.3.1" 4

get router info routing-table all
get router info routing-table database

diagnose sys session filter proto 1 
diagnose sys session clear

diagnose debug flow filter clear 
diagnose debug flow filter proto 1
diagnose debug flow filter addr 100.64.3.1 
diagnose debug enable
diagnose debug flow trace start 10

get router info bgp summary 
get router info bgp neighbors 
get router info bgp network

diagnose ip router bgp all enable 
diagnose ip router bgp level info 
diagnose debug enable

execute router clear bgp all

diagnose debug disable
diagnose ip router bgp all disable

get router info routing-table all
get router info routing-table database

get router info ospf neighbor

get router info routing-table all

get router info ospf neighbor
get router info routing-table all

get router info ospf status 
get router info ospf neighbor

diagnose ip router ospf all enable 
diagnose ip router ospf level info 
diagnose debug enable

diagnose debug disable
diagnose ip router ospf all disable
```