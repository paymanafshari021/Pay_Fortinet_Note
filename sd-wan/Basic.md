+ SD-WAN is Session based
+ point of presence (PoP): It’s a **gateway** that brings internet or cloud services **closer to users** for faster and more reliable connections.
+ **Cloud on-ramp** means a **dedicated, high-performance connection** between your company’s network and a **cloud provider** (like AWS, Azure, or Google Cloud).
+ Visibility of application detection criteria is, by default, hidden on the FortiGate GUI. Because it is an advanced parameter that requires the administrator to understand its specifics, you must enable the feature visibility on the CLI using the global command `set gui-app-detection-sdwan enable`. Note that if GUI visibility is disabled, configuration for application criteria remains active and configuration is still visible on the CLI.
+ Migrating Interfaces to SD-WAN
	+ ![Alt text](attachments/Pasted_image_20251101114330.png)
	+ ![Alt text](attachments/Pasted_image_20251101114444.png)
	+ Delete Entry: Delete the Firewall Policy
+ When `update-static-route` is enabled, FortiGate disables the static routes for inactive interfaces. 
+ When you enable `update-static-route`, you must carefully consider the consequence on overlay tunnels.

# tshoot
https://docs.fortinet.com/document/fortigate/7.6.4/administration-guide/665425/troubleshooting-sd-wan
