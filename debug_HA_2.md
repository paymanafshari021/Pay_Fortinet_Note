```
get system ha statu
```

Shows HA mode, roles, sync status, and failover reasons.

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