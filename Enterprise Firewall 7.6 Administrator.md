# Page 18 — Life of a Packet: Initial Session Packets

![lifeofapacket](images/lifeofapacket.png)

### INGRESS SIDE (Left Column — Incoming Packet Path)

Packets enter from the left and flow **top to bottom** through these mandatory and optional steps:

#### 1. **Network Interface (Ingress)**
The packet arrives on a physical or logical interface. This is the entry point into the FortiGate.

#### 2. **Access Control List (ACL)**
- This is an **NP7/NP6-offloaded** step (note the chip icon on the left).
- ACLs are checked first to drop obviously bad or unwanted traffic before it consumes CPU resources.
- This is a **hardware-level** check — very fast.

#### 3. **Host Protection Engine (HPE)**
- Also handled by the **NP** (Network Processor).
- HPE protects the FortiGate itself from DoS attacks (e.g., rate-limiting SYN floods).
- It prevents the FortiGate's CPU from being overwhelmed by attack traffic.

#### 4. **IP Integrity Header Checking**
- Again, NP-offloaded.
- Validates that IP headers are well-formed and not malformed/crafted to exploit vulnerabilities.
- Malformed packets are dropped here.

#### 5. **IPsec VPN Decryption** *(Optional/Configurable — shown in dashed box)*
- If the incoming packet is IPsec-encrypted, it is **decrypted here** before further processing.
- The NP can handle this in hardware if the algorithm is supported.
- This is shown as **optional** because it only applies to IPsec VPN traffic.

---

### MIDDLE PROCESSING (Center — Kernel + UTM/NGFW)

After ingress processing, the packet moves to the center for deep inspection.

#### Kernel Processing (Blue box — 8 steps):
This is where the core FortiOS kernel handles the session. Steps in order:

1. **Destination NAT** — If a VIP (Virtual IP) or DNAT policy applies, the destination IP is translated *before* routing.
2. **Routing, RPF check, and SD-WAN** — The FortiGate looks up the routing table. Reverse Path Forwarding (RPF) verifies the source IP is reachable via the expected interface. SD-WAN rules are evaluated here.
3. **Stateful inspection / policy lookup / session management** — The firewall policy table is checked. If a matching policy is found, a **session entry** is created in the session table. This is where ALLOW or DENY is decided.
4. **Session helpers** — For protocols like FTP, SIP, H.323 that embed IP/port info in the payload, session helpers rewrite or track that data.
5. **User authentication** — If the policy requires authentication (captive portal, FSSO, RSSO, etc.), it is checked here.
6. **Device identification** — FortiGate identifies the device type based on DHCP fingerprinting, HTTP User-Agent, and other signals.
7. **SSL VPN** — SSL VPN tunnel traffic is processed/decrypted here.
8. **Local management traffic** — Traffic destined *for* the FortiGate itself (e.g., GUI, SSH, SNMP) is handled here rather than forwarded.

#### UTM/NGFW Processing (Yellow box — 4 steps):
This is the **content inspection layer**, handled partly by the **CP (Content Processor)** and partly by the **CPU**:

1. **Flow-based inspection** — The CP chip accelerates pattern matching for IPS, antivirus, and application control in flow mode. Faster but slightly less thorough than proxy-based.
2. **Proxy-based inspection** — Full content buffering and deep inspection (e.g., antivirus in full scan mode, web filtering with certificate inspection). Handled by the CPU — more thorough but slower.
3. **Explicit web proxy** — If FortiGate is configured as an explicit proxy, this handles that traffic path.
4. **Botnet check** — Checks source/destination IPs and domains against Fortinet's botnet database.

---

### 📤 EGRESS SIDE (Right Column — Outgoing Packet Path)

After processing, the packet exits top to bottom:

1. **Kernel: Forwarding + Source NAT** — The packet is forwarded toward its destination. If an outbound NAT policy (SNAT) applies, the source IP is translated here.
2. **IPsec VPN Encryption** *(Optional — NP-offloaded)* — If the outbound traffic needs to be sent over an IPsec tunnel, it is encrypted here.
3. **Traffic Shaping** *(Optional/Configurable)* — QoS and bandwidth shaping policies are applied.
4. **WAN Optimization** *(Optional/Configurable)* — WAN-OPT (if licensed and configured) compresses and deduplicates traffic.
5. **Network Interface (Egress)** — The packet exits via the outbound interface.

---

### 🟡 Color Legend
- **Orange/Yellow boxes** = Optional or configurable steps (only happen if the feature is enabled/licensed).
- **Blue boxes** = Mandatory steps — every packet goes through these.

---

### 🖥️ Processor Icons Explained
- **NP6/NP7 chip icon** = Handled by the Network Processor (hardware offload — no CPU used).
- **CP5/CP9/CP10 icon** = Handled by the Content Processor (hardware-accelerated content inspection).
- **CPU label** = Handled by the main FortiGate CPU (software path — slower, more flexible).

---

## 📝 Supporting Text Explanations

**On NP (Network Processor):**
The NP handles early-stage security tasks like ACL, HPE, and IP integrity checking. Once a session is fully established and the session key is installed in the NP, **subsequent packets in that session are offloaded** entirely to the NP, bypassing the CPU completely. The NP also handles IPsec encryption/decryption when the configured algorithms are hardware-supported.

**On CP (Content Processor):**
The CP acts as a **co-processor** to the FortiGate CPU. It offloads heavy compute tasks like IPS pattern matching for flow-based UTM, SSL/TLS encryption and decryption for deep SSL inspection, and IPsec operations for supported algorithms. This dramatically improves throughput for security-enabled traffic.

**On Virtual FortiGates (VMs):**
Virtual FortiGates follow **the exact same processing order**, but since there is no physical NP or CP hardware, **the CPU handles all steps**. Nothing is offloaded. This is why VM FortiGates have lower throughput than physical appliances with NP/CP chips.

---

## ⭐ FCX Exam Key Points

**Important Facts:**
- **DNAT happens BEFORE routing** — this is a classic exam question. The destination IP must be translated first so the routing table lookup uses the correct (translated) destination.
- **SNAT happens AFTER forwarding** — on the egress side, after the routing decision is made.
- **ACL, HPE, and IP integrity checks are NP-offloaded** — they happen in hardware before the CPU is involved.
- **Session helpers are in the kernel layer**, not UTM — FTP, SIP, etc. are handled before content inspection.
- **Flow-based inspection uses the CP chip; proxy-based uses the CPU.**
- **IPsec decryption is on the ingress side; IPsec encryption is on the egress side.**
- The **first packet** of a session goes through ALL these steps. **Subsequent packets** in an established session can be offloaded to the NP (hardware fast path).
- Virtual FortiGates **cannot offload** to NP or CP — the CPU handles everything.

**Exam Traps:**
- ❌ Don't say routing happens before DNAT — it's the other way around. **DNAT → Routing.**
- ❌ Don't confuse flow-based (CP-accelerated) with proxy-based (CPU-only). They are separate paths.
- ❌ Don't assume IPsec VPN decryption/encryption always uses the NP — it only does if the **algorithm is supported in hardware**.
- ❌ Traffic shaping and WAN optimization are **optional/configurable**, not mandatory steps.
- ❌ Local management traffic (to the FortiGate itself) is handled in the **kernel layer**, not UTM/NGFW.
- ❌ On VMs, **no hardware offload exists at all** — don't apply NP/CP logic to virtual FortiGates.