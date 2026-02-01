ADOM deployment can have two device modes: _Normal_ (default) and _Advanced_.
- In _Normal_ mode, you cannot assign different FortiGate VDOMs to different ADOMs. The FortiGate unit can only be added to a single ADOM.
- In _Advanced_ mode, you can assign a VDOM from a single device to a different ADOM. This allows you to analyze data for individual VDOMs, but will result in more complicated management scenarios. It is recommended only for advanced users.

ℹ️ FortiManager does not support splitting FortiGate VDOMs between multiple ADOMs in different ADOM modes (normal/backup).

To change from _Advanced_ mode back to _Normal_ mode, you must ensure no FortiGate VDOMs are assigned to an ADOM.

###### To change the ADOM device mode:

1. Go to _System Settings > Advanced > Advanced Settings_.
2. In the ADOM Mode field, select either _Normal_ or _Advanced_.
3. Select _Apply_ to apply your changes.


ℹ️ While in _Workspace_ mode with _Advanced_ ADOM mode enabled, changes made to a managed device's database in the _Device Manager_ are automatically saved and applied, and the _Save_ button is not selectable.