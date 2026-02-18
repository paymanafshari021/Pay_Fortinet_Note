
# Saturn Network Monitor (PowerShell)

A lightweight, real-time network monitoring tool designed to track the availability of server hosts. It provides a live dashboard, visual alerts for outages, and keeps a detailed log of downtime duration.

## Features

- **Live Dashboard:** View the status of all hosts in a single, clean terminal window.
- **Automatic Logging:** Records `DOWN` and `RECOVERED` events to `Outage_Log.txt`.
- **Downtime Tracking:** Automatically calculates how many minutes and seconds a host was offline.
- **Visual Alerts:** Triggers a 2-second Windows popup notification when a host fails.

## Prerequisites

- **Windows 10/11** or **Windows Server**.
- **PowerShell 5.1** or higher.
- **Network Permissions:** You must have permission to send ICMP Echo Requests (Pings) to the target hosts.

## etup & Usage

1. **Create the Script File:**
    - Create a new folder (e.g., `C:\NetworkMonitor`).
    - Create a new text file named `Monitor.ps1`.
    - Paste the provided PowerShell code into this file and save it.
2. **Run the Monitor:**
    - Right-click `Monitor.ps1` and select **Run with PowerShell**.
    - _Alternatively_, open a PowerShell terminal, navigate to the folder, and type:
        ```
        .\Monitor.ps1
        ```
3. **To Stop Monitoring:**
    - Simply press `CTRL + C` in the terminal window or close the window.

## File Structure

- `Monitor.ps1`: The main logic script.
- `Outage_Log.txt`: Generated automatically on the first run. This contains the history of all connection issues.

## Configuration

To add or remove servers from the list, open `Monitor.ps1` in Notepad and edit the `$hosts` array at the top of the file:

```
$hosts = "server-01.lan", "server-02.lan", "etc..."
```

## Example Log Output
The log file will look like this:

> `[2026-02-18 10:00:00] DOWN: server-01.lan`
> 
> `[2026-02-18 10:05:30] RECOVERED: server-01.lan (Total Downtime: 5m 30s)`
## Code
```powershell
$hosts = "batman.gotham.dmz", 
         "kratos.midgard.lan", 
         "10.10.10.1"

$logFile = "Outage_Log.txt"
$outageTracker = @{} # Stores host start times: { "hostname" = DateTime }

if (-not (Test-Path $logFile)) { New-Item $logFile -ItemType File | Out-Null }

while ($true) {
    $currentTime = Get-Date
    $timestamp = $currentTime.ToString("yyyy-MM-dd HH:mm:ss")
    $currentOffline = @()

    foreach ($target in $hosts) {
        $pingSuccess = Test-Connection -ComputerName $target -Count 1 -Quiet -ErrorAction SilentlyContinue
        
        if (-not $pingSuccess) {
            $currentOffline += $target
            
            # If host JUST went down, record the start time
            if (-not $outageTracker.ContainsKey($target)) {
                $outageTracker[$target] = $currentTime
                Add-Content -Path $logFile -Value "[$timestamp] DOWN: $target"
                
                # Visual Notification (No Beep)
                $ws = New-Object -ComObject WScript.Shell
                $ws.Popup("OUTAGE: $target is down!", 2, "Network Alert", 48) | Out-Null
            }
        } 
        # If host was down but is now UP
        elseif ($outageTracker.ContainsKey($target)) {
            $startTime = $outageTracker[$target]
            $duration = $currentTime - $startTime
            $durationString = "{0}m {1}s" -f [math]::Floor($duration.TotalMinutes), $duration.Seconds
            
            $logMsg = "[$timestamp] RECOVERED: $target (Total Downtime: $durationString)"
            Add-Content -Path $logFile -Value $logMsg
            
            # Remove from tracker
            $outageTracker.Remove($target)
        }
    }

    # UI Refresh
    Clear-Host
    Write-Host "--- UPTIME MONITOR & DOWNTIME TRACKER [$timestamp] ---" -ForegroundColor Cyan
    Write-Host "Log: $(Get-Location)\$logFile`n"
    
    if ($outageTracker.Count -eq 0) {
        Write-Host "STATUS: ALL SYSTEMS GREEN" -ForegroundColor Green
    } else {
        Write-Host "OFFLINE HOSTS:" -ForegroundColor Red
        foreach ($h in $currentOffline) {
            $elapsed = $currentTime - $outageTracker[$h]
            $elapsedStr = "{0}m {1}s" -f [math]::Floor($elapsed.TotalMinutes), $elapsed.Seconds
            Write-Host "[$timestamp] DOWN: $h (Duration: $elapsedStr)" -ForegroundColor Yellow
        }
    }
    
    Start-Sleep -Seconds 5
}
```