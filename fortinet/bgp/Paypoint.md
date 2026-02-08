
+ If that route does **not exist in the routing table**, NOTHING is advertised
+ Change Router ID = 3 sec - 30 sec
+ every Spoke next hop must be change in Route-maps
+ **execute router clear bgp all** - 30 Sec
-----
+ change router id in HUB und in SPOKE
+ verified -> get router info bgp neighbors -> in HUB weniger als 25-30 Sec - in SPOKE 3-5 secunde

---


config system interface
    edit "lb_bgp"
        set vdom "root"
        set ip 10.255.254.110 255.255.255.255
        set type loopback
        set role lan
        set snmp-index 10
        set ip-managed-by-fortiipam disable
    next
end

---
modify bgp 
firewall policy
