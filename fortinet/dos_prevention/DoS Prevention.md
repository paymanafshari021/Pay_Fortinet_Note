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
            edit "icmp_flood"
                set status enable
                set log enable
                set action block
                set threshold 250
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

Ein **TCP SYN-Flood-Angriff** ist eine **Denial-of-Service (DoS)**-Attacke:

- Der Angreifer schickt sehr viele **SYN-Anfragen** (Verbindungsanfragen) an einen Server.
- Der Server antwortet jeweils mit **SYN-ACK** und wartet auf die Bestätigung (**ACK**).
- Diese Bestätigung kommt **nie**.
- Dadurch bleiben viele **halboffene Verbindungen** bestehen.
- Die Ressourcen des Servers werden blockiert → **legitime Nutzer kommen nicht mehr durch**.
```
config firewall DoS-policy
    edit 0
        set name "no tcp sync-flood"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL_TCP"
        config anomaly
            edit "tcp_syn_flood"
                set status enable
                set log enable
                set action block
                set threshold 10
            next
        end
    next
end
```
### Test
```
sudo hping3 -i u100 -S -p 80 <Public IP>
```
---
## Port Scan Policy
```
config firewall DoS-policy
    edit 0
        set name "disable port scans"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL"
        config anomaly
            edit "tcp_port_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
            edit "udp_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
            edit "sctp_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
        end
    next
end
```
---
## Adding Quarantine
```
config firewall DoS-policy
    edit 4
        set name "disable port scans"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL"
        config anomaly
            edit "tcp_port_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
            next
            edit "udp_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
            next
            edit "sctp_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
        end
    next
end
```
### Remove Quarantine Attacker
