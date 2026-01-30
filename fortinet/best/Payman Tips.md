Activate private data encryption
```
config system global
	set private-data-encryption enable
end​
```
Enter a 32-bit hex key after this. FMG will ask for this key if you are not on 7.6 with automatic private data encryption and once FMG implements this fully

Run an automatic disc check after a reboot
```
config system global
	set autorun-log-fsck enable
end​
``` 
Log CLI commands
```
config system global 
    set cli-audit-log enable 
end
```
Run SSL-VPN on a loopback if you aren't on 7.4 (not necessary there because ISDB objects can be used in local-in policies in 7.4+)
```
config system interface
     edit "vpn_loopback"
        set vdom "root"
        set ip <IP> 255.255.255.255
        set type loopback
        set role lan
    next
end
config vpn ssl settings
    set source-interface "vpn_loopback"
end
config firewall vip
    edit "VIP_SSL-VPN_TCP_443"
        set extip <PUBLIC_IP>
        set mappedip "<LOOPBACK_IP>"
        set extintf "<WAN_INTERFACE>"
        set portforward enable
        set extport 443
        set mappedport 443
    next
    edit "VIP_SSL-VPN_UDP_443"
        set extip <PUBLIC_IP>
        set mappedip "<LOOPBACK_IP>"
        set extintf "<WAN_INTERFACE>"
        set portforward enable
        set protocol udp
        set extport 443
        set mappedport 443
    next
end
config firewall address
	edit "H_VPN_LOOPBACK"
		set subnet <IP>/32
	next
end
config firewall policy
    edit 0
        set name "OUTSIDE 2 SSL-VPN LOOPBACK"
        set srcintf "OUTSIDE_SD-WAN"
        set dstintf "vpn_loopback"
        set action accept
        set srcaddr "all"
        set dstaddr "H_VPN_LOOPBACK"
        set schedule "always"
        set service "HTTPS" "SVC_UDP_443"
        set utm-status enable
        set ssl-ssh-profile "certificate-inspection"
        set ips-sensor "protect_server"
        set logtraffic all
    next
end
```
 

 Configure an SD-WAN zone regardless of the amount of WAN connections you have
Use zones for all your interfaces, even if a zone only has one interface 
Add interface bandwidth widgets to a dashboard so historic statistics are recorded
Disable unused interfaces
Enable device detection on internal interfaces
## Various HA settings
```
config system ha
	set session-pickup enable
	set session-pickup-connectionless enable
	set session-pickup-expectation enable
	set session-pickup-nat enable
	set memory-based-failover enable
	set memory-failover-threshold 86
	set ssd-failover enable
end
```
## Log implicit denies and local traffic
```
config log setting
	set fwpolicy-implicit-log enable
	set local-in-allow enable
    set local-in-deny-unicast enable
    set local-in-deny-broadcast enable
    set local-out enable
	set log-invalid-packet enable
	set resolve-ip enable
end
config log memory filter
    set local-traffic enable
end
```
# Configure log retention on FortiGates with disks
```
config log disk setting
	set status enable
	set maximum-log-age 0
end
```
# Local-In Policies for management
Create a group called, for example, "GRP_ALLOWED_SOURCES_MGMT_LOCAL_IN" with all your relevant hosts and subnets
```
 config firewall local-in-policy
    edit 0
        set intf "any"
        set srcaddr "GRP_ALLOWED_SOURCES_MGMT_LOCAL_IN"
        set action accept
        set dstaddr "all"
		set virtual-patch enable
        set service "HTTPS" "SSH"
        set schedule "always"
    next
    edit 0
        set intf "any"
        set srcaddr "all"
        set dstaddr "all"
		set virtual-patch enable
        set service "HTTPS" "SSH"
        set schedule "always"
    next
end
```
---
# Changes services as necessary

Use trusted hosts in addition to local-in policies
Use Network Protocol Enforcement in Application Control profiles
Apply where necessary. Be careful with HTTP/HTTPS, because legitimate programs use it.
```
config application list
    edit "<PROFILE>"
        set control-default-network-services enable
        config default-network-services
            edit 1
                set port 25
                set services smtp
            next
            edit 2
                set port 22
                set services ssh
            next
            edit 3
                set port 53
                set services dns
            next
            edit 4
                set port 21
                set services ftp
            next
			edit 5
				set port 161
				set services snmp
			next
			edit 6
				set port 143
				set services imap
			next
			edit 7
				set port 110
				set services pop3
			next
        end
    next
end
```
---
# Route RFC1918 addresses in a blackhole
```
config firewall address
edit "N_RFC-1918-10_8"
set allow-routing enable
set subnet 10.0.0.0 255.0.0.0
next
edit "N_RFC-1918-172_12"
set allow-routing enable
set subnet 172.16.0.0 255.240.0.0
next
edit "N_RFC-1918-192_16"
set allow-routing enable
set subnet 192.168.0.0 255.255.0.0
next
end
config firewall addrgrp
edit "GRP_RFC-1918"
set member "N_RFC-1918-10_8" "N_RFC-1918-172_12" "N_RFC-1918-192_16"
set allow-routing enable
next
end
config router static
edit 0
set dstaddr "GRP_RFC-1918"
set distance 254
set blackhole enable
next
end

```
 ---
# Small things for hardening
```
config system global
set admin-https-ssl-versions tlsv1-2 tlsv1-3
set admin-https-redirect enable
set ssl-static-key-ciphers disable
set strong-crypto enable
set dh-params 8192
set admin-lockout-threshold 3
set admin-lockout-duration 300
end
config system auto-install
set auto-install-config disable
set auto-install-image disable
end
```
---
# Use Threat Feeds
```
config system external-resource
    edit "EXT_TF_EMERGINGTHREAT_BLOCK_IPs"
        set type address
        set resource "https://rules.emergingthreats.net/fwrules/emerging-Block-IPs.txt"
    next
    edit "EXT_TF_EMERGINGTHREAT_COMPRO_IPs"
        set type address
        set resource "https://rules.emergingthreats.net/blockrules/compromised-ips.txt"
    next
    edit "EXT_TF_FEODO_IP_BLOCKLIST"
        set type address
        set resource "https://feodotracker.abuse.ch/downloads/ipblocklist_recommended.txt"
    next
    edit "EXT_TF_FIREHOL_LEVEL1"
        set type address
        set resource "https://raw.githubusercontent.com/ktsaou/blocklist-ipsets/master/firehol_level1.netset"
    next
end
config firewall policy
edit 0
set name "BLOCK TRAFFIC 2 BAD IPs"
set srcintf "any"
set dstintf "<WAN_INTERFACE>"
set srcaddr "all"
set dstaddr "EXT_TF_EMERGINGTHREAT_BLOCK_IPs" "EXT_TF_EMERGINGTHREAT_COMPRO_IPs" "EXT_TF_FEODO_IP_BLOCKLIST" "EXT_TF_FIREHOL_LEVEL1"
set schedule "always"
set service "ALL"
set logtraffic all
next
edit 0
set name "BLOCK TRAFFIC FROM BAD IPs"
set srcintf "z_sd-wan_WAN"
set dstintf "any"
set srcaddr "EXT_TF_EMERGINGTHREAT_BLOCK_IPs" "EXT_TF_EMERGINGTHREAT_COMPRO_IPs" "EXT_TF_FEODO_IP_BLOCKLIST" "EXT_TF_FIREHOL_LEVEL1"
set dstaddr "all"
set schedule "always"
set service "ALL"
set logtraffic all
next
end
```
---
# Block ISDB objects
```
config firewall internet-service-group
edit "ISDB-BLOCK-SOURCE"
set direction source
set member "Botnet-C&C.Server" "Hosting-Bulletproof.Hosting" "Malicious-Malicious.Server" "Phishing-Phishing.Server" "Proxy-Proxy.Server" "Spam-Spamming.Server" "Tor-Exit.Node" "Tor-Relay.Node" "VPN-Anonymous.VPN"
next
edit "ISDB-BLOCK-DESTINATION"
set direction destination
set member "Blockchain-Crypto.Mining.Pool" "Botnet-C&C.Server" "DNS-DoH_DoT" "Hosting-Bulletproof.Hosting" "Malicious-Malicious.Server" "Phishing-Phishing.Server" "Proxy-Proxy.Server" "Spam-Spamming.Server" "Tor-Exit.Node" "Tor-Relay.Node" "VPN-Anonymous.VPN"
next
end
config firewall policy
edit 0
set name "BLOCK TRAFFIC 2 BAD ISDB IPs"
set srcintf "any"
set dstintf "z_sd-wan_WAN"
set srcaddr "all"
set internet-service enable
set internet-service-group "ISDB-BLOCK-DESTINATION"
set schedule "always"
set logtraffic all
next
edit 0
set name "BLOCK ISDB TRAFFIC 2 INTERNAL"
set srcintf "z_sd-wan_WAN"
set dstintf "any"
set dstaddr "all"
set internet-service-src enable
set internet-service-src-group "ISDB-BLOCK-SOURCE"
set schedule "always"
set service "ALL"
set logtraffic all
next
end
`````

disable ssl vpn