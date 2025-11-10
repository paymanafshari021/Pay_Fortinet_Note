# Increasing Disk Space

With FortiAnalyzer VMs, you can dynamically add more disk space:

1. Power off the VM and add a new virtual disk
2. Start the VM and run `execute lvm info` to confirm the new disk was detected The new disk is labeled as Unused
3. Run `execute lvm extend`
	+ This command will add the space of the additional disk to the lvm volume
4. Reboot the VM, and then run the command `get system status` to see the new disk space available

With hardware FortiAnalyzer, you must add one or more disks to the device
+ If you are using RAID, this requires you to rebuild your RAID array

Be sure to account for future growth and size correctly from the outset!