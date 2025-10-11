```bash
config firewall address
    edit "Internet_IPv4_1-9"
        set type iprange
        set start-ip 1.0.0.0
        set end-ip 9.255.255.255
    next

    edit "Internet_IPv4_11-126"
        set type iprange
        set start-ip 11.0.0.0
        set end-ip 126.255.255.255
    next

    edit "Internet_IPv4_128-169"
        set type iprange
        set start-ip 128.0.0.0
        set end-ip 169.253.255.255
    next

    edit "Internet_IPv4_169-172"
        set type iprange
        set start-ip 169.255.0.0
        set end-ip 172.15.255.255
    next

    edit "Internet_IPv4_172-191"
        set type iprange
        set start-ip 172.32.0.0
        set end-ip 191.0.1.255
    next

    edit "Internet_IPv4_191-191.1"
        set type iprange
        set start-ip 191.1.0.0
        set end-ip 191.255.255.255
    next

    edit "Internet_IPv4_192-192.0"
        set type iprange
        set start-ip 192.0.0.0
        set end-ip 192.0.0.255
    next

    edit "Internet_IPv4_192-192.88"
        set type iprange
        set start-ip 192.0.3.0
        set end-ip 192.88.98.255
    next

    edit "Internet_IPv4_192.88-192.167"
        set type iprange
        set start-ip 192.88.100.0
        set end-ip 192.167.255.255
    next

    edit "Internet_IPv4_192.169-198.17"
        set type iprange
        set start-ip 192.169.0.0
        set end-ip 198.17.255.255
    next

    edit "Internet_IPv4_198.20-198.51"
        set type iprange
        set start-ip 198.20.0.0
        set end-ip 198.51.99.255
    next

    edit "Internet_IPv4_198.51-203.0"
        set type iprange
        set start-ip 198.51.101.0
        set end-ip 203.0.112.255
    next

    edit "Internet_IPv4_203.0-223"
        set type iprange
        set start-ip 203.0.114.1
        set end-ip 223.255.255.255
    next
end

config firewall addrgrp
    edit "Internet_IPv4"
        set member "Internet_IPv4_1-9" "Internet_IPv4_11-126" "Internet_IPv4_128-169" "Internet_IPv4_169-172" "Internet_IPv4_172-191" "Internet_IPv4_191-191.1" "Internet_IPv4_192-192.0" "Internet_IPv4_192-192.88" "Internet_IPv4_192.88-192.167" "Internet_IPv4_192.169-198.17" "Internet_IPv4_198.20-198.51" "Internet_IPv4_198.51-203.0" "Internet_IPv4_203.0-223"
    next
end
```