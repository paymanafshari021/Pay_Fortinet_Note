# HA 
#ha #Fortinet #Debug 

![Alt text](HA_Override_en.png)

Force a failover
```
diagnose sys ha reset-uptime
```
Check the HA uptime difference
```
diag sys ha dump-by vcluster
```
![Alt text](HA_Override_dis.png)
Shows HA mode, roles, sync status, and failover reasons.
```
get system ha statu
```
Recalculates checksums to fix false sync errors.
```
diagnose sys ha checksum recalculate
```
Forces configuration sync from primary to secondary.
```
execute ha synchronize start
```
Connects to secondary unit CLI (use `execute ha manage ?` for IDs).
```
execute ha manage <id> <username>
```