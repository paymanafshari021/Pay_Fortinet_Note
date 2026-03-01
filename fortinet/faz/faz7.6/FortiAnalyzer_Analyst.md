# FortiView - Complete Explanation

## What is FortiView?
FortiView is FortiAnalyzer's **comprehensive monitoring and visualization system** that provides both real-time and historical security data in a unified interface.

---

## Key Characteristics

### 1. Real-time and Historical Data
- FortiView simultaneously displays **live traffic/threats** happening right now AND **historical trends** over time, giving analysts a complete picture of network security posture.

### 2. ADOM-Specific Analysis
- Each ADOM has its **own independent FortiView instance**. Data from one ADOM is completely separate from another.
- Important practice: always **verify you are in the correct ADOM** before analyzing FortiView data to avoid confusion.

### 3. Analytics Logs Only (NOT Archive Logs)
- FortiView **only displays data from analytics logs** — meaning logs that have been parsed, normalized, and indexed into the **SQL database**.
- **Archive logs** (raw compressed .gz files) are **NOT visible** in FortiView. To view archive logs you must use Log Browse instead.

### 4. Multiple Dashboards
FortiView offers several dashboard categories visible in the menu:
- **Threats** — malicious activity, IOCs, compromised hosts
- **Traffic** — network flow analysis
- **Shadow IT** — unauthorized application usage
- **Applications & Websites** — app and web activity
- **VPN** — VPN tunnel and user activity
- **System** — system-level visibility

---

## Disabling FortiView via CLI
FortiView can be **disabled for performance tuning** using:
```
config system global
    set disable-module fortiview-noc
end
```
When disabled:
- The **GUI hides the FortiView section entirely**
- **Background processing stops** for FortiView features
- This frees up system resources when FortiView is not needed

---

## Key Takeaways for the Exam
| Feature | Detail |
|---|---|
| Data source | Analytics logs (SQL database) only |
| Archive logs | NOT shown in FortiView |
| Scope | Per-ADOM |
| Disable method | CLI only |
| Purpose | Real-time + historical monitoring |
