#Agentless_Polling_Mode #FSSO #Debug #Agentless #Polling #Mode
# Agentless Polling Mode

```
diagnose debug fsso-polling detail

AD Server Status:
ID=1, name(10.0.1.10),ip=10.0.1.10,source(security),users(0)
port=auto username=administrator
read log offset=251636, latest logon timestamp: Wed Feb 1 09:47:31 2023
polling frequency: every 10 second(s) success(246), fail(0)
LDAP query: success(0), fail(0)
LDAP max group query period(seconds): 0
most recent connection status: connected
```

```
diagnose debug fsso-polling refresh-user

refresh completes. All logon users are obsolete. Please re-logon to make them available.
```

```bash
diagnose sniffer packet any 'host 10.124.2.18 and tcp port 445'
```

```bash
diagnose debug application fssod -1
diagnose debug application smbcd -1
diagnose debug enable

[fsso_ldap_group_add:427] logon: 10.0.100.58, aduser1/TRAINING, , add group
CN=aduser1,CN=Users,DC=training,DC=lab
```
##### Common errors:

+ FortiGate cannot resolve the active directory server name:
```
failed to resolve server(<server name>)
```
+ FortiGate and AD system times must be in sync so that statistics can be calculated correctly:
```
Please sync the time of Fortigate and AD server
```
+ Check FortiGate memory usage:
```
No memory alloc
```


