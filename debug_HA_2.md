
![[Pasted image 20251018224148.png]]
Force a failover
```
diagnose sys ha reset-uptime
```
Check the HA uptime difference
```
diag sys ha dump-by vcluster
```
![[Pasted image 20251018224430.png]]



![[Pasted image 20251018225201.png]]
Shows HA mode, roles, sync status, and failover reasons.
```
get system ha statu
```

```
diagnose sys ha checksum recalculate
```

Recalculates checksums to fix false sync errors.

```
execute ha synchronize start
```

Forces configuration sync from primary to secondary.

```
execute ha manage <id> <username>
```

Connects to secondary unit CLI (use `execute ha manage ?` for IDs).

```
diagnose sys ha reset-uptime
```

Resets HA uptime to trigger failover.