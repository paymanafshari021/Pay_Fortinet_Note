*  FortiGate devices will act as intelligent SD-WAN nodes that are interconnected by an overlay network to provide secure connectivity across all sites, cloud services, and public Internet and to always select an optimal path for each application
* Unacceptable paths are sometimes called out of SLA
* We can configure an SD-WAN rule matching our business-critical applications and preferring "wan1" over "wan2", but only as long as it meets the required SLA target (for example, 200 ms latency). If the health of "wan1" degrades, and it can no longer meet the target, the sessions will be steered to "wan2". All the existing sessions will (by default) switchover. Still, the health of "wan1" will be constantly probed. Once the Internet connection "wan1" recovers, the application sessions will switchover back to "wan1", freeing up the backup link "wan2" and always meeting their SLA targets.
* Auto-Discovery VPN (ADVPN), which is our dynamic tunneling technology, is enabled in our Hub-and-Spoke topology. ADVPN dynamically builds direct Spoke-to-Spoke tunnels (also known as shortcuts) when they are needed, and tears down the tunnels when no longer in use. It maintains a Zero-Touch property of a Hub-and-Spoke and helps avoid the complexity of a Full-Mesh, while preserving the benefits of direct site-to-site communication without bottlenecks.
	* Deployment workflow
# ZTP using FortiCloud and FortiDeploy

![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/0466f009-7301-11f0-a9d0-d2b0d2e22f7d/images/606234a0329a55832cea6de47d1f0ca4_fmg_fig03.png)

Using FortiCloud and FortiDeploy is the classic _call-home_ onboarding method:

- A new FortiGate device with a factory-default configuration is plugged in and powered on.  
- The FortiGate boots up and obtains Internet connectivity from a DHCP server.
- The FortiGate automatically contacts FortiCloud (in other words, the FortiGate _calls home_), authenticates using its own serial number, and obtains the location of FortiManager (based on the device registration data in FortiCloud).
- Finally, the FortiGate connects to FortiManager, authenticates using its own serial number, and triggers the Auto-Link process.
As can be seen, this method requires neither initial configuration nor any human intervention, apart from connecting the device to a power socket and to Internet access equipment (for example, an ISP modem).