# Debug DHCP-Relay  
  

DHCP Relay Debug

_diag debug reset_  
_diag debug enable_  
_diag debug console timestamp enable_  
_diag debug application dhcprelay -1_

  
Packet Sniffing

_diag sniffer packet any 'port 67 or port 68' 4 0 a_

  
Flow Debugging

_diag debug disable_  
_diag debug flow trace stop_  
_diag debug flow filter clear_  
_diag debug reset_

  
_diag debug flow filter addr xxx.xxx.xxx.xxx_  
_diag debug flow filter proto 17_  
_diag debug flow filter port 67  
  
_

_diag debug flow show function-name enable_  
_diag debug flow show iprope enable_  
_diag debug console timestamp enable_  
_diag debug flow trace start 10_  
_diag debug enable  
  
#debug #dhcp-relay #dhcp_