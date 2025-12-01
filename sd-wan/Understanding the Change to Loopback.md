### Understanding the Change
In Fortinet FortiGate devices, particularly in SD-WAN or ADVPN setups, iBGP "per overlay" typically means establishing separate BGP peering sessions for each VPN overlay tunnel (e.g., one per underlay like MPLS or internet). This can lead to more complex routing and limited failover or shortcut capabilities in dynamic environments.

Changing to iBGP "per loopback" consolidates routing into a single iBGP session per hub-spoke pair, using loopback interfaces as the peering endpoints. This simplifies the control plane, enables better ADVPN shortcuts between spokes, supports multipath and failover across overlays, and uses a unified loopback subnet for next-hop resolution. It relies on IKE extensions (like exchange-ip-addr4) to advertise loopback IPs over IPsec tunnels.

This approach is common in dual-hub or multi-hub topologies for scalability.

**Assumptions for this guide:**
- You're using FortiOS 7.0+ (examples based on 7.4/7.6).
- Hub-spoke topology with ADVPN/SD-WAN enabled.
- Existing per-overlay setup uses separate BGP neighbors per tunnel interface.
- Basic IPsec VPN overlays are already configured (e.g., phase1/phase2 interfaces like "advpn" and "advpn_b").
- AS number is 65500 (adjust as needed).
- Loopback subnet: 172.16.100.0/24 (unique IPs per device, e.g., hub: 172.16.100.1, spoke1: 172.16.100.2).
- Backup your config before changes (`execute backup config`) to avoid issues.
- Changes may cause brief routing flaps; test in a lab if possible.

### Example Topology
- **Hub**: Public IPs on port1 (20.0.0.1) and port2 (30.0.0.1) for overlays.
- **Spoke1**: Connects to hub via two overlays, advertises LAN 192.168.1.0/24.
- **Spoke2**: Similar, advertises LAN 192.168.2.0/24.
- Goal: Spokes peer with hub via loopback, enabling spoke-to-spoke shortcuts.

### Step-by-Step Guide to Change from Per Overlay to Per Loopback

#### Step 1: Create Loopback Interfaces
On all devices (hub and spokes), add a loopback interface. This will be the stable endpoint for BGP peering, independent of overlay interfaces.

**On Hub:**
```
config system interface
    edit "Loopback"
        set vdom "root"
        set ip 172.16.100.1 255.255.255.255
        set allowaccess ping
        set type loopback
        set role lan
    next
end
```

**On Spoke (e.g., Spoke1):**
```
config system interface
    edit "Loopback"
        set vdom "root"
        set ip 172.16.100.2 255.255.255.255
        set allowaccess ping
        set type loopback
    next
end
```
- Repeat for other spokes with unique IPs (e.g., Spoke2: 172.16.100.3).
- This ensures loopbacks are always up, unlike overlay interfaces that can flap.

#### Step 2: Update IPsec Configuration for Loopback Advertisement
Modify existing phase1-interfaces to advertise the loopback IP via IKE (exchange-ip-addr4). This injects static routes for loopback reachability over the tunnels. Disable add-route if enabled in per-overlay setup to avoid conflicts.

**On Hub (for dynamic tunnels):**
```
config vpn ipsec phase1-interface
    edit "advpn"  # First overlay
        set exchange-ip-addr4 172.16.100.1
        set add-route disable
        set auto-discovery-sender enable
        set network-overlay enable
        set network-id 1  # Unique per overlay
    next
    edit "advpn_b"  # Second overlay
        set exchange-ip-addr4 172.16.100.1
        set add-route disable
        set auto-discovery-sender enable
        set network-overlay enable
        set network-id 2
    next
end
```

**On Spoke (e.g., Spoke1):**
```
config vpn ipsec phase1-interface
    edit "advpn"
        set exchange-ip-addr4 172.16.100.2
        set add-route disable
        set auto-discovery-receiver enable
        set network-overlay enable
        set network-id 1
        set remote-gw 20.0.0.1  # Hub's public IP for this overlay
    next
    edit "advpn_b"
        set exchange-ip-addr4 172.16.100.2
        set add-route disable
        set auto-discovery-receiver enable
        set network-overlay enable
        set network-id 2
        set remote-gw 30.0.0.1
    next
end
```
- No changes needed for phase2 unless proposals differ.
- This step replaces per-overlay route injection with loopback-focused reachability.

#### Step 3: Add Firewall Policies for Loopback Access
Allow traffic from overlays to the loopback (for BGP and health checks). In per-overlay, this might not exist or be interface-specific.

**On Hub:**
```
config firewall policy
    edit 0  # New policy ID
        set name "Overlay-to-Loopback"
        set srcintf "advpn" "advpn_b"
        set dstintf "Loopback"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"  # Or restrict to "PING" "BGP"
    next
end
```

**On Spokes:**
```
config firewall policy
    edit 0
        set name "Loopback-to-Overlay"
        set srcintf "Loopback"
        set dstintf "advpn" "advpn_b"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
    next
end
```

#### Step 4: Reconfigure BGP for Loopback Peering
Remove per-overlay neighbors (e.g., delete config neighbor for overlay IPs). Switch to loopback as update-source and interface. Enable features for recursion and multipath.

**On Hub (use neighbor-group for dynamic spokes):**
```
config router bgp
    set as 65500
    set router-id 172.16.100.1
    set ibgp-multipath enable
    set recursive-next-hop enable
    set tag-resolve-mode merge  # For failover across overlays

    config neighbor-group
        edit "advpn-group"
            set soft-reconfiguration enable
            set interface "Loopback"
            set remote-as 65500
            set update-source "Loopback"
            set route-reflector-client enable  # Hub reflects routes to spokes
        next
    end

    config neighbor-range
        edit 1
            set prefix 172.16.100.0 255.255.255.0  # Loopback subnet
            set neighbor-group "advpn-group"
        next
    end

    config network
        edit 1
            set prefix 172.16.100.0 255.255.255.0  # Advertise loopback subnet
        next
        # Add hub LANs if needed
    end
end
```
- Disable next-hop-self to preserve ADVPN shortcuts.
- Disable network-import-check if loopback isn't in routing table.

**On Spoke (e.g., Spoke1, peer directly to hub loopback):**
```
config router bgp
    set as 65500
    set router-id 172.16.100.2
    set ibgp-multipath enable
    set recursive-next-hop enable
    set tag-resolve-mode merge

    config neighbor
        edit "172.16.100.1"  # Hub loopback
            set soft-reconfiguration enable
            set interface "Loopback"
            set remote-as 65500
            set update-source "Loopback"
        next
    end

    config network
        edit 1
            set prefix 192.168.1.0 255.255.255.0  # Spoke LAN
        next
        edit 2
            set prefix 172.16.100.2 255.255.255.255  # Own loopback
        next
    end
end
```
- Add route-map for tagging if needed (e.g., for SD-WAN SLA integration).

#### Step 5: Update SD-WAN (If Applicable)
Source health checks from loopback. Avoid using BGP peer as health-check target.

**On Spokes:**
```
config system sdwan
    config members
        edit 0  # For each overlay member
            set source 172.16.100.2  # Loopback IP
        next
    end
    config health-check
        edit "hub-health"
            set source 172.16.100.2
            # Set server to a non-BGP IP if possible
        next
    end
end
```

#### Step 6: Verify and Test
- Apply configs (`end` to commit).
- Check BGP: `get router info bgp summary` (should show peers via loopback).
- Check routes: `get router info routing-table all` (next-hops should resolve to loopbacks).
- Test connectivity: Ping spoke-to-spoke, monitor for shortcuts (`diagnose vpn tunnel list`).
- If issues: Debug BGP (`diagnose debug enable; diagnose debug application bgpd -1`).