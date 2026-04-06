### 1. What is Ethernet Switching? 

- Switches forward **Ethernet frames** based on their **destination MAC address**.
- MAC addresses identify Network Interface Cards (NICs) in the network.
- Ethernet switches:
  - Operate at **Layer 2** of the OSI model.
  - Learn MAC addresses and record them in **MAC address tables**.
  - Are usually deployed in LANs.
  - Provide **high-performance connectivity** (high bandwidth + low latency).
### 2. Collision and Broadcast Domains

- A **collision domain** = one or more devices sharing the same network segment. If two devices transmit at the same time → collision → retransmissions → low performance.
- Hubs extend collision domains (they flood everything).
- **Switches break down collision domains** using **segmentation** (each port becomes its own collision domain). Full-duplex connections eliminate collisions completely.
- A **broadcast domain** = the network portion reachable using Layer 2 broadcasts. All devices in it receive broadcasts (e.g., ARP requests).
- One broadcast domain can contain **multiple** collision domains.

Switches stop the crashes but not the announcements (unless you use VLANs).

### 3. MAC Address

- A MAC address is a **48-bit (6-byte)** unique identifier assigned to the NIC.
- Structure:
  - First 3 bytes = **OUI** (Organizationally Unique Identifier) → identifies the manufacturer (Fortinet OUIs: 04:D5:90 and 00:09:0F).
  - Last 3 bytes = **NIC specific**.
- Bits in first byte:
  - I/G bit (least significant): 0 = unicast, 1 = multicast.
  - U/L bit: 0 = globally unique, 1 = locally administered.
- Notation (Fortinet mostly uses **colon-separated**):
  - Colon: `04:D5:90:39:73:30`
  - Hyphen: `04-D5-90-39-73-30`
  - Period: `04D5.9039.7330`

Your device’s “hardware fingerprint.” It never changes (unless you spoof it). The OUI tells you who made the NIC.
### 4. Destination MAC Address
- Depends on the type of transmission:
  - **Unicast**: One device only (normal traffic).
  - **Multicast**: Many devices in a group (I/G bit = 1, e.g., `01:00:5E:xx:xx:xx` for IGMP)
  __text__
  - **Broadcast**: All devices (`FF:FF:FF:FF:FF:FF`).
### 5. Ethernet Frame Format (PDF Page 11)
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

**Simple explanation:**  
The “envelope” that carries your data. FortiSwitch is ready for big envelopes (jumbo frames) out of the box.

**Latest Fortinet confirmation:**  
FortiGate/FortiSwitch interfaces support jumbo frames up to 9216 bytes by default on most models.

### 6. MAC Learning and Frame Forwarding (PDF Page 12)
**Key points from the PDF:**
- Switch maintains a **MAC address table** (maps MAC → port + VLAN).
- **Learning**: From **source** MAC of incoming frames → dynamic entries with aging timer.
- **Forwarding**:
  - Destination MAC **known** → forward only to that port.
  - Destination MAC **unknown** → **flood** to all ports except the ingress port.
- Static entries possible (manual or system-created).

**Example in PDF (two switches + two PCs in VLAN 10):**  
Frame from PC1 to PC2 is forwarded port9 → port1.

**Simple explanation:**  
The switch watches who is talking (source MAC) and remembers the door (port) they came from. Next time someone wants to talk to them, it uses the shortcut instead of shouting.

### 7. MAC Address Table and MAC Address Aging Timer (PDF Page 13)
**Key points from the PDF:**
- **View the table** (on FortiGate for managed switches):
  ```bash
  # diagnose switch-controller switch-info mac-table
  ```
  Example output:
  ```
  MAC: 00:e0:4c:36:0e:a6  VLAN: 10  Port: port1
  MAC: 5c:85:7e:32:16:a2  VLAN: 10  Port: port2
  ```

- **Aging timer** (default = **300 seconds** = 5 minutes):
  ```bash
  config switch-controller global
      set mac-aging-interval <seconds>   # 10 to 1,000,000 (0 = disable)
  end
  ```

**Simple explanation:**  
The table is the switch’s memory. Old entries are automatically deleted after 5 minutes of silence (so the table doesn’t fill up with devices that left the network).

**Latest Fortinet confirmation (7.6.5):**  
Default still 300 seconds. You can change it globally exactly as shown above.

---

**You’ve now mastered the complete foundation of Ethernet switching on FortiSwitch!**  
These concepts are used in **every** later topic (VLANs, STP, security, etc.).

**What next?**  
Just tell me:
- “Next topics in Lesson 01” (Link Aggregation + Layer 2 Discovery)
- Or a specific page/topic (e.g., “Page 34” or “Link Aggregation”)
- Or jump to Lesson 02: Managed Switch (Page 62)

Your turn — what do you want to learn now? 🚀