```bash
diag debug reset
diag debug enable
diag debug console timestamp enable
diag debug application dhcprelay -1

diag debug disable
diag debug reset
```

```bash
diag sniffer packet any 'port 67 or port 68' 4 0 a
```

```bash
diag debug disable
diag debug flow trace stop
diag debug flow filter clear
diag debug reset

diag debug flow filter addr x.x.x.x
diag debug flow filter proto 17
diag debug flow filter port 67

diag debug flow show function-name enable
diag debug flow show iprope enable
diag debug console timestamp enable
diag debug flow trace start 10
diag debug enable
```

## Common DHCP Relay Issues
1. **Clients not receiving IP addresses**:
   - DHCP requests not reaching the server.
   - DHCP responses not relayed back to clients.
   - Incorrect routing or firewall policies.
2. **Configuration errors**:
   - Wrong DHCP server IP in relay settings.
   - Incorrect interface or VLAN configuration.
   - DHCP relay not enabled on the correct interface.
3. **Firmware bugs**:
   - Known issues in specific FortiOS versions (e.g., 6.4.12) affecting DHCP relay.[](https://www.reddit.com/r/fortinet/comments/11rdic5/fortigate_dhcp_relay_bug/)
4. **Network issues**:
   - DHCP traffic blocked by firewall policies or ACLs.
   - DHCP snooping or guarding enabled on switches, restricting traffic.[](https://www.reddit.com/r/fortinet/comments/o9md04/dhcp_relay_issue/)
5. **Multiple VLANs**:
   - Relay issues when configuring multiple VLANs to the same DHCP server.[](https://www.reddit.com/r/fortinet/comments/cdlp0g/vlan_dhcp_relay_issue/)

## Troubleshooting Steps
### 1. Verify DHCP Relay Configuration
- **GUI Check**:
  1. Go to **Network > Interfaces** > Edit the interface.
  2. Ensure **DHCP Server** is enabled, set to **Relay** mode, and the correct DHCP server IP is specified.[](https://www.krakovic.de/fortigate-wie-konfiguriert-man-dhcp-relay-dhcp-helper/)
  3. Verify **Type** is set to **Regular** (or **IPsec** if applicable).
- **CLI Check**:
  ```bash
  config system interface
      edit <interface_name>
          show
  ```
  Look for:
  - `set dhcp-relay-service enable`
  - `set dhcp-relay-ip <DHCP_server_IP>`
  - For multiple DHCP servers: `set dhcp-relay-ip <IP1> <IP2> ... <IP8>` (up to 8 IPs).[](https://www.krakovic.de/fortigate-wie-konfiguriert-man-dhcp-relay-dhcp-helper/)
- **Enable Advanced DHCP Options (if hidden in GUI)**:
  ```bash
  config system global
      set gui-dhcp-advanced enable
  end
  ```

### 2. Check Routing
- Ensure a static route exists to the DHCP server if it’s not on a directly connected interface.[](https://www.krakovic.de/fortigate-wie-konfiguriert-man-dhcp-relay-dhcp-helper/)
  ```bash
  config router static
      edit <route_id>
          set dst <DHCP_server_subnet> <subnet_mask>
          set gateway <next_hop_IP>
          set device <interface>
      next
  end
  ```

### 3. Verify Firewall Policies
- Ensure policies allow DHCP traffic (UDP ports 67 and 68) between client and server subnets.
  ```bash
  config firewall policy
      edit <policy_id>
          set srcintf <client_interface>
          set dstintf <server_interface>
          set srcaddr <client_subnet>
          set dstaddr <server_subnet>
          set service DHCP
          set action accept
      next
  end
  ```

### 4. Packet Capture for DHCP Traffic
- Use FortiGate’s built-in sniffer to capture DHCP packets (ports 67/68).
  ```bash
  diagnose sniffer packet any "port 67 or port 68" 4 0 l
  ```
  - Look for DORA (Discover, Offer, Request, ACK) packets.
  - Confirm if:
    - DHCP Discover reaches FortiGate.
    - FortiGate forwards Discover to the DHCP server (check `giaddr` field).
    - Server responds with Offer, and FortiGate relays it back to the client.[](https://community.fortinet.com/t5/FortiGate/Technical-Tip-Diagnosing-DHCP-on-a-FortiGate/ta-p/192960)

### 5. Enable DHCP Debug
- Enable debugging to see DHCP relay activity:
  ```bash
  diagnose debug enable
  diagnose debug console timestamp enable
  diagnose debug application dhcprelay 7
  ```
  - Look for errors like missing relay bindings or packets not forwarded.[](https://www.krakovic.de/fortigate-wie-konfiguriert-man-dhcp-relay-dhcp-helper/)
- For FortiGate as DHCP server (if applicable):
  ```bash
  diagnose debug application dhcps 7
  ```

### 6. Check DHCP Server Logs
- Verify the DHCP server receives requests and sends responses.
- Check server logs for errors or lease issues.[](https://www.reddit.com/r/fortinet/comments/11rdic5/fortigate_dhcp_relay_bug/)

### 7. Verify Switch Configuration
- If using DHCP snooping or guarding on switches, ensure the uplink to the DHCP server is trusted.[](https://www.reddit.com/r/fortinet/comments/o9md04/dhcp_relay_issue/)
  ```bash
  # Example for FortiSwitch
  config switch interface
      edit <interface>
          set dhcp-snooping trusted
      next
  end
  ```

### 8. Check FortiOS Version
- Some versions (e.g., 6.4.12) have known DHCP relay bugs. Consider upgrading to a stable version like 7.0.10 or later.[](https://www.reddit.com/r/fortinet/comments/11rdic5/fortigate_dhcp_relay_bug/)
  ```bash
  get system status
  ```

### 9. Multiple VLANs
- If relaying for multiple VLANs, ensure each VLAN interface has DHCP relay enabled and points to the correct DHCP server IP.[](https://www.reddit.com/r/fortinet/comments/cdlp0g/vlan_dhcp_relay_issue/)
- CLI example for multiple VLANs:
  ```bash
  config system interface
      edit vlan_100
          set dhcp-relay-service enable
          set dhcp-relay-ip <DHCP_server_IP>
      next
      edit vlan_200
          set dhcp-relay-service enable
          set dhcp-relay-ip <DHCP_server_IP>
      next
  end
  ```

### 10. Test with Static IP
- Assign a static IP to a client in the same subnet and test connectivity to the DHCP server (e.g., ping).[](https://www.reddit.com/r/fortinet/comments/bx2uks/dhcp_relay_refuses_to_work_with_dhcp_server/)
  ```bash
  ping <DHCP_server_IP>
  ```

## Common Fixes
- **Incorrect DHCP Server IP**: Update the relay IP in the interface settings.
- **Firewall Policy Missing**: Add a policy allowing DHCP traffic.
- **Routing Issue**: Add or correct static routes to the DHCP server.
- **Firmware Bug**: Upgrade FortiOS to a stable version.[](https://www.reddit.com/r/fortinet/comments/11rdic5/fortigate_dhcp_relay_bug/)
- **DHCP Snooping**: Trust the uplink interface on switches.[](https://www.reddit.com/r/fortinet/comments/o9md04/dhcp_relay_issue/)
- **VLAN Misconfiguration**: Ensure VLAN interfaces are correctly tagged and relay is enabled.

## Useful CLI Commands
- Check DHCP leases:
  ```bash
  execute dhcp lease-list
  ```
- Clear DHCP leases:
  ```bash
  execute dhcp lease-clear <interface> <IP>
  ```
- Show DHCP relay configuration:
  ```bash
  show system interface <interface_name>
  ```
- Reset debug:
  ```bash
  diagnose debug disable
  ```

## Additional Notes
- If using IPsec, ensure the tunnel is up and DHCP relay is configured for IPsec mode.[](https://help.cloudi-fi.com/hc/en-us/articles/14071364189725-How-to-enable-Cloudi-Fi-DHCP-with-a-FortiGate-firewall)
## References
- Fortinet Community: Diagnosing DHCP on a FortiGate[](https://community.fortinet.com/t5/FortiGate/Technical-Tip-Diagnosing-DHCP-on-a-FortiGate/ta-p/192960)
- Fortinet Community: Troubleshooting DHCP Relay Issues[](https://community.fortinet.com/t5/FortiGate/Troubleshooting-Tip-DHCP-relay-issue/ta-p/215535)
- Fortinet Document Library: DHCP Servers and Relays[](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/783526/dhcp-servers-and-relays)
- Reddit r/fortinet: DHCP Relay Bug Discussion[](https://www.reddit.com/r/fortinet/comments/11rdic5/fortigate_dhcp_relay_bug/)