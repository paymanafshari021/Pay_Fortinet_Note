#dos #ddos #Prevention
# DoS Prevention
## ICMP Flooding DoS Prevention
```
config firewall DoS-policy
    edit 0
        set name "no icmp flooding"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL_ICMP"
        config anomaly
            edit "tcp_syn_flood"
                set threshold 2000
            next
            edit "tcp_port_scan"
                set threshold 1000
            next
            edit "tcp_src_session"
                set threshold 5000
            next
            edit "tcp_dst_session"
                set threshold 5000
            next
            edit "udp_flood"
                set threshold 2000
            next
            edit "udp_scan"
                set threshold 2000
            next
            edit "udp_src_session"
                set threshold 5000
            next
            edit "udp_dst_session"
                set threshold 5000
            next
            edit "icmp_flood"
                set status enable
                set log enable
                set action block
                set threshold 250
            next
            edit "icmp_sweep"
                set threshold 100
            next
            edit "icmp_src_session"
                set threshold 300
            next
            edit "icmp_dst_session"
                set threshold 1000
            next
            edit "ip_src_session"
                set threshold 5000
            next
            edit "ip_dst_session"
                set threshold 5000
            next
            edit "sctp_flood"
                set threshold 2000
            next
            edit "sctp_scan"
                set threshold 1000
            next
            edit "sctp_src_session"
                set threshold 5000
            next
            edit "sctp_dst_session"
                set threshold 5000
            next
        end
    next
end
```
### Test
```
sudo hping3 -i u100 -l <Public IP>
```
---
## TCP syn-flood DoS Prevention

### Test
```
sudo hping3 -i u100 -S -p 80 <Public IP>
```