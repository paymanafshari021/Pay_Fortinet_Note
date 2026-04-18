+ MAC address (Media Access Control) is a 48-bit (6-byte) unique identifier assigned to a NIC (Network Interface Card)
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
+ MAC Address Notation — 3 Formats
The same MAC address can be written in three different ways:

| Format               | Example             | Notes                                |
| -------------------- | ------------------- | ------------------------------------ |
| **Colon-separated**  | `04:D5:90:39:73:3D` | ⭐ Used by **most Fortinet products** |
| **Hyphen-separated** | `04-D5-90-39-73-3D` | Common on Windows                    |
| **Period-separated** | `04D5.9039.733D`    | Common on Cisco devices              |
+ 