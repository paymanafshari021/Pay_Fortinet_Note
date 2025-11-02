```bash
config system snmp sysinfo
    set status enable
end
config system snmp user
    edit "<username>"
        set security-level auth-priv
        set auth-proto sha
        set auth-pwd "<auth_password>"
        set priv-proto aes
        set priv-pwd "<priv_password>"
        set queries enable
        set query-port 161
        set trap-status enable
        set trap-lport 162
        set trap-rport 162
        set notify-hosts "<SNMP_manager_IP>"
    next
end
```