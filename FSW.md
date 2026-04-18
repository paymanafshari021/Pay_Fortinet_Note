+ MAC address (Media Access Control) is a 48-bit (6-byte) unique identifier assigned to a NIC (Network Interface Card)
+ MAC addresses are globally unique but **can be overridden** — a common real-world use case is cloning a MAC address when replacing a faulty device
+ Fortinet OUIs - Left Half : 04:D5:90 and 00:09:0F
+ Fortinet notationColon-separated (`XX:XX:XX:XX:XX:XX`)
+ [ bit8 | bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 ]
                                    ↑      ↑
	                            I/G    U/L
+ I/G Bit (bit1) — Individual/Group

| Value | Meaning                                               |
| ----- | ----------------------------------------------------- |
| `0`   | **Unicast** — frame intended for a single device      |
| `1`   | **Multicast** — frame intended for a group of devices |

> If a frame is a multicast application, the I/G bit must be set to `1`.
+ U/L Bit (bit2) — Universal/Local

| Value | Meaning                                                  |
| ----- | -------------------------------------------------------- |
| `0`   | **Globally unique** — assigned by IEEE/manufacturer      |
| `1`   | **Locally administered** — manually set by administrator |

> If the MAC was locally configured by an admin, both the U/L bit AND the I/G bit should be set to `1`.
+ **In most cases both bits are 0** (unicast, globally unique)
+ MAC Address Notation — 3 Formats
The same MAC address can be written in three different ways:

| Format               | Example             | Notes                                |
| -------------------- | ------------------- | ------------------------------------ |
| **Colon-separated**  | `04:D5:90:39:73:3D` | ⭐ Used by **most Fortinet products** |
| **Hyphen-separated** | `04-D5-90-39-73-3D` | Common on Windows                    |
| **Period-separated** | `04D5.9039.733D`    | Common on Cisco devices              |
+ The Three Types — Side by Side

|Type|Destination|Example MAC|Special Rule|
|---|---|---|---|
|**Unicast**|One specific device|`04:D5:90:39:73:3D`|Normal MAC address|
|**Multicast**|A group of devices|`01:00:5E:00:00:01`|I/G bit = `1`|
|**Broadcast**|ALL devices in the broadcast domain|`FF:FF:FF:FF:FF:FF`|All bytes = `FF`|
+ `01:00:5E:00:00:01` — this is an **IGMP (Internet Group Management Protocol)** multicast MAC
+ Broadcast frames create more load on the network because **every device must process them** — this is why limiting broadcast domains (via VLANs) is important
+ Frame Structure:

| Field                  | Size               | Purpose                                                                                    |
| ---------------------- | ------------------ | ------------------------------------------------------------------------------------------ |
| **Destination MAC**    | 6 bytes            | MAC address of the intended recipient                                                      |
| **Source MAC**         | 6 bytes            | MAC address of the sender (switch learns from this)                                        |
| **VLAN Tag**           | 4 bytes (optional) | Tags the frame with a VLAN ID — added by devices to identify which VLAN a frame belongs to |
| **Ethertype / Length** | 2 bytes            | Identifies the type of payload OR the frame length                                         |
| **Payload**            | 46–1500 bytes      | The actual data being transported (usually an IP packet)                                   |
| **FCS**                | 4 bytes            | Frame Check Sequence — used to detect corrupted/errored frames                             |
+ Ethertype / Length
	- If the value is **≥ 1536 (0x0600)** → it's an **Ethertype** (identifies payload protocol)
	- If the value is **≤ 1500** → it's a **Length** field
	- Common Ethertypes:
	    - `0x0800` = **IPv4**
	    - `0x86DD` = **IPv6**
	    - `0x8100` = **802.1Q VLAN tag**
+ FCS (Frame Check Sequence)
	- A **4-byte checksum** calculated over the entire frame
	- The receiving NIC recalculates the FCS and compares it — if they don't match, the frame is **dropped** (corrupted)
	- ⚠️ The FCS is **checked and stripped by the NIC** before passing to the OS/host
	- This is why **Wireshark typically does NOT show the FCS** — it's already been removed by the time the capture happens
-  Frame Size Rules
	- Standard Ethernet (Non-Jumbo) Frames:

| Condition                     | Maximum Size   |
| ----------------------------- | -------------- |
| Without VLAN tag              | **1518 bytes** |
| With VLAN tag (4 extra bytes) | **1522 bytes** |
+  Minimum Frame Size:
	- **64 bytes** (including FCS)
	- Frames smaller than 64 bytes are called **Runts** and are **dropped by NICs**
	- Runts often indicate a collision or hardware problem

+ Jumbo Frames (FortiSwitch specific):

	- FortiSwitch supports jumbo frames up to **9216 bytes**
	- Jumbo frame breakdown:
	    - Payload: up to **9000 bytes**
	    - Header: **216 bytes**
	    - Total: **9216 bytes**
	- **All FortiSwitch ports have an MTU of 9216 bytes by default** — jumbo frames are supported out of the box!
 + ⚠️  Important: Frame MTU vs IP MTU

The slide warns about a common confusion:

|MTU Type|Applies To|Adjusted On|
|---|---|---|
|**Frame MTU**|Ethernet frame size|Switch (Layer 2)|
|**IP MTU**|IP packet size|Router / Layer 3 device|
> When you change MTU on a **switch** → you're changing the **Ethernet frame size** When you change MTU on a **router** → you're changing the **IP packet size**

These are **different things** — don't confuse them!