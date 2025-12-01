## SD-WAN Overlay Deployment
![[16.png]]
## IPsec VPN Tunnel Configuration in Hub-and-Spoke Topology on FortiManager

#### Options for Configuration
- Primary method: Use the SD-WAN overlay orchestrator for automated IPsec tunnel setup.
- Alternative method: Manually use predefined IPsec tunnel templates (no orchestrator required).
#### Key Features of Both Methods
- FortiManager applies the same recommended IPsec settings in both cases.
#### Predefined Template Usage
- Templates: **BRANCH_IPsec_Recommended** and **HUB_IPsec_Recommended**
- Administrator only needs to define main parameters.
- Templates automatically add recommended settings covered in the lesson (e.g., tunnel type, net-device).
- Templates can be edited later for fine-tuning and advanced options.
#### ADVPN Support
- Predefined templates also support enabling ADVPN with corresponding recommended parameters in hub-and-spoke topologies.
---
![[17.png]]
## IPsec Templates Settings BGP per Overlay Orchestrator (Default/Recommended)  in FortiManager

FortiManager automatically prepares IPsec templates for **hub-and-spoke topology** with **BGP per overlay** routing.
#### Hub Template Settings
- **Tunnel type**: Dynamic (dial-up server)  
  → Hub accepts connections from spokes with unknown/dynamic IP addresses
- **net-device**: disable  
  → Improves performance optimization due to high number of tunnels
- Configures system interface settings (IP address and allowed remote IP range) on the new VPN interface
#### Branch (Spoke) Template Settings
- **Tunnel type**: Static (dial-up client)  
  → Hub IP addresses are known and fixed
- **net-device**: enable  
  → Required for ADVPN shortcut support; performance impact is minimal due to low overlay count on spokes
#### Common Settings (Both Hub and Branch)
- **network-overlay**: enable
- **network-id**: configured (unique per overlay)  
  → Prevents cross-overlay traffic
**Optional**: Add keepalive settings to both templates as needed.
---
![[18.png]]
## IPsec Templates Settings - BGP on Loopback Overlay Template in FortiManager

**Key Behavior** When "BGP on loopback" is selected in the overlay template, FortiManager automatically optimizes the generated IPsec templates for loopback-based BGP routing.
**Primary Difference in Tunnel IP Addressing**

- **BGP per overlay**: Uses standard IKE mode-config for tunnel endpoint assignment
- **BGP on loopback**: Uses Fortinet-proprietary **exchange-interface-ip** option to exchange loopback IPs as tunnel endpoints

**Resulting IPsec Template Parameters**

**Hub Side (Dial-up Server)**

- Tunnel type: Dynamic
- net-device: disable

**Branch Side**

- Tunnel type: Static (remote IP known via exchanged loopback)
- net-device: enable

These settings are the recommended IPsec configuration when BGP routing runs over loopback interfaces instead of directly over the overlay tunnels.

---
![[19.png]]
## IBGP Configuration for Spokes with BGP Per Overlay

#### Key Characteristics
- Uses **IBGP** (local AS = remote AS = 65000)
- Preferred over EBGP because IBGP preserves the original next-hop, which is essential for proper ADVPN operation
- Supports SD-WAN ECMP; requires `set ibgp-multipath enable` to allow equal-cost multipath for IBGP routes
#### Neighbor Configuration
- Each `config neighbor` entry defines one hub overlay IP:
  - e.g., 192.168.1.61 → Hub overlay over ISP1
  - e.g., 192.168.1.125 → Hub overlay over ISP2
- `interface` setting binds BGP session to the specific SD-WAN overlay interface
- 🎁 `update-source` explicitly sets the source IP (optional/redundant here because `interface` already forces the correct source IP, but recommended for consistency)
#### Purpose of Binding
- Prevents accidental cross-overlay BGP peerings (e.g., ISP1 overlay talking to ISP2 overlay)
- Ensures BGP control plane follows the same SD-WAN path selection as data plane
#### Route Redistribution
- `config redistribute "connected"` injects the local LAN subnet (e.g., 10.0.1.0/24 on port5) into BGP so it is advertised to hub and other spokes
---
![[20.png]]
## BGP Configuration on Hub with Per-Overlay BGP

**Key Purpose**  
- Configures BGP on the hub for spokes acting as dial-up clients over multiple overlays/underlays.

**Neighbor-Groups**  
- Used to group spokes sharing the same underlay (e.g., same ISP).  
- Hub always initiates BGP peerings from spokes (expected dial-up behavior).  
- Common settings from the neighbor-group are automatically applied to each dynamic spoke peering.  
- Optional settings (rarely used on hub by overlay orchestrator):  
  - `interface` – binds BGP packets to a specific interface  
  - `update-source` – specifies source IP for BGP packets  
- On spokes, these settings are mandatory to prevent cross-overlay BGP peerings.

**Route Reflector Settings**  
- Enable `route-reflector-client` in neighbor-group so spokes can learn each other’s prefixes.  
- Enable route reflector on the hub only if ADVPN is planned.

**Neighbor-Range**  
- Defines the IP address range of expected spoke BGP peers per neighbor-group.  
- Helps contain route propagation within the same underlay/ISP when route reflection is active.

**Route Advertisement**  
- `redistribute connected` injects connected subnets into BGP using a defined route-map.  
- Alternative: use `config network` to manually advertise specific IGP prefixes (connected, static, OSPF, RIP, etc.) into BGP.  

In summary, the configuration leverages neighbor-groups and neighbor-ranges to efficiently manage dynamic spoke peerings per overlay while providing control and scalability for route reflection and ADVPN scenarios.

---
![[21.png]]
## BGP on Loopback Design – Key Summary
#### Core Concept
- Uses unique loopback IP addresses to identify each SD-WAN node in the overlay.
- Loopback serves as BGP peering address, ADVPN shortcut monitoring, etc.
- ⚠️ No tunnel IP addresses are configured → IKE Mode Config is **not used**.
#### Design Requirements
- ⚠️ Allocate one summary subnet (loopback summary) that covers all loopback addresses.
- Hub advertises this loopback summary route to all spokes.
#### Routing Mechanism
- Relies on Fortinet proprietary BGP extension: **tag-based recursive resolution** (tag-match).
#### How Tag-Based Recursive Resolution Works
- Spokes tag inbound BGP routes from each hub using **route-map-in** (e.g., tag 1 for routes from Hub1).
- During recursive next-hop resolution, the FortiGate only considers routes that carry the **same tag** as the route being resolved.
- ⚠️ Routes learned from other hubs or by other protocols are ignored for resolution, even if they are administratively better.
#### Resolution Modes
- **Preferred mode**: Uses only tag-matched routes.
- 🎁 **Merge mode** (used in BGP-on-loopback SD-WAN topologies): Combines tag-match results with standard best-match resolution.
#### Important Characteristics
- Tag-based resolution is **locally significant only** on the resolving FortiGate.
- No BGP capability negotiation; tags and resolution outcomes are **not advertised** to peers.
- All BGP peering and messaging remain fully standards-compliant.
خلاصهٔ متن به زبان فارسی:

در طراحی **BGP روی Loopback**، هر گرهٔ SD-WAN با یک آدرس Loopback منحصربه‌فرد شناسایی می‌شود. این آدرس برای **BGP Peering**، **نظارت بر میان‌بُرهای ADVPN** و سایر عملکردها استفاده می‌شود. در مرحلهٔ طراحی، یک **زیرشبکهٔ واحد** برای همهٔ آدرس‌های Loopback تعریف و توسط هاب‌ها به سخو (Spoke)‌ها تبلیغ می‌شود. در این مدل نیازی به آدرس‌دهی Tunnel یا استفاده از **IKE Mode-Config** نیست.

مسیر‌یابی در این معماری از یک قابلیت اختصاصی Fortinet استفاده می‌کند به نام **Tag-based Recursive Resolution**. این روش به دستگاه‌ها اجازه می‌دهد به مسیرهای BGP دریافتی یک **Tag** اختصاص دهند و هنگام حلّ مسیر، فقط مسیرهایی را در نظر بگیرند که همان Tag را دارند.

نحوهٔ کار:

- سخو‌ها برای مسیرهای دریافت‌شده از هر هاب، با استفاده از Route-map یک Tag مشخص می‌گذارند (مثلاً HUB1_TAG = 1).
    
- سپس دستگاه تلاش می‌کند مسیرهای دارای این Tag را فقط با مسیرهایی که از همان هاب یاد گرفته شده‌اند حل کند؛ مسیرهای هاب دیگر یا مسیرهای «بهترین تطابق» خارج از همین Tag وارد روند حلّ نمی‌شوند.
    

دو حالت وجود دارد: **Preferred** (فقط براساس Tag) و **Merge** (ترکیب Tag و بهترین‌تطابق). در SD-WAN معمولاً حالت **Merge** استفاده می‌شود.

این مکانیزم فقط روی دستگاه اثر دارد و **با همتاهای BGP مذاکره نمی‌شود**؛ یعنی Tagها یا نتایج آن به سایر همتاها ارسال نمی‌شود و ارتباط همچنان کاملاً مطابق استاندارد BGP باقی می‌ماند.

---
![[22.png]]
## BGP on Loopback Key Points

- **Recursive Next-Hop Resolution**  
  - By default, FortiGate BGP does not consider routes when the next-hop is resolved recursively.  
  - For BGP on loopback to work (hub and spokes), you **must enable** `recursive-next-hop`.  
  - This parameter allows one level of recursive route resolution.  
  - Must be enabled on **both hub and all spokes**.

- **Neighbor-Group Configuration**  
  - Standard “BGP per overlay” design → one neighbor-group per overlay pointing to the hub.  
  - BGP on loopback design → requires **only one neighbor-group** (named **EDGE** by FortiManager overlay orchestrator) for **all spokes**, regardless of the number of overlays.

- **Hub Configuration (FortiManager Orchestrated)**  
  - Uses Fortinet-recommended BGP parameters.  
  - Automatically enables **ibgp-multipath** and **ebgp-multipath**.  
  - Prepares the hub for multi-region topologies with load-balancing capabilities.

In short: BGP on loopback simplifies spoke-to-hub neighborship but requires `recursive-next-hop` everywhere and a single “EDGE” neighbor-group, while the hub gets multipath enabled for scalability.

به طور پیش‌فرض، روت‌های BGP وقتی که next-hop آن‌ها نیاز به resolution بازگشتی (recursive) داشته باشد، در نظر گرفته نمی‌شوند. چون در طراحی BGP on Loopback، هاب و اسپوک‌ها باید این نوع روت‌ها را بپذیرند، باید پارامتر `recursive-next-hop` را فعال کنید. این پارامتر به FortiGate اجازه می‌دهد تا یک سطح resolution بازگشتی را انجام دهد. این تنظیم باید هم روی هاب و هم روی تمام اسپوک‌ها فعال شود.

در حالت معمولی «BGP per overlay» برای هر اورلی یک neighbor-group جداگانه تعریف می‌کردید، اما در طراحی BGP on Loopback فقط به یک neighbor-group واحد برای تمام اسپوک‌ها (صرف‌نظر از تعداد اورلی‌ها) نیاز دارید. در اورکستریتور FortiManager این گروه به طور خودکار با نام EDGE ساخته می‌شود.

نمونه کانفیگی که در اسلاید نشان داده شده، توسط FortiManager overlay orchestrator آماده شده و شامل تنظیمات پیشنهادی فورتی‌نت است. همچنین پارامترهای `ibgp-multipath` و `ebgp-multipath` در آن فعال شده تا هاب برای توپولوژی‌های چندمنطقه‌ای (multi-region) آماده باشد.

---
![[23.png]]
## BGP Route Reflector in FortiGate SD-WAN with ADVPN
#### Default IBGP Behavior
- By default, IBGP routers do **not** advertise routes learned from one internal neighbor to another internal neighbor (no route propagation between peers).
#### Route Reflector Role
- Configuring `route-reflector-client` on hub neighbor groups turns the FortiGate hub into a **BGP route reflector**.
- This allows the hub to **reflect** routes learned from one IBGP client (spoke) to other IBGP clients (other spokes).
- Required when ADVPN is enabled so spokes can learn each other’s prefixes directly via the hub.
#### How Reflection Works in the Example
- Spoke1 advertises 10.0.1.0/24 to the hub over two overlays (HUB1-VPN1 and HUB1-VPN2).
- Hub learns the prefix with two different next hops:
  - 192.168.1.1 (via HUB1-VPN1)
  - 192.168.1.65 (via HUB1-VPN2)
- Hub reflects the prefix to Spoke2, preserving the original next hop (IBGP next-hop-unchanged behavior).
#### Spoke Behavior on Receiving Reflected Routes
- Spoke2 performs a **recursive route lookup** using the preserved next hop.
- Lookup matches static routes for overlay subnets → outgoing interface becomes HUB1-VPN1 (or HUB1-VPN2).
- Routing table on Spoke2 shows **[2]** duplicate entries for 10.0.1.0/24 (one per path), visible in FIB but summarized in RIB.
#### Additional-Path Limitation
- By default (`set additional-path disable`), the hub reflects **only one path** (here, the one with next hop 192.168.1.1).
- The second path (next hop 192.168.1.65) is **not** reflected unless additional-path is explicitly enabled.
---
![[24.png]]
## BGP Path Advertisement in SD-WAN Overlays
#### Default Behavior
- FortiGate as BGP speaker advertises **only one path per prefix** by default.
- In hub-and-spoke with hub as route reflector, the hub reflects **only one path** per prefix to spokes, even if it learns multiple paths from a spoke client.
#### SD-WAN Requirement
- SD-WAN needs **all available paths** to all destinations on every site.
- Reason: Missing routes in the FIB can cause SD-WAN rules and members to be skipped.
#### Recommended Best Practice
- 🎁 Configure FortiGate to advertise **additional paths** so peers learn all available paths.
- Specifically on the **hub**: advertise **one additional path per overlay** (add-paths send).
#### Example Scenario (10.0.1.0/24 from spoke2)
- Hub learns two paths:
  - Via HUB1-VPN1 → next-hop 192.168.1.1
  - Via HUB1-VPN2 → next-hop 192.168.1.65
- Hub is configured to:
  - Send two additional paths
  - Reflect prefixes learned from clients (with add-paths)
- Spoke is configured to receive additional paths (add-paths receive).
#### Result on Spoke
- Spoke receives **both paths** from the hub via each overlay.
- Total: **four routes** for 10.0.1.0/24 (two paths × two overlays).
- Because iBGP preserves next-hop, the routing table on the spoke shows **duplicate routes** for the prefix (expected and normal).
---
![[25.png]]