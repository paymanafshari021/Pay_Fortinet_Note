# HA 
#ha #Fortinet #Debug 
```
get system ha statu
diagnose sys ha reset-uptime
diagnose sys ha checksum recalculate
execute ha failover set 1
execute ha failover unset 1
execute ha manage <id> <username>
```
## Primary FortiGate Election - Override Enabled

```
config system ha
	set override enable
end
```
+ Connected monitored ports
+ Priority
+ HA uptime
+ Serial number
### Force a failover
```
diagnose sys ha reset-uptime
```
### Check the HA uptime difference
```
diag sys ha dump-by vcluster
```

<<<<<<< HEAD
## Primary FortiGate Election - Override Disabled
```
config system ha
	set override enable
end
```
+ Connected monitored ports
+ HA uptime
+ Priority
+ Serial number
### Shows HA mode, roles, sync status, and failover reasons.
=======
![Alt text](attachments/HA_Override_dis.png)
Shows HA mode, roles, sync status, and failover reasons.
>>>>>>> 0d3641a715c7a6da1e47f46f3e23562207e76ffe
```
get system ha statu
```
### Recalculates checksums to fix false sync errors.
```
diagnose sys ha checksum recalculate
```
### Forces configuration sync from primary to secondary.
```
execute ha synchronize start
```
### Connects to secondary unit CLI (use `execute ha manage ?` for IDs).
```
execute ha manage <id> <username>
```