![](attachments/07.png)
### **Default Behavior**
- FortiManager shows device and interface statistics for the **past 10 minutes only**.
### **Enable Extended Monitoring**
- To display data over longer periods, enable history storage: 
```
config system admin setting 
	set sdwan-monitor-history enable 
end
```
- Once enabled, you can select time ranges (minutes, hours, days, or custom periods).
### **Data Retention**
- FortiManager stores SD-WAN device data for **up to 180 days** by default.
- To reduce retention and save disk space: 
```
  config system admin setting 
	  set rtm-max-monitor-by-days 
  end
```

![](attachments/08.png)