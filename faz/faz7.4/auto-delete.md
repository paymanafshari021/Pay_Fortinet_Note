# auto-delete

Use this command to automatically delete policies for logs, reports, and archived and quarantined files.
#### Syntax
```
config system auto-delete
	config dlp-files-auto-deletion
		set retention {days | weeks | months}
		set runat <integer>
		set status {enable | disable}
		set value <integer>
	end
	config quarantine-files-auto-deletion
		set retention {days | weeks | months}
		set runat <integer>
		set status {enable | disable}
		set value <integer>
	end
	config log-auto-deletion
		set retention {days | weeks | months}
		set runat <integer>
		set status {enable | disable}
		set value <integer>
	end
	config report-auto-deletion
		set retention {days | weeks | months}
		set runat <integer>
		set status {enable | disable}
		set value <integer>
	end
end

```
 
| Variable                            | Description                                                    |
| ----------------------------------- | -------------------------------------------------------------- |
| dlp-files-auto-deletion             | Automatic deletion policy for DLP archives.                    |
| quarantine-files-auto-deletion      | Automatic deletion policy for quarantined files.               |
| log-auto-deletion                   | Automatic deletion policy for device logs.                     |
| report-auto-deletion                | Automatic deletion policy for reports.                         |
| retention {days \| weeks \| months} | Automatic deletion in days, weeks, or months (default = days). |
| runat <integer>                     | Automatic deletion run at (0 - 23) o'clock (default = 0).      |
| status {enable \| disable}          | Enable/disable automatic deletion (default = disable).         |
| value <integer>                     | Automatic deletion in x days, weeks, or months (default = 0).  |