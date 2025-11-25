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

    edit "EXT_TF_BLOCKLISTDE_ALL"
        set type address
        set resource "https://lists.blocklist.de/lists/all.txt"
    next
    edit "EXT_TF_CINS_BADGUYS"
        set type address
        set resource "https://cinsscore.com/list/ci-badguys.txt"
    next
    edit "EXT_TF_C2_TRACKER"
        set type address
        set resource "https://raw.githubusercontent.com/montysecurity/C2-Tracker/main/data/all.txt"
    next
    edit "EXT_TF_ABUSECH_SSLBL"
        set type address
        set resource "https://sslbl.abuse.ch/blacklist/sslipblacklist.txt"
    next
    edit "EXT_TF_IPSUM"
        set type address
        set resource "https://raw.githubusercontent.com/stamparm/ipsum/master/ipsum.txt"
    next
    edit "EXT_TF_DIGITALSIDE_URLS"
        set type address
        set resource "https://osint.digitalside.it/Threat-Intel/lists/latesturls.txt"
    next
    edit "EXT_TF_BLOCKLISTDE_SSH"
        set type address
        set resource "https://lists.blocklist.de/lists/ssh.txt"
    next
    edit "EXT_TF_BLOCKLISTDE_MAIL"
        set type address
        set resource "https://lists.blocklist.de/lists/mail.txt"
    next
    edit "EXT_TF_BLOCKLISTDE_FTP"
        set type address
        set resource "https://lists.blocklist.de/lists/ftp.txt"
    next
    edit "EXT_TF_BLOCKLISTDE_WEB"
        set type address
        set resource "https://lists.blocklist.de/lists/apache.txt"
    next
    edit "EXT_TF_FIREHOL_LEVEL2"
        set type address
        set resource "https://raw.githubusercontent.com/ktsaou/blocklist-ipsets/master/firehol_level2.netset"
    next
    edit "EXT_TF_FIREHOL_LEVEL3"
        set type address
        set resource "https://raw.githubusercontent.com/ktsaou/blocklist-ipsets/master/firehol_level3.netset"
    next
    edit "EXT_TF_MALICIOUS_IP_GITHUB"
        set type address
        set resource "https://raw.githubusercontent.com/romainmarcoux/malicious-ip/main/malicious.ip"
    next
    edit "EXT_TF_MALICIOUS_OUTGOING_IP_GITHUB"
        set type address
        set resource "https://raw.githubusercontent.com/romainmarcoux/malicious-outgoing-ip/main/malicious_outgoing.ip"
    next
    edit "EXT_TF_THREATHIVE"
        set type address
        set resource "https://threathive.net/hiveblocklist.txt"
    next
    edit "EXT_TF_ISMALICIOUS_FREE"
        set type address
        set resource "https://ismalicious.com/blocklist.txt"
    next
end

```

```
config firewall policy
    edit 0
        set name "BLOCK TRAFFIC TO BAD_IPs"
        set srcintf "any"
        set dstintf "<WAN_INTERFACE>"
        set srcaddr "all"
        set dstaddr \
            "EXT_TF_EMERGINGTHREAT_BLOCK_IPs" \
            "EXT_TF_EMERGINGTHREAT_COMPRO_IPs" \
            "EXT_TF_FEODO_IP_BLOCKLIST" \
            "EXT_TF_FIREHOL_LEVEL1" \
            "EXT_TF_BLOCKLISTDE_ALL" \
            "EXT_TF_CINS_BADGUYS" \
            "EXT_TF_C2_TRACKER" \
            "EXT_TF_ABUSECH_SSLBL" \
            "EXT_TF_IPSUM" \
            "EXT_TF_DIGITALSIDE_URLS" \
            "EXT_TF_BLOCKLISTDE_SSH" \
            "EXT_TF_BLOCKLISTDE_MAIL" \
            "EXT_TF_BLOCKLISTDE_FTP" \
            "EXT_TF_BLOCKLISTDE_WEB" \
            "EXT_TF_FIREHOL_LEVEL2" \
            "EXT_TF_FIREHOL_LEVEL3" \
            "EXT_TF_MALICIOUS_IP_GITHUB" \
            "EXT_TF_MALICIOUS_OUTGOING_IP_GITHUB" \
            "EXT_TF_THREATHIVE" \
            "EXT_TF_ISMALICIOUS_FREE"
        set schedule "always"
        set service "ALL"
        set action deny
        set logtraffic all
    next

    edit 0
        set name "BLOCK TRAFFIC FROM BAD_IPs"
        set srcintf "z_sd-wan_WAN"
        set dstintf "any"
        set srcaddr \
            "EXT_TF_EMERGINGTHREAT_BLOCK_IPs" \
            "EXT_TF_EMERGINGTHREAT_COMPRO_IPs" \
            "EXT_TF_FEODO_IP_BLOCKLIST" \
            "EXT_TF_FIREHOL_LEVEL1" \
            "EXT_TF_BLOCKLISTDE_ALL" \
            "EXT_TF_CINS_BADGUYS" \
            "EXT_TF_C2_TRACKER" \
            "EXT_TF_ABUSECH_SSLBL" \
            "EXT_TF_IPSUM" \
            "EXT_TF_DIGITALSIDE_URLS" \
            "EXT_TF_BLOCKLISTDE_SSH" \
            "EXT_TF_BLOCKLISTDE_MAIL" \
            "EXT_TF_BLOCKLISTDE_FTP" \
            "EXT_TF_BLOCKLISTDE_WEB" \
            "EXT_TF_FIREHOL_LEVEL2" \
            "EXT_TF_FIREHOL_LEVEL3" \
            "EXT_TF_MALICIOUS_IP_GITHUB" \
            "EXT_TF_MALICIOUS_OUTGOING_IP_GITHUB" \
            "EXT_TF_THREATHIVE" \
            "EXT_TF_ISMALICIOUS_FREE"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
        set action deny
        set logtraffic all
    next
end
```