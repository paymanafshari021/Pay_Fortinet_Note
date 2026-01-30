In workspace mode, a device must be locked before changes can be made to it. Other administrators will be unable to make changes to that device until you unlock it, log out of the FortiManager, or they forcibly disconnect you when they are locking the ADOM that the device is in.

Individual device locks will be removed if you lock the ADOM that the device is in.
###### To lock a device:

1. Ensure you are in the correct ADOM.
2. Go to _Device Manager > Device & Groups_.
3. In the device list, right-click on the device and select _Lock_. A padlock icon in the locked state is shown next to the device name to indicate that the device is locked.

Other administrators are now unable to make changes to the device, and cannot lock the ADOM without first forcing you to disconnect.

⚠️ Individual devices cannot be locked if ADOMs are in advanced mode ([ADOM device modes](https://docs.fortinet.com/document/fortimanager/7.4.8/administration-guide/066530/adom-device-modes)).

###### To unlock a device:

1. Ensure you are in the correct ADOM.
2. Go to _Device Manager > Device & Groups_.
3. Ensure that you have saved any changes by clicking _Save_ in the toolbar.
4. In the device list, right-click on the locked device and select _Unlock_. If you have not saved your changes, a confirmation dialog box will give you the option to save or discard your changes.

⚠️ After unlocking, the padlock icon next to the device name is removed, and the device is unlocked. The device will also be unlocked when you log out of the FortiManager.

All devices are unlocked when you log out of the FortiManager. If you have unsaved changes, a confirmation dialog box will give you the option to save or discard them.
