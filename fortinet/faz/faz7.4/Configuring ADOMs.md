# Configuring ADOMs

To use administrative domains, the `admin` administrator must first enable the feature, create ADOMs, and assign existing FortiAnalyzer administrators to ADOMs.

> [!CAUTION] 
> Enabling ADOMs moves non-global configuration items to the `root` ADOM. Back up the FortiAnalyzer unit configuration before enabling ADOMs.

Within the CLI, you can enable ADOMs and set the administrator ADOM. To configure the ADOMs, you must use the GUI.
###### To enable or disable ADOMs:
Enter the following CLI command:
```
config system global
set adom-status {enable | disable}
end
```
An administrative domain has two modes: normal and advanced. Normal mode is the default device mode. In normal mode, a FortiGate unit can only be added to a single administrative domain. In advanced mode, you can assign different VDOMs from the same FortiGate to multiple administrative domains.
> [!CAUTION] 
> Enabling the advanced mode option will result in more complicated management scenarios. It is recommended only for advanced users.
###### To change ADOM device modes:
Enter the following CLI command:
```bash
config system global
set adom-mode {advanced | normal}
end
```
###### To assign an administrator to an ADOM:
Enter the following CLI command:
```bash
config system admin user
edit <name>
set adom <adom_name>
next
end
```
where `<name>` is the administrator user name and `<adom_name>` is the ADOM name.