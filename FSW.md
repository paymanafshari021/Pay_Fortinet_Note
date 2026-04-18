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