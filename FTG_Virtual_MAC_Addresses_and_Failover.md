# Virtual MAC Addresses and Failover

This explains what happens at the **network layer during an HA failover event** — specifically how the new primary unit announces itself to the surrounding network using **gratuitous ARP**, and what can go wrong when switches don't update their MAC tables fast enough. It also introduces the `set link-failed-signal` command as the solution for stubborn switches.

## Concept: Virtual MAC Addresses in HA

Before understanding failover behaviour, you need to know what a **virtual MAC address** is in the HA context.

In a FortiGate HA cluster, all cluster interfaces share **virtual MAC addresses** — these are synthetic MAC addresses that are the same on both the primary and subordinate units. The virtual MAC is what the rest of the network knows and uses in its ARP tables and switch MAC forwarding tables.

The key design goal is: **when a failover happens, the MAC address seen by the network stays the same**. Only the physical switch port through which that MAC is reachable changes — because the new primary is connected to a different port. The network devices (servers, routers, other switches) keep their ARP entries unchanged, because the MAC-to-IP mapping hasn't changed — only the switch port that leads to that MAC has moved.

## Gratuitous ARP After Failover

After a failover event (the former primary fails, the subordinate takes over):

**The new primary immediately broadcasts Gratuitous ARP (GARP) packets.**

### What is a Gratuitous ARP?
A Gratuitous ARP is an unsolicited ARP reply that a device sends to announce its MAC-to-IP mapping to all devices on the network segment — without anyone having asked. It is "gratuitous" because it is unrequested.

### What does the GARP accomplish here?
It tells every switch and host on the network: *"The virtual MAC address X is now reachable through this switch port (the new primary's port), not the old one."*

**Effect on switches:** Switches maintain a **MAC forwarding table** (also called the CAM table) that maps MAC addresses to physical ports. When the GARP arrives on the new primary's port, the switch updates its table: virtual MAC address X → new port. From that point on, traffic destined for the virtual MAC is forwarded out the correct port to the new primary.

**Effect on hosts/routers:** Because the virtual MAC did not change, hosts and routers do not need to update their ARP caches at all. Their ARP entry still says: IP address Y → MAC address X. Only the switch needed to update where port MAC X is physically located.

## The Problem — Slow Switches
> **"Some switches might not clear their MAC tables fast enough, so they would keep sending packets to the former primary device."**

### Why does this happen?
Some enterprise switches — particularly high-end chassis switches with hardware-forwarding ASICs — may not immediately process the GARP update. Their MAC table entries can have aging timers or hardware pipeline delays. During this window, they continue forwarding traffic to the **former primary's physical port**, which is now a failed or subordinate device. This results in **traffic blackholing** — packets are sent to a device that is no longer active, and they are dropped.

This is especially problematic for **time-sensitive or stateful traffic** where even a few seconds of misdirected traffic causes noticeable disruption.

## The Solution — `set link-failed-signal`

To solve the stubborn switch problem, FortiGate provides the following mechanism:

### What it does:
When `link-failed-signal` is **enabled**, the **former primary FortiGate shuts down all of its data interfaces for one second** immediately when the failover occurs. Heartbeat interfaces and reserved management interfaces are excluded — only data-forwarding interfaces are shut down.

### Why shutting down interfaces helps:
When a physical interface goes down, all connected switches **immediately detect a link-down event (physical Layer 1 signal loss)**. This forces the switch to:
1. Instantly invalidate all MAC table entries learned from that port
2. Re-learn MAC addresses when the link comes back up (one second later)

This is far faster and more reliable than waiting for GARP packets to be processed, because **Layer 1 link state changes are handled in hardware** with no software processing delay on the switch side.

### The one-second shutdown is specifically designed to be:
- Long enough for all connected switches to detect the link failure and flush their MAC tables
- Short enough that it causes minimal disruption — the new primary is already active and handling traffic through its own interfaces during this one second

### CLI Code — Line by Line:

```
config system ha
```
→ Enters the HA system configuration context

```
        set link-failed-signal enable | disable
```
→ Controls the link-failed-signal feature:
- **`enable`** → activates the one-second interface shutdown on the former primary during failover, forcing switches to clear their MAC tables
- **`disable`** → the former primary does NOT shut down its interfaces; only GARP is used to notify the network (this is the default)

The **red box around `disable`** with the label **"Default value"** tells you that `link-failed-signal` is **disabled by default**. You must explicitly enable it if you need this behaviour.

```
end
```
→ Saves and exits the `config system ha` context

### Final result:
> **"Because of the link outage, all switches detect the failure and clear their MAC tables"**

All switches connected to the former primary's data interfaces see a physical link-down event, immediately flush any MAC table entries associated with those ports, and re-learn the correct forwarding path when the link comes back up — this time pointing to the new primary through its correct port.

## Complete Failover Sequence with `link-failed-signal enable`

| Step | What Happens |
|---|---|
| 1 | Former primary fails (hardware fault, monitored interface down, etc.) |
| 2 | Subordinate unit wins election → becomes new primary |
| 3 | New primary sends **Gratuitous ARP** packets → updates most switches immediately |
| 4 | Former primary shuts down all data interfaces for **1 second** (link-failed-signal) |
| 5 | All connected switches detect **link-down** → immediately flush MAC table entries for those ports |
| 6 | Former primary's interfaces come back up after 1 second |
| 7 | Switches re-learn MAC addresses → now point to new primary's ports |
| 8 | Traffic flows correctly through the new primary |

## Without `link-failed-signal enable` (Default Behaviour)

| Step | What Happens |
|---|---|
| 1 | Former primary fails |
| 2 | Subordinate → new primary |
| 3 | New primary sends GARP → most switches update correctly |
| 4 | Some high-end switches **ignore or delay processing** the GARP |
| 5 | Those switches **continue sending traffic to the former primary's port** |
| 6 | Traffic is blackholed until the switch's MAC aging timer expires |