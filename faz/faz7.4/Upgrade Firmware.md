# Firmware best practices

- Stay current on patch releases for your current major release.
- Only upgrade to a new major release or version when you are looking for specific functionality in the new major release or version. For more information, see the _[FortiAnalyzer Release Notes](https://docs.fortinet.com/document/fortianalyzer/7.4.8/release-notes/723553/)_ on the Fortinet Document Library ([https://docs.fortinet.com/](https://docs.fortinet.com/)), or contact Fortinet Customer Service & Support ([https://support.fortinet.com/](https://support.fortinet.com/)).
- Upgrade FortiAnalyzer before upgrading FortiOS, and be sure to maintain release version compatibility at all times.
# Preparing to Upgrade FortiAnalyzer

We recommend performing the following tasks to prepare for a successful upgrade of a FortiAnalyzer unit. Following is a summary of the preparation tasks and a link to the details for each task.

###### To prepare for upgrading FortiAnalyzer (summary):

1. Download release notes, firmware images, and SNMP MIB files. See [Downloading files from Customer Service & Support](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/281540/downloading-files-from-customer-service-support).
2. Review release notes. See [Reviewing FortiAnalyzer 7.4.8 Release Notes](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/344239/reviewing-fortianalyzer-7-4-8-release-notes).
3. Plan when to perform the upgrade. See [Planning when to upgrade](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/442146/planning-when-to-upgrade).
4. Review the status of managed devices. See [Reviewing status of managed devices](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/448110/reviewing-status-of-managed-devices).
5. Review FortiAnalyzer system resources and license information. See [Reviewing FortiAnalyzer system resources and license information](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/273689/reviewing-fortianalyzer-system-resources-and-license-information).
6. Back up configuration files and databases. See [Backing up configuration files and databases](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/621448/backing-up-configuration-files-and-databases).
7. Back up logs. See [Backing up logs](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/170398/backing-up-logs).
8. Check reports. See [Checking reports](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/669300/checking-reports)
9. Clone VM instances. See [Creating a snapshot of VM instances](https://docs.fortinet.com/document/fortianalyzer/7.4.8/upgrade-guide/833804/creating-a-snapshot-of-vm-instances).

# Planning when to upgrade

Plan a maintenance window to complete the firmware upgrade. If possible, you may want to set up a test environment to ensure that the upgrade does not negatively impact your network or managed devices.

# Reviewing status of managed devices

Before starting an upgrade, use the _Device Manager_ pane to review the status of all logging devices to ensure 0 devices have _Status_ = _Down_.
Either correct devices that are _Down_ or make note of them prior to starting the upgrade.
You can use the following CLI commands to review the status of managed devices. Use this command to check that device and ADOM disk quota are correct before and after the upgrade.
- `diagnose log device`

# Reviewing FortiAnalyzer system resources and license information

Before starting an upgrade, go to _System Settings_ to review the following widgets:
- License Information widget
- System Resources widget to check for high memory and CPU usage
It is also recommended to check the Alert Message Console widget in _System Settings_ and the notifications in the toolbar.
###### If you are upgrading a FortiAnalyzer VM:
Make sure your VM partition has more than 1024MB and your VM server is up to date.

# Backing up configuration files and databases

Back up the FortiAnalyzer configuration file and databases.
It is recommended that you create a system backup file and save this configuration to your local computer. The device configuration file is saved with a `.dat` extension.
It is also recommended that you verify the integrity of your backup file.

# Backing up logs

It is recommended to back up logs before an upgrade.
`execute backup logs <device name(s)> {ftp | scp | sftp} <ip> <username> <passwd> <directory> [vdlist]`
It is also recommended to send logs to a temporary FortiAnalyzer or syslog server during the upgrade.

# Checking reports

Wait until all the running reports are completed, and ensure that no reports are scheduled to run during the upgrade.
You can use the following CLI commands to check for running and pending reports:
`FAZ1000D # dia report status running`
`FAZ1000D # dia report status pending`
