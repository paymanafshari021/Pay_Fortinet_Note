# ZTP using FortiCloud and FortiDeploy

![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/0466f009-7301-11f0-a9d0-d2b0d2e22f7d/images/606234a0329a55832cea6de47d1f0ca4_fmg_fig03.png)

Using FortiCloud and FortiDeploy is the classic _call-home_ onboarding method:

- A new FortiGate device with a factory-default configuration is plugged in and powered on.  
- The FortiGate boots up and obtains Internet connectivity from a DHCP server.
- The FortiGate automatically contacts FortiCloud (in other words, the FortiGate _calls home_), authenticates using its own serial number, and obtains the location of FortiManager (based on the device registration data in FortiCloud).
- Finally, the FortiGate connects to FortiManager, authenticates using its own serial number, and triggers the Auto-Link process.
As can be seen, this method requires neither initial configuration nor any human intervention, apart from connecting the device to a power socket and to Internet access equipment (for example, an ISP modem).