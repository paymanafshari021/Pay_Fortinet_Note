# Cheat Sheet for VPN Diagnostics

## Quick IKE Debug Setup

```bash
diagnose vpn ike log filter rem-addr4 <remote gateway IPv4>
diagnose debug application ike -1
diagnose debug enable
```
## IKE Log Filtering and Debugging

```bash
# Clear IKE log filter
diagnose vpn ike log filter clear
# Set IKE log filter by name
diagnose vpn ike log-filter name <name from ipsec>
# Filter by remote gateway IPv4
diagnose vpn ike log filter rem-addr4 <remote gateway IPv4>
# Enable timestamp in debug console
diagnose debug console timestamp enable
# Enable IKE debugging with all details
diagnose debug application ike -1
diagnose debug enable
```

## VPN Tunnel Diagnostics

```bash
# List available VPN tunnel diagnostic commands
diagnose vpn tunnel ?
# List details for a specific tunnel
diagnose vpn tunnel list name <name>
# Get detailed IPsec tunnel information
get vpn ipsec tunnel details
# List all IPsec tunnels
get ipsec tunnel list
# Get IPsec tunnel statistics
get vpn ipsec stats tunnel
# Check IPsec VPN status
diagnose vpn ipsec status
# List IKE gateways
diagnose vpn ike gateway list
# List specific IKE gateway
diagnose vpn ike gateway list name <name>
# Clear IKE gateways
diagnose vpn ike gateway clear
# Show IKE log filter settings
diagnose vpn ike log filter
```

## IKE Debug Bitmask

```bash
# Set IKE log filter for remote gateway
diagnose vpn ike log filter rem-addr4 <remote gateway IPv4>
# Enable IKE debugging with specific bitmask
diagnose debug application ike <bitmask>
```

### Bitmask Values

|Description|Value|
|---|---|
|Major errors|1|
|Configuration changes|2|
|Connection attempts|4|
|Phase 1 and 2 negotiation messages|8|
|NAT-T messages|16|
|Dead peer detection messages|32|
|Encryption and decryption keys|64|
|Encrypted traffic payload|128|

## Packet Sniffing

### No NAT IKE Traffic

```bash
diagnose sniffer packet <port> 'host <remote-gw> and udp port 500'
```

### ESP Traffic

```bash
diagnose sniffer packet any 'host <remote-gw> and esp'
```

### With NAT and NAT-T IKE/ESP Traffic

```bash
diagnose sniffer packet any 'host <remote-gw> and (udp port 500 or udp port 4500)'
```

## Configure IKE and NAT-T Ports

```bash
config system settings
    set ike-port <1024 – 65535>
end
```

## Additional VPN Commands

```bash
# List IKE gateways
diagnose vpn ike gateway list
# Clear IKE gateways
diagnose vpn ike gateway clear
# List VPN tunnels
diagnose vpn tunnel list
```