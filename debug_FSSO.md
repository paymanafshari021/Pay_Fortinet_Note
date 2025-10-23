#Agentless_Polling_Mode #FSSO #Debug 
# Agentless_Polling_Mode

```bash
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

```bash
diagnose debug fsso-polling refresh-user

refresh completes. All logon users are obsolete. Please re-logon to make them available.
```
```bash
diagnose sniffer packet any 'host 10.124.2.18 and tcp port 445'
```
