## Link Monitor Configuration (config system link-monitor)

```bash
config system link-monitor
     edit "ping-dmz" # Starts configuring a link monitor profile called **ping-dmz**. Link-monitor checks connectivity by sending ICMP probes or similar.
        set srcintf "vlan873" # Uses **vlan873** as the source interface to send pings/health-check traffic.
        set server "100.64.91.18" # Target IP address (probe server) that FortiGate will ping to test connectivity.
        set interval 1000 # Interval for checks = 1000 ms (1 second) between each probe.
        set failtime 3 # FortiGate considers the link down if 3 consecutive probes fail. (So ~3 seconds with the above interval.)
        set ha-priority 5 # If this link fails, it will decrease the local HA unit’s priority by 5. This influences HA failover decisions.
        set update-cascade-interface disable # Prevents cascading route/interface failures to other downstream interfaces. Only this monitor is affected.
        set update-static-route disable # Disables automatic removal of static routes associated with this server if link-monitor fails.
	next
end 
```
---

## HA Configuration (config system ha)

```bash
config system ha # Entering HA (High Availability) settings for the FortiGate cluster.
    set group-id 100 # Unique cluster ID = 100. Must match on all cluster members; prevents cross-talk with other clusters.
    set group-name "ha-ap" # Cluster name is "ha-ap". This appears in HA monitoring.
    set mode a-p # Sets **Active-Passive** mode. One device is master, the other standby.
	set password ENC <string> # Cluster authentication/encryption key. Used for secure HA communication. (Encrypted representation here.)
	set hbdev "port3" 0 "port4" 0 # Defines **HA heartbeat interfaces**: port3 and port4. The trailing `0` is priority weights (here equal, no preference).
	set session-pickup enable # Enables **stateful session synchronization**, so active sessions survive a failover.
	set session-pickup-connectionless enable # Also syncs **UDP/connectionless traffic** sessions between HA peers.
	set ha-mgmt-status enable # Enables **dedicated HA management** interface, so cluster members can be managed independently.
	config ha-mgmt-interfaces
	    edit 1
	        set interface "port5"
	        set gateway 194.187.94.97
        next
	end # Configures HA management: uses **port5** with gateway **194.187.94.97** for out-of-band management access to each FortiGate unit.
	set override enable # Enables **HA override mode**. The device with higher priority will always try to become primary (instead of just sticking to the current primary).
	set priority 100 # This HA member has priority **100** (higher = more preferred as primary).
	set monitor "port1" "port2" # Monitors **port1 and port2** link state. If one goes down, HA priority drops, possibly triggering failover.
	set pingserver-monitor-interface "vlan873" # Explicitly tells HA to use **vlan873** (the one you configured under link-monitor) for ping-server monitoring.
	set pingserver-secondary-force-reset disable # If the backup unit detects ping-server failure, it does **not forcibly reset connections** when doing takeover/failover.
end
```