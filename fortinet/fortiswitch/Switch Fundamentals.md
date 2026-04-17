### What is Ethernet Switching? 

- Switches forward **Ethernet frames** based on their **destination MAC address**.
- MAC addresses identify Network Interface Cards (NICs) in the network.
- Ethernet switches:
  - Operate at **Layer 2** of the OSI model.
  - Learn MAC addresses and record them in **MAC address tables**.
  - Are usually deployed in LANs.
  - Provide **high-performance connectivity** (high bandwidth + low latency).
### Collision and Broadcast Domains

- A **collision domain** = one or more devices sharing the same network segment. If two devices transmit at the same time → collision → retransmissions → low performance.
- Hubs extend collision domains (they flood everything).
- **Switches break down collision domains** using **segmentation** (each port becomes its own collision domain). Full-duplex connections eliminate collisions completely.
- A **broadcast domain** = the network portion reachable using Layer 2 broadcasts. All devices in it receive broadcasts (e.g., ARP requests).
- One broadcast domain can contain **multiple** collision domains.

Switches stop the crashes but not the announcements (unless you use VLANs).

### MAC Address

- A MAC address is a **48-bit (6-byte)** unique identifier assigned to the NIC.
- Structure:
  - 💥<ins>First 3 bytes = **OUI** (Organizationally Unique Identifier) → identifies the manufacturer (Fortinet OUIs: 04:D5:90 and 00:09:0F)</ins>.
  - Last 3 bytes = **NIC specific**.
- Bits in first byte:
  - I/G bit (least significant): 0 = unicast, 1 = multicast.
  - U/L bit: 0 = globally unique, 1 = locally administered.
- Notation (Fortinet mostly uses **colon-separated**):
  - Colon: `04:D5:90:39:73:30`
  - Hyphen: `04-D5-90-39-73-30`
  - Period: `04D5.9039.7330`

Your device’s “hardware fingerprint.” It never changes (unless you spoof it). The OUI tells you who made the NIC.
### Destination MAC Address
- Depends on the type of transmission:
  - **Unicast**: One device only (normal traffic).
  - 💥<ins>**Multicast**: Many devices in a group (I/G bit = 1, e.g., `01:00:5E:xx:xx:xx` for IGMP)</ins>
  - **Broadcast**: All devices (`FF:FF:FF:FF:FF:FF`).
### Ethernet Frame Format
**Key points from the PDF:**
- Standard frame (payload up to 1500 bytes):
  - Destination MAC (6 bytes)
  - Source MAC (6 bytes)
  - VLAN tag (optional, 4 bytes)
  - Ethertype/Length (2 bytes) → e.g., 0x0800 = IPv4, 0x86DD = IPv6
  - Payload (46–1500 bytes)
  - FCS (4 bytes) — error checking (usually stripped by NIC)

- **Standard sizes**: 1518 bytes (no VLAN), 1522 bytes (with VLAN). Minimum 64 bytes.
- **Runts** (<64 bytes) are dropped.
- **Jumbo frames**: FortiSwitch supports up to **9216 bytes** (9000-byte payload + 216-byte header).  
  **Default MTU on all FortiSwitch ports = 9216 bytes**.

The “envelope” that carries your data. FortiSwitch is ready for big envelopes (jumbo frames) out of the box.

**Latest Fortinet confirmation:**  
💥<ins>FortiGate/FortiSwitch interfaces support jumbo frames up to 9216 bytes by default on most models.💥</ins>

### MAC Learning and Frame Forwarding

- Switch maintains a **MAC address table** (maps MAC → port + VLAN).
- **Learning**: From **source** MAC of incoming frames → dynamic entries with aging timer.
- **Forwarding**:
  - Destination MAC **known** → forward only to that port.
  - Destination MAC **unknown** → **flood** to all ports except the ingress port.
- Static entries possible (manual or system-created).

The switch watches who is talking (source MAC) and remembers the door (port) they came from. Next time someone wants to talk to them, it uses the shortcut instead of shouting.

### MAC Address Table and MAC Address Aging Timer

- 💥<ins>**View the table** (on FortiGate for managed switches):</ins>
  ```bash
  diagnose switch-controller switch-info mac-table
  ```
  Example output:
  ```
  MAC: 00:e0:4c:36:0e:a6  VLAN: 10  Port: port1
  MAC: 5c:85:7e:32:16:a2  VLAN: 10  Port: port2
  ```

- 💥<ins>**Aging timer** (default = **300 seconds** = 5 minutes):</ins>
  ```bash
  config switch-controller global
      set mac-aging-interval <seconds>   # 10 to 1,000,000 (0 = disable)
  end
  ```

The table is the switch’s memory. Old entries are automatically deleted after 5 minutes of silence (so the table doesn’t fill up with devices that left the network).

**Latest Fortinet confirmation (7.6.5):**  
💥<ins>Default still 300 seconds. You can change it globally exactly as shown above.</ins>

---
### Switch Port Types

- FortiSwitch supports a **wide variety of ports** to meet different needs.
- Table from the PDF:

| Port Type       | Speed                          | Cable              | Transceiver | Split Port                  | PoE              |
|-----------------|--------------------------------|--------------------|-------------|-----------------------------|------------------|
| 1G RJ45         | 1G / 100M / 10M                | Copper             | —           | —                           | PoE / PoE+       |
| 2.5G/5G RJ45    | 5G / 2.5G / 1G / 100M / 10M    | Copper             | —           | —                           | PoE / PoE+ / UPoE|
| 1G SFP          | 1G                             | Fiber / Copper     | Yes         | —                           | —                |
| 10G SFP+        | 10G / 1G                       | Fiber / Copper / DAC | Yes       | —                           | —                |
| 40G QSFP+       | 40G                            | Fiber / DAC        | Yes         | 4×10G / 4×1G                | —                |
| 100G QSFP28     | 100G                           | Fiber              | Yes         | 2×50G / 4×25G / 4×10G / 4×1G | —              |

- **SFP types explained**:
  - SFP: 1G fixed.
  - SFP+: up to 10G (backward compatible with SFP).
  - QSFP+: 40G (can be split with breakout cable).
  - QSFP28: 100G (highest performance, can be split).
- DAC = Direct Attach Copper (cheap, short-distance twinax cable with built-in transceivers).
- More details: See *FortiSwitch-Compatible Transceivers* document on docs.fortinet.com.

You can mix copper (cheap & easy) and fiber/SFP (long distance or high speed) on the same switch. 💥<ins>Split ports let one physical 40G/100G port act as multiple 10G/25G ports with the right cable.</ins>

**Latest Fortinet docs (7.6.5):**  
Same port types and split-port support. See “Configuring port speed and status” → https://docs.fortinet.com/document/fortiswitch/7.6.5/fortilink-guide/801177/configuring-port-speed-and-status
### Switch Ports on the FortiGate GUI

- Go to **WiFi & Switch Controller → FortiSwitch Ports** (make sure **Port** tab is selected).
- Shows every port on every managed switch.
- Also shows trunks and faceplates (switch to **Faceplates** tab if needed).
- **How to edit**:
  - Hover over any setting → pencil icon appears → click to change.
  - Right-click any column header → add more columns.
  - Right-click a specific port → context menu (Set Mode, View Statistics, Reset PoE, etc.).

This is your central dashboard for all switch ports. Everything is editable directly from FortiGate — no need to log into the switch itself.

**Latest Fortinet docs (7.6.5):**  
“FortiSwitch ports display” confirms the exact same GUI page and right-click actions → https://docs.fortinet.com/document/fortiswitch/7.6.5/fortilink-guide/801185/fortiswitch-ports-display
### Switch Ports on the FortiGate CLI

💥<ins>**View port status**:</ins>
```bash
execute switch-controller get-conn-status <switch-name>
```
Example output shows:
- Interface status (up/down)
- Duplex, speed, FortiLink, stacking, PoE status
- Aggregate interfaces / trunks

**View or change port configuration**:
```bash
show switch-controller managed-switch <switch-name>
```
Look for the `config ports` subsection:
```bash
config switch-controller managed-switch
    edit "Access-1"
        config ports
            edit <port>          # e.g. port1
                set <setting> ...   # mode, native-vlan, etc.
            next
        end
    next
end
```

Use the `execute` command for quick status. Use `config switch-controller managed-switch` for detailed config (same structure as FortiGate interfaces).
### Displaying Switch Faceplate on the FortiGate GUI

- On **FortiSwitch Ports** page → click **Faceplates** tab.
- FortiLink discovery frames automatically build the visual faceplate.
- At the top: list of FortiSwitch VLANs → click one to highlight ports assigned to that **native VLAN**.
- Hover over any port → tooltip shows:
  - Link status
  - Switch it belongs to
  - Assigned VLANs
  - PoE status
  - STP status

It’s a visual picture of the physical switch front panel — super useful for troubleshooting which physical port is which.

**Latest Fortinet docs (7.6.5):**  
Confirmed in “FortiSwitch ports display” — Faceplates tab still works exactly the same.
### Displaying Transceivers on the FortiGate GUI

- On **FortiSwitch Ports** page → locate the **Transceiver** column.
- Hover over any transceiver entry → pop-up shows:
  - Transceiver type
  - Vendor name
  - Part number

No need to physically look at the switch — the GUI tells you exactly what SFP/SFP+ is plugged in.
### Displaying Transceivers on the FortiGate CLI 

**Summarized information**:
```bash
diagnose switch-controller switch-info modules summary <switch> <port>
```
Example:
```
port25   INSERT   SFP/SFP+   N   1000-Base-T   OK   +Fortinet   ICP-1250RJ...
```

**Detailed information**:
```bash
diagnose switch-controller switch-info modules detail <switch> <port>
```
Shows:
- Identifier, connector, encoding, supported lengths, vendor, part number, serial number, manufacture date, etc.

Use `summary` for quick check, `detail` when you need the full datasheet of the transceiver.

**Latest Fortinet docs (7.6.5):**  
These exact `diagnose` commands are still used in the FortiLink Guide (Diagnostics and Tools section).