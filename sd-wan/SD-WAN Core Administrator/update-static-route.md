The key concept you are asking about, `update-static-route`, is a critical feature within the FortiGate Secure SD-WAN solution that deals with how the device handles routing when the quality or state of a network connection changes.

Here is an explanation of the key concepts of `update-static-route` using simple words and examples, based on the provided sources:

### Key Concepts of `update-static-route`

In simple terms, `update-static-route` is an action that the FortiGate firewall automatically takes when it detects that an SD-WAN member (a physical or virtual link, like an internet connection or an IPsec tunnel) has gone **dead** or has become **alive** again.

Its main job is to ensure that network traffic avoids using a broken or unhealthy path.

#### 1. What it is and why it's used

- **Location:** This feature is found under the "Action When Inactive" settings for a Performance SLA (Service Level Agreement), which is how FortiGate monitors the health and performance of network links.
- **Default State:** This option is **enabled by default** for each Performance SLA.
- **Purpose:** The goal is to dynamically disable static routes associated with a faulty link so that traffic is automatically routed through an alternative, healthy link. This change applies to _all_ traffic flows, even those not explicitly controlled by SD-WAN rules.

#### 2. How it works (The Mechanism)

The FortiGate tracks the static routes that are configured to use a specific link's gateway.

- **When a Link Goes Dead (Inactive State):**
    
    - If the Performance SLA determines that an SD-WAN member is "dead" (due to failed probes or degraded performance).
    - FortiGate **disables the static routes** that match that member's gateway.
    - These static routes become **inactive** and are therefore **not installed in the main routing table** (Forwarding Information Base or FIB). You can still view them in the routing table database, but they won't be used for forwarding traffic.

- **When a Link Comes Alive (Active State):**
    
    - If the member is determined to be "alive" again.
    - The associated static routes become **active**.
    - They are re-installed in the routing table, making them available to route traffic if they are the best routes for a destination.

#### 3. Important Caution (The Caveat)

Administrators must be **careful** because this feature, while helpful for failover, can deactivate routes that are still needed for non-SD-WAN traffic.

- **Impact on Overlay Tunnels (IPsec):** If you have an overlay tunnel (like an IPsec VPN) that relies on a static route to connect to its remote peer over the unhealthy link, disabling that static route could impact the tunnel.
    - **Example:** A branch office FortiGate uses `update-static-route`. The main internet line (port1) goes down. If an IPsec tunnel uses a static route pointing out port1 to reach the head office, that route is disabled. The FortiGate will then try to establish a new tunnel through a different link (like port2), if configured to do so.

### Example Scenario

Imagine a small office (Site A) with two internet connections, **ISP1 (Port 1)** and **ISP2 (Port 2)**, both configured as SD-WAN members in the `underlay` zone. The administrator has a default static route configured that references the `underlay` zone, meaning both ISP links have equal-cost routes to the internet.

1. **Healthy State:** Both ISP1 and ISP2 are "alive". The default static route through ISP1's gateway and the static route through ISP2's gateway are both **active** in the routing table. Traffic is steered based on SD-WAN rules or load-balanced by the implicit rule.
2. **Failure State (ISP1 Dies):** The Performance SLA detects that ISP1 (Port 1) is "dead".
3. **`update-static-route` Action:** Because `update-static-route` is enabled, FortiGate **disables** the static route associated with ISP1's gateway (e.g., 192.2.0.2). This route is removed from the active routing table, meaning no traffic attempts to use the dead ISP1 link.
4. **Traffic Rerouting:** All traffic automatically switches to use the remaining **active** route (ISP2/Port 2).
5. **Recovery State (ISP1 Comes Back):** The Performance SLA detects that ISP1 is "alive" again.
6. **`update-static-route` Action:** The static route for ISP1 is made **active** again and re-installed in the routing table, allowing FortiGate to use it for traffic steering.

This feature is like a traffic cop automatically turning off the lanes and traffic lights leading to a blocked highway exit, forcing all cars (traffic) to use the remaining open routes immediately, rather than waiting for a specific navigation app (SD-WAN rule) to steer them.