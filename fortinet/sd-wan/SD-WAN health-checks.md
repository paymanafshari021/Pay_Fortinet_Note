===Fortigate SDWAN health-checks, each one ===  
set sla-fail-log-period 10  
set sla-pass-log-period 30

=== Fortimanager add history ===  
config system admin setting  
set sdwan-monitor-history enable  
end