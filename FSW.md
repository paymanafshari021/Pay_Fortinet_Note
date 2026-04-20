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
	 + The slide warns about a common confusion:

| MTU Type      | Applies To          | Adjusted On             |
| ------------- | ------------------- | ----------------------- |
| **Frame MTU** | Ethernet frame size | Switch (Layer 2)        |
| **IP MTU**    | IP packet size      | Router / Layer 3 device |
> When you change MTU on a **switch** → you're changing the **Ethernet frame size** When you change MTU on a **router** → you're changing the **IP packet size** - These are **different things** — don't confuse them!

+ The **MAC address table** (also known as the CAM table — Content Addressable Memory) is the brain of an Ethernet switch.
+ Two types of entries:

| Type        | How Created                                                                                     | Lifespan                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **Dynamic** | Learned automatically from incoming traffic                                                     | Removed when aging timer expires (usually **5 minutes**) — resets if traffic is seen again |
| **Static**  | Manually configured by admin, or auto-created for internal system interfaces (e.g., management) | Permanent — does not age out                                                               |
+ How MAC Learning Works — Step by Step
	+ When a switch receives a frame, it follows this process:

```
Frame arrives on a port
        ↓
1. Read the SOURCE MAC address
        ↓
2. Is it in the MAC table?
   ├── NO  → Create a new DYNAMIC entry (MAC + port + VLAN) + start aging timer
   └── YES → Update the entry + RESET the aging timer
        ↓
3. Read the DESTINATION MAC address
        ↓
4. Is it in the MAC table?
   ├── YES → Forward the frame to the PORT listed in the table
   └── NO  → FLOOD the frame to ALL ports EXCEPT the ingress port (the port it came in on)
```

> 💡 **Flooding** = sending the frame out of every port except where it came from. This is how the switch "discovers" unknown destinations — someone will respond, and that response will teach the switch where the destination is.

- The switch learns MACs from **ingress (incoming) traffic only** — specifically from the **source MAC** of each frame
- If a matching source MAC entry already exists → the aging timer is **reset** (refreshed), not a new entry created
- The aging timer is typically **5 minutes** but varies by vendor/configuration
- When the switch doesn't know where the destination is → it **floods** (not broadcasts!) — flooding is specific to unknown unicast; broadcast frames are always flooded
+ On a FortiSwitch, you can view the MAC address table with this CLI command: #cli

```bash
# View the MAC address table on FortiSwitch
diagnose switch mac-address list
```

+ Or from FortiGate (managing FortiSwitch via FortiLink):#cli

```bash
diagnose switch-controller mac-cache list
```

+ This command is run **from the FortiGate** that is managing the FortiSwitch (via FortiLink / Switch Controller): #cli

```bash
# diagnose switch-controller switch-info mac-table
Managed Switch : Access-1 0
…
MAC: 00:e0:4c:36:0e:a6  VLAN: 10  Port: port1(port-id 1)
  Flags: 0x00010441 [ hit dynamic src-hit native ]

MAC: 5c:85:7e:32:16:a2  VLAN: 10  Port: port2(port-id 2)
  Flags: 0x00010441 [ hit dynamic src-hit native ]
```

+ Flags Explained — `[ hit dynamic src-hit native ]`

| Flag      | Meaning                                                                           |
| --------- | --------------------------------------------------------------------------------- |
| `hit`     | This entry has been **recently matched/used** — traffic was seen from/to this MAC |
| `dynamic` | This is a **dynamically learned** entry (not static — will age out)               |
| `src-hit` | The MAC was learned from the **source MAC** of an incoming frame                  |
| `native`  | The MAC was learned on the **native VLAN** of the port (untagged traffic)         |
+ The **default aging timer is 300 seconds (5 minutes)**. To change it globally for all managed FortiSwitches, run this from FortiGate:
	+ This command is run on the **FortiGate**, not directly on the FortiSwitch, because FortiSwitch is managed centrally by the FortiGate Switch Controller.

```bash
config switch-controller global
    set mac-aging-interval <seconds>
end
```
+ Split Port (Breakout) Feature
	+ High-speed ports (40G and 100G) can be **split into multiple lower-speed ports** using a **breakout cable**:

| Port            | Split Options                                     |
| --------------- | ------------------------------------------------- |
| **40G QSFP+**   | → 4× 10G **or** 4× 1G                             |
| **100G QSFP28** | → 2× 50G **or** 4× 25G **or** 4× 10G **or** 4× 1G |
* **How it works:** 
	* A breakout cable has **one QSFP+ or QSFP28 connector** on one end and **4 SFP+ connectors** on the other — physically splitting the single high-speed port into multiple independent lower-speed ports.
* ❌ SFP ports do **not** support PoE. PoE is exclusive to **RJ45 copper ports**:

| PoE Standard       | Max Power Delivered | Typical Use                                |
| ------------------ | ------------------- | ------------------------------------------ |
| **PoE** (802.3af)  | 15.4W per port      | IP phones, basic cameras                   |
| **PoE+** (802.3at) | 30W per port        | PTZ cameras, dual-band APs                 |
| **UPoE** (802.3bt) | 60W per port        | High-power APs, video conferencing systems |

> ⚠️ The **2.5G/5G RJ45** ports support UPoE — important for **Wi-Fi 6/6E access points** that need both high bandwidth AND high power.

+ DAC = Direct Attach Copper
	+ It's a **twinax copper cable** with **transceivers factory-attached** at both ends
	- Used for **short-distance, high-speed connections** (typically up to 7 meters)
	- **More cost-effective** than fiber + separate transceivers
	- Common use: connecting a switch directly to a server in the same rack

+ ### Display Port Status from FortiGate CLI
```bash
execute switch-controller get-conn-status Access-1
```

> ⚠️ **You must include the Switch ID** (e.g., `Access-1`) at the end — without it, port information won't be shown.

```bash
Get managed-switch Access-1 connection status.
Admin Status: Authorized # The FortiSwitch is **authorized** to be managed by this FortiGate
Connection: Connected (capwap) # Switch is **actively connected** via the **CAPWAP** protocol (the same protocol used for managing APs)
Image Version: FS24VM-v7.6.1-build5996,241205(Interim)
Remote Address: 10.0.13.2
Join Time: Tue Jun 3 11:48:30 2025
interface   status   duplex   speed      fortilink   stacking   poe status
    port1     up      full    1000Mbps      no          no      Not Supported
…
    port3     up      full    1000Mbps      no          yes     Not Supported
…
# `fortilink``yes` = this port is a **FortiLink uplink** to FortiGate, `no` = regular port
Aggregate Interfaces:
         Interface      Status   Duplex   Speed       Type
    _FlInKl_MLAG0_(*)    up      full    1000Mbps     ISL

```

+ Show the managed switch config:
```bash
show switch-controller managed-switch Access-1
config switch-controller managed-switch
    edit "Access-1"
    ...
        config ports
            edit <port>
                set <setting> ...
            next
        end
    next
end
```
+ Command — Summarized Transceiver Information
```bash
# Syntax: `diagnose switch-controller switch-info modules summary <switch-id> <port>`
diagnose switch-controller switch-info modules summary Access-1 port25
Vdom: root

Core-1:
  Portname   State    Type      DMI   Transceiver   RX    Vendor      Part Number        Serial Number
  --------   ------   -------   ---   -----------   ---   --------    ----------------   ------------
  port25     INSERT   SFP/SFP+  N     1000-Base-T   OK    +Fortinet   LCP-1250RJ3SRTFN   233401002279
  # A transceiver is **physically inserted** in this port (`EMPTY` = no transceiver)
  # **Digital Monitoring Interface** — `N` = not supported by this transceiver, `Y` = supported (allows real-time monitoring of TX/RX power, temperature, etc.)
 
```
+ Command — Detailed Transceiver Information
```bash
# Syntax: diagnose switch-controller switch-info modules detail <switch-id> <port>
diagnose switch-controller switch-info modules detail Core-1 port25
Vdom: root

Core-1:
Port(port25)
identifier        QSFP+
connector         LC
transceiver       Unk(0x00)
encoding          64B/66B
Length Decode Common
  length_smf_1km  N/A
  length_cable    N/A
vendor            AVAGO
vendor_oid        0x00176A
vendor_pn         AFBR-79EBPZ
vendor_rev        01
vendor_sn         AVM2251S1LA
manuf_date        12/14/2022
```