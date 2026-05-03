##  Log File Workflow

### The Three-Phase Log Lifecycle

|#|Log Phase|FortiAnalyzer Location|Immediate Analytic Support?|
|---|---|---|---|
|1|Logs received from registered device|Active log file saved with **`.log`** extension|❌ **No**|
|2|Indexed (analytics logs)|Added to **ClickHouse** database and indexed|✅ **Yes — considered _online_**|
|3|Logs rolled over and compressed (archive logs)|Compressed log file saved with **`.gz`** extension|❌ **No — considered _offline_**|
### Key Terminology from the Slide

|Term|Meaning|
|---|---|
|**Analytics logs**|Indexed in the ClickHouse DB, online, immediately searchable|
|**Archive logs**|Compressed `.gz` files on disk, offline, not immediately searchable|
|**ADOM data policy**|Governs both how long logs stay indexed AND how long archived files are kept on disk|
|**ClickHouse**|The SQL database engine FortiAnalyzer 7.6 uses for log indexing (replaces PostgreSQL)|
|**Roll-over**|The process of closing a full `.log` file, compressing it to `.gz`, and opening a new one|
### The Three Log States in Detail

Logs in FortiAnalyzer exist in one of three phases: 
+ a real-time log (just arrived, stored uncompressed in Archive, not yet in the database); 
+ an Archive log (when the real-time log file has been fully inserted, it is compressed and considered offline); 
+ and an Analytics log (indexed in the SQL database and online). Archive logs are critical — they are used to rebuild the database in the event of corruption or during upgrades.

### What Analytics Logs Can Do That Archive Logs Cannot

+ Analytic logs are the only logs used for analysis in Log View (excluding Log Browse), Incidents and Events, and Reports. Analytic logs are dissected during insertion and subtypes are stored as their own category — for example, web filtering logs are sent and stored as Traffic logs when archived, but Analytics extracts the relevant web filtering fields separately.

### The ClickHouse Migration — Critical 7.6 Change

+ Beginning in 7.6.0, FortiAnalyzer stores logs in a ClickHouse SQL database rather than a Postgres SQL database. 
+ If upgrading from an earlier version, the log database will automatically begin migration after upgrading. 
+ During migration, all historical logs insert from the Postgres database to the ClickHouse database — FortiView, Log View, Fabric View, Incidents & Events, and Reports are available during migration, but data will not be accurate until migration completes.

### Storage Size Difference — Analytics vs. Archive

+ Analytic logs indexed in the SQL database require significantly more disk space than Archive logs. 
+ ⚠️ An average indexed (analytics) log is approximately 400 bytes, while an average compressed (archive) log is approximately 50 bytes — roughly an 8:1 ratio.
+ This difference must be considered when specifying the storage ratio for Analytics and Archive logs in the ADOM disk quota settings.

### Data Policy Scope

+ Logs and files are stored on the FortiAnalyzer hard disks and are also temporarily stored in the SQL database. 
+ ⚠️When a SIEM license is added, a separate SIEM database is created to store normalized Fabric logs. 
+ When ADOMs are enabled, data policy settings can be specified per ADOM and apply only to the devices in it. Both global and per-ADOM log storage settings are always simultaneously active.

### Archive Logs and Database Rebuilds

+ FortiAnalyzer's database rebuild is performed using Archive logs — ⚠️ if Archive logs are trimmed before performing a device database rebuild, Analytic logs will not be restored. It is therefore important to review your storage data policy before performing any rebuild.

# Events and Event Handlers
+ In FortiAnalyzer, an event does not just appear automatically from a raw log — it is produced by a mechanism called an event handler. Without an event handler watching for specific conditions, a log entry will be stored but will not become an event.
+ Every log that arrives at FortiAnalyzer — from FortiGate, FortiClient, FortiMail, etc. — is automatically passed through the event handlers.
+ If logs match the conditions configured in an event handler, FortiAnalyzer generates an event

+ 🟡 "Subscription to the Security Automation Service provides FortiAnalyzer with real-time event handlers that can help detect zero-day attacks"

* This is a critical note highlighted deliberately on the slide:
    + Security Automation Service (SAS) is a Fortinet subscription service.
    + Subscribing to it gives FortiAnalyzer access to real-time, cloud-updated event handlers.
    + These handlers are specifically designed to detect zero-day attacks — meaning threats that are brand new and may not yet be in traditional signature databases.
    + This keeps FortiAnalyzer's event detection capability current and up-to-date against emerging threats without requiring manual reconfiguration by admins.

+ ❌ Don't confuse logs with events — logs are raw data; events are generated from logs by event handlers when conditions match.
+ ❌ Don't assume FortiAnalyzer generates events from ALL logs — only logs that match event handler conditions become events.
+ ❌ The Security Automation Service is a subscription — it is NOT included by default without a license/subscription.
+ ❌ Zero-day detection requires SAS subscription — this will NOT work with default built-in event handlers alone.

# How Are Events Generated?
+ Events are generated when a log matches a rule/condition in an event handler.
+ Event handlers check criteria such as: threat type, device type, log type.
+ Predefined handlers can be used, cloned, or customized; you can also build them from scratch.
+ Events are viewed in the Event Monitor, categorized as endpoint, threat, or system events.
+ Events can be escalated to Incidents for deeper investigation.
+ On escalation, you can: correlate logs, view timeline, assign priority, assign analyst.
+ Playbooks handle high-volume incident creation and resolution automatically.
+ IOCs extracted from events (IPs, domains, URLs) can be enriched via FortiGuard and VirusTotal.
+ ❌ Don't say ALL logs become events — only those that match event handler conditions.
+ ❌ Don't confuse events with incidents — events are generated first; incidents are escalated from events.
+ ❌ Enrichment services are FortiGuard AND VirusTotal — not just FortiGuard alone.
+ ❌ The Event Monitor shows three sub-categories: endpoint, threat, system — not just one combined view.
+ ❌ Playbooks are used for high-volume incident handling — not just for automation in general.

# Managing Event Handlers
+ Disabled handlers do NOT generate events — this is absolute. (TCH)
> [!TIP]
> **Best practice:** Enable only the handlers you need to avoid noise. (TCH)

# Event Handlers: Configuration
🔸 Event Handler Type: Basic or Correlation
+ Two types of event handlers exist:
    + Basic:
        + Uses OR logic across its rules. This means if any one rule in the handler matches a log, an event is generated. Simpler to configure — ideal for detecting individual, standalone conditions.
    + Correlation:
        + Uses sequence-based logic — a set of rules must be met in a specific order/sequence. More powerful and complex — ideal for detecting multi-step attack patterns. Has many more operator logic options than Basic type. Example: Rule 1 must fire, then Rule 2, then Rule 3 — only then is an event generated.
- Two event handler types: **Basic** (OR logic) and **Correlation** (sequence logic).
- **Basic** = ANY rule match → event generated.
- **Correlation** = set of rules must be met **in sequence** → event generated.
- **Data Selectors** = pre-filters applied **before rules** = also called **exclusion filters**.
- **Automation Stitch** = FortiAnalyzer notifies **FortiGate** → checks for stitch in **FortiOS** → triggers action.
- **Automatically Create Incident** toggle → every event auto-escalates to incident.
- **Notification profile** = sends alerts when handler fires.
- **MITRE attributes** can be configured per handler; many predefined handlers already have these set.
- MITRE ATT&CK matrices are viewable under **Incidents & Events** in FortiAnalyzer.
- A handler can have **one or more rules**.
- ❌ Don't confuse **Basic (OR)** with **Correlation (sequence)** — this is a very common exam question.
- ❌ **Data Selector** is NOT a rule — it is a **pre-filter/exclusion filter** that runs BEFORE the rules.
- ❌ Automation Stitch does NOT trigger actions in FortiAnalyzer itself — it notifies **FortiGate**, which checks **FortiOS** for the stitch.
- ❌ **Automatically Create Incident** is a toggle — it doesn't happen by default; it must be **explicitly enabled**.
- ❌ MITRE Tech IDs are **optional** attributes — they don't affect detection logic, only classification.
# Event Handlers: Rule Configuration
- ❌ Don't confuse rule-level logic with handler-level logic:
    - Handler (Basic) = OR between rules
    - Rule internal = Both AND ("All Filters") and OR ("Any One of the Filters") supported
- ❌ Event Severity is set per Rule — NOT per handler. Different rules in the same handler can have different severities.
- ❌ The Log Device Type selection changes available fields — if you pick the wrong device type, you'll see incorrect/irrelevant fields.
- ❌ Log Filter by Text is for precise/advanced filtering — it is NOT the same as the GUI dropdown filters.
- ❌ Log Subtype = "Any" does NOT mean the rule ignores subtypes — it means it matches ALL subtypes.

- Event conditions (thresholds) reduce the number of generated events — preventing alert fatigue.
- Four threshold types:
    - Minimum log count (group contains N or more occurrences)
    - Unique field values (N or more distinct values in a log field)
    - Sum of a numeric field (bytes, packets — greater than or equal to N)
    - Safeguard Risk Score (score exceeds N)
- Duration = the time window (in minutes) within which the threshold must be met.
- Duration + Expression work together — both must be satisfied for an event to fire.
- ⚠️ If generating too many events → configure aggregation expression and aggregation duration.
- Indicators can be extracted per rule (destination IP → IP type, hostname → Domain type).
- Advanced settings allow customization of event type, message, status, tags, and indicators.
- ❌ Duration alone does NOT fire events — it only sets the time window for the expression threshold.
- ❌ Don't confuse aggregation expression (the count/sum condition) with aggregation duration (the time window).
- ❌ Setting threshold to 1 means events fire for every single matching log — this is default but can be noisy.
- ❌ Unique values threshold (Option 2) counts distinct values in a field, NOT total log count.
- ❌ Indicators defined here (in the rule) are what gets extracted and enriched — not all log fields automatically become IOCs.
# Event Handlers: Data Selectors
- ❌ Data selectors use OR logic across filters — NOT AND logic. A log matching ANY filter passes.
- ❌ Within a filter, conditions can be AND or OR — this is configurable (don't assume one or the other).
- ❌ Log fields available in a filter depend on the device type — selecting FortiGate vs FortiMail gives different field options.
- ❌ You cannot apply a data selector to an event handler without creating it first — it must exist independently.
- ❌ Data selectors are not the same as Rules — they are pre-filters applied before rules in the event handler pipeline.
- ❌ Subnets in data selectors come from Fabric view — not manually typed IP ranges.
# Event Status
- UNHANDLED
    - **Meaning:** The threat was **detected** but was **not stopped** — it passed through without being blocked, quarantined, or isolated.
    - The event is **"open"** — meaning it requires **analyst attention and action**.
- CONTAINED
    - **Meaning:** The **source of the threat** has been **isolated** from the network — it can no longer spread or communicate.
    - This typically means the device or user was **quarantined**.
    - **Real-world log example from study guide:**
        - An **antivirus log** with `action=quarantine` → FortiAnalyzer assigns **Contained** status.
        - The malicious file was quarantined — the threat is isolated but exists.
    - **Key difference from Mitigated:** Contained means the **source is isolated** (device quarantined); Mitigated means the **traffic was blocked** (the threat attempt itself was stopped).
- BLANK
    - **Meaning:** The event doesn't clearly fall into any of the three defined categories.
    - This typically occurs when **mixed actions** are observed in the logs associated with the event.
    - Blank signals the analyst that **manual review is needed** to determine the actual risk state.
- Events have **exactly four possible statuses:** Unhandled, Contained, Mitigated, Blank.
- **Unhandled** = risk NOT mitigated or contained = **open** = needs analyst action.
- **Contained** = risk source **isolated** (quarantined).
- **Mitigated** = traffic **blocked or dropped**.
- **Blank** = mixed actions / other scenarios — doesn't fit the other three.
- **Log action examples:**
  - `action=pass` → **Unhandled**
  - `action=quarantine` → **Contained**
  - `action=block` or `action=drop` → **Mitigated**
  - Both allow + block → **Blank**
- **Botnet and IoC events** are always **Unhandled**.
- Status can be **set manually in handler settings** OR **assigned automatically** by FortiAnalyzer.
- Event status is viewed in **Incidents & Events > Event Monitor**.
- ❌ Don't confuse **Contained** and **Mitigated**:
  - **Contained** = the **SOURCE** is isolated (quarantine).
  - **Mitigated** = the **TRAFFIC** was blocked/dropped.
- ❌ **Blank** does NOT mean "no status set yet" — it means the risk state is **ambiguous** (mixed actions).
- ❌ **Unhandled ≠ undetected** — the threat WAS detected; it just wasn't stopped (action=pass).
- ❌ Botnet and IoC events are always **Unhandled** — even if related traffic was later blocked.
- ❌ FortiAnalyzer assigns status **automatically** based on log action — you don't always need to set it manually.
# Event Handlers: Log Filter by Text
- Generic Text Filters provide **more precise and flexible** log filtering than GUI dropdowns.
- Filters are based on **regex and POSIX** standard.
- **Eight comparison operators:** `==`, `!=`, `<`, `<=`, `>`, `>=`, `~`, `!~`
- `~` = **Contains** (included somewhere in the string) — supports regex.
- `!~` = **Not contained** (not included).
- **Logical tokens:** `and` / `&`, `or` / `|`, `not`, `(` `)`.
- Parentheses `( )` control **evaluation precedence** — use them to group `or` conditions.
- Use **escape character `\`** for reserved characters in filter values.
- **Best practice:** Copy field names/values from **raw logs** to avoid syntax errors.
- Syntax help/examples are available **directly in the GUI** via the ℹ️ icon.
- ❌ Don't confuse `==` (exact match) with `~` (contains) — they behave very differently:
  - `hostname == "facebook"` → hostname must be **exactly and only** "facebook"
  - `hostname ~ "facebook"` → hostname just needs to **contain** the word "facebook"
- ❌ `&` and `'and'` are **the same** — both are valid AND operators.
- ❌ `|` and `'or'` are **the same** — both are valid OR operators.
- ❌ **Parentheses matter** — without them, operator precedence may cause unexpected behavior.
- ❌ The `~` operator is **not just a simple substring match** — it supports **regex patterns**.
- ❌ Reserved characters must be **escaped with `\`** — forgetting this causes filter errors.
- ❌ Generic text filter is based on **POSIX** — not standard SQL or Python regex syntax.
# Predefined Event Handlers
- FortiAnalyzer includes **predefined event handlers** for immediate deployment — no configuration required to start using them.
- Predefined handlers are especially useful for **new deployments** as they provide essential monitoring right away.
- Predefined handlers follow **Fortinet's recommended best practices** — they are reliable and well-designed.
- Predefined handlers serve **two purposes:** operational detection AND learning tool.
- Three specific **MS Exchange predefined handlers:**
  1. **MS Exchange–Large Email** → detects abnormally large emails (data exfiltration risk)
  2. **MS Exchange–Multiple Failed Deliveries** → detects delivery failures (email harvesting/DHA risk)
  3. **MS Exchange–Suspicious Email Activity** → detects anomalous email behavior (account compromise risk)
- You can **examine predefined handler filters/conditions** to learn how to build your own.
- Logs flow from endpoints → MS Exchange → Windows Server → FortiClient → **FortiAnalyzer**.
- ❌ Don't say predefined handlers require configuration before use — they are **ready to enable immediately**.
- ❌ Predefined handlers are NOT just for MS Exchange — they cover many technologies; MS Exchange is just the example used on this slide.
- ❌ Don't assume predefined handlers are always enabled by default — they must be **explicitly enabled** (disabled handlers don't generate events).
- ❌ **MS Exchange–Multiple Failed Deliveries** is NOT about login failures — it's about **email delivery failures** (NDRs, bounces).
- ❌ Predefined handlers can be used, cloned, OR customized — they are **not locked/read-only**.
# Use Case: Sensitive Keyword Detection for Safeguarding
- The predefined handler is called "Sensitive Keyword Detection for Safeguarding".
- It monitors three traffic channels: Web traffic, Application traffic (social media), and Email.
- It has three separate rules — one per log type: web filter, application control, email filter.
- Trigger type used: Safeguard Risk Score is more than [threshold] — NOT log count or sum.
- Default threshold: Safeguard Risk Score > 0.8.
- Duration window: 1440 minutes = 24 hours.
- Keywords and risk scores are viewed at Incidents & Events > Safeguarding.
- Keywords are provided and maintained by FortiGuard (origin = FortiGuard).
- Example category: Cyber Bullying — with 2,163 keywords.
- Each keyword has a Risk Score between 0.0 and 1.0.
- Keywords with score 0.6 (as shown) would NOT trigger an event (threshold is > 0.8).
- Warning: When Safeguard trigger is used, log categorization options may be more limited.
- ❌ Don't confuse the Risk Score threshold (> 0.8) with individual keyword scores — a keyword scoring 0.6 does NOT trigger an event.
- ❌ The handler has three rules, not one — each rule targets a different log type.
- ❌ The duration is 1440 minutes (24 hours) — not the default 30 minutes.
- ❌ Keywords are maintained by FortiGuard — not manually entered by the analyst.
- ❌ "App traffic" refers specifically to social media and application control logs — not general application logs.
- ❌ The Safeguarding page (Incidents & Events > Safeguarding) is SEPARATE from the Event Handlers page — it's where you VIEW keywords and scores.
# Exporting and Importing Event Handlers
- Event handlers are **scoped per ADOM** — they are NOT shared across ADOMs automatically.
- To reuse handlers in a different ADOM → **Export from source ADOM, Import into destination ADOM**.
- **Export options:**
  - Include **Data Selectors** (toggle)
  - Include **Notification Profiles** (toggle)
  - Format: **Zipped / Text / CLI**
- Export is accessed via: **Select handler(s) → Right-click (or More ▾) → Export**.
- Import is accessed via: **Right-click (or More ▾) → Import → Drag & Drop or Browse**.
- **Multiple handlers** can be selected and exported at once.
- On name conflict during import: **Rename / Replace / Skip**.
- **Factory Reset** option (in More ▾ menu) resets all handlers to factory defaults.
- **Show Predefined / Show Custom** toggles control which handlers are visible in the list.
- Subnets from **Fabric view** can be used as filters in both event handlers and reports.
- ❌ Event handlers are **per ADOM** — do NOT assume they are global or shared across all ADOMs automatically.
- ❌ If you export a handler **without including its Data Selector**, the imported handler may not work correctly in the new ADOM — the referenced data selector won't exist there.
- ❌ **Factory Reset** is in the **More ▾** menu — be careful; it resets ALL handlers to defaults.
- ❌ **CLI** export format is NOT just for viewing — it can be used to **recreate the handler via command line**.
- ❌ On name conflict, you have **three choices**: Rename, Replace, OR Skip — NOT just overwrite.
- ❌ **Show Predefined / Show Custom** are **view filters only** — they don't enable or disable handlers.
# Managing Events
- ❌ Don't confuse First Occurrence (when the event first fired) with Last Update (most recent log added to the event) — they can be the same or different.
- ❌ Count in the event table = number of log occurrences grouped into the event — NOT the number of separate events.
- ❌ An event with Mitigated status doesn't necessarily need analyst action — but Unhandled Critical always does.
- ❌ Unexpected events = handler configuration problem — not a FortiAnalyzer bug.
- ❌ The Handler column shows the handler NAME — use this to trace back which handler fired and why.
- ❌ "Traffic to C&C detected" with status Unhandled means the traffic was NOT blocked — it passed through. This is a critical finding.
# Available Management Actions for Events
- ❌ Acknowledging does NOT delete the event — it's still there, just hidden from default view. Use "Show Acknowledged" to see it.
- ❌ Don't assume Mitigated = safe to ignore always — excessive mitigated events from one host can indicate compromise.
- ❌ "Add to Existing Incident" ≠ "Create New Incident" — one adds to existing, one creates new.
- ❌ Acknowledged events are hidden by default — always check this if events seem to be "missing".
- ❌ Assign To can be to yourself — it's not only for delegating to others.
- ❌ The right-click filter panel offers Replace as well as Add — Replace will remove all current filters and apply the new one.
# What Are Indicators?
- **Indicators** = data extracted from events that could be **harmful or malicious** = **IOCs**.
- **Three indicator types:** IP address / URL / Domain — **only these three**.
- Indicators are configured in **event handler rule → Advanced Settings → Indicators section**.
- You can also **manually create indicators** on the **Indicator page**.
- **Four reputation types** (assigned after enrichment): **Malicious / Suspicious / Harmless / Undetected**.
- Reputation is determined by **enrichment** via FortiGuard and/or VirusTotal.
- **Undetected ≠ Safe** — the indicator may be a new/unknown threat.
- **Malicious** = confirmed threat → immediate action required.
- **Suspicious** = warrants investigation but not confirmed.
- **Harmless** = confirmed clean — no action needed.
- In the GUI example: `dstip` → **IP** type, `hostname` → **Domain** type.
- ❌ Indicators are **NOT automatically extracted from all log fields** — you must **configure which fields** to extract in the event handler rule.
- ❌ There are **exactly THREE indicator types** — IP, URL, Domain. Not MAC address, not port, not hash.
- ❌ **Undetected** does NOT mean safe — it means **no intelligence data found** for this indicator.
- ❌ Reputation is assigned **after enrichment** — an un-enriched indicator has no reputation yet.
- ❌ **Harmless** indicators can still exist in logs — harmless reputation means the threat intel confirms it's clean, not that it's irrelevant to investigate.
- ❌ Indicators can be created **both automatically (via event handler rules) AND manually (via Indicator page)**.
# Enriching Indicators
- **Indicators page path:** `Incidents & Events > Indicators`
- **Enrich button** in the toolbar triggers enrichment for selected indicators.
- FortiAnalyzer enriches indicators using **two sources:**
  1. **FortiGuard CTS** (Cyber Threat Service)
  2. **VirusTotal**
- **FortiGuard CTS provides:** Confidence level, IOC Category, IOC Tags, Web Filter Category, AV Category.
- **VirusTotal provides:** Number of engines detecting as malicious out of total (e.g., **1/94**), per-vendor verdicts.
- **WHOIS Lookup** provides domain registration context (registrar, creation date, name servers, etc.).
- Enrichment history: **updates existing entry** if results unchanged; **creates new entry only if results change**.
- Example: `99.goodyouxi.com` → FortiGuard: **Malware C&C, High confidence** / VirusTotal: **1/94 malicious**.
- Even **1/94 VirusTotal detection + FortiGuard Malware C&C** = treat as malicious — FortiGuard's high confidence overrides.
- **ELF Malware** tag = **Linux-based malware** — important context for identifying targeted systems.
- **Web Filter Category: Meaningless Content** = common disguise used by C&C domains.
- ❌ **VirusTotal alone may not be sufficient** — FortiGuard CTS provides independent, specialized intelligence. A "1/94" VirusTotal score combined with FortiGuard's "High confidence Malware C&C" is still highly significant.
- ❌ **Low VirusTotal detection count does NOT mean safe** — new/targeted malware may only be detected by 1-2 engines.
- ❌ Enrichment **saves history** — it does NOT create a new record every time you re-enrich.
- ❌ A **new history entry** is only created when enrichment results **change** — not on every enrichment.
- ❌ **WHOIS data** is context/registration info — it is NOT a threat verdict. A GoDaddy-registered domain can still be malicious.
- ❌ **Harmless** from most VirusTotal vendors doesn't override **Malicious** from FortiGuard CTS — use all sources together.
- ❌ Don't confuse **IOC Category** (FortiGuard's classification like "Malware C&C") with **Reputation type** (Malicious/Suspicious/Harmless/Undetected)
# Connectors for Indicator Enrichment
- Two enrichment services: **FortiGuard CTS** and **VirusTotal**.
- **FortiGuard** = **built-in, ready to use, no API key needed**.
- **VirusTotal** = requires **API key configuration** in **Incidents & Events > Automation > Active Connectors**.
- VirusTotal connector configuration path: **Incidents & Events > Automation > Active Connectors**.
- The **Indicator Enrichment playbook** is the automation engine behind all indicator enrichment.
- The playbook is: **Predefined / Read-only / Enabled by default**.
- The playbook runs **every time an indicator is enriched**.
- Playbook flow: **ON_DEMAND STARTER → ENRICH_START → [VirusTotal + FortiGuard in parallel] → ENRICH_AGGREGATE**.
- VirusTotal and FortiGuard enrichment run **simultaneously (in parallel)**.
- **ENRICH_AGGREGATE** node combines results from both sources.
- The playbook is found at: **Incidents & Events > Automation > Playbooks**.
- ❌ **FortiGuard does NOT require an API key** — it is built-in. Only **VirusTotal requires an API key**.
- ❌ The Indicator Enrichment playbook is **read-only** — you CANNOT edit or delete it.
- ❌ The playbook is **enabled by default** — unlike event handlers which are disabled until you enable them.
- ❌ VirusTotal connector is configured under **Automation > Active Connectors** — NOT under Event Handlers or the Indicators page directly.
- ❌ The two enrichment sources run **in parallel** — NOT sequentially one after the other.
- ❌ **ENRICH_AGGREGATE** is a separate node — it's not part of either the VirusTotal or FortiGuard node; it combines their outputs.
- ❌ VirusTotal is counted as a **Third Party Connector** — FortiGuard is not counted in that category since it is native/built-in.
# Blocking Indicators
- **Blocking indicators** requires an **authorized FortiManager connector** configured on FortiAnalyzer's **Fabric Connector** page.
- The **⊘ Block** button in the Indicators toolbar initiates blocking.
- The **Block_indicator playbook** runs every **5 minutes** in the background to push blocked indicators to FortiManager.
- After the playbook runs → indicator status changes from **TBD** to **Blocked** on FortiAnalyzer.
- **"Blocked" status = updated on FortiManager ONLY — NOT yet synced to FortiGate.**
- Blocked indicators are stored in **FortiManager: FortiGuard > External Resource** as three files:
  - **root_BLKIP** — blocked IPs
  - **root_BLKURL** — blocked URLs
  - **root_BLKDOM** — blocked Domains
- FortiManager External Resources can be used to create: **threat feeds, security profiles, policy blocks**.
- Can push blocklist to **specific FortiGate** OR **all FortiGate devices**.
- **Firmware version of FortiManager must match FortiAnalyzer** for blocklist to be pushed to FortiGate.
- Initial block status before pushing = **TBD (To Be Determined)**.
- ❌ **"Blocked" status does NOT mean FortiGate is blocking** — it only means FortiManager has the updated list. FortiGate is not synced yet!
- ❌ Block_indicator playbook runs every **5 minutes** — **NOT instantly**. There is a delay.
- ❌ FortiAnalyzer cannot push blocks **directly to FortiGate** — it always goes through **FortiManager** first.
- ❌ The required connector is a **FortiManager connector** — NOT a FortiGuard or VirusTotal connector.
- ❌ **Firmware mismatch** between FortiAnalyzer and FortiManager = blocklist **cannot be pushed** to FortiGate.
- ❌ **TBD** = "To Be Determined" — indicator is identified but blocking not yet executed.
- ❌ There are **three separate External Resource files** in FortiManager — one per indicator type (IP, URL, Domain) — not one combined file.

# Incidents
- The **Incidents page** is the **central command center** for SOC analysts.
- An **incident** = a **container for related security events**.
- **Three interactive donut charts:** Severity / Status / Category — clicking filters the table.
- **Incident Number format:** IN + 7 digits (e.g., IN0000001).
- **"Auto-Raised"** in Incident Reporter = automatically created by the system.
- **Promote** = escalate an event directly to an incident from this page.
- ❌ Don't confuse **Event** (single detected occurrence) with **Incident** (container for related events requiring investigation).
- ❌ **"Auto-Raised"** does NOT mean a human analyst created it — it was automatically escalated by the system (via "Automatically Create Incident" toggle in event handler).
- ❌ The three charts show **Severity / Status / Category** — not the same as Event status
- ❌ Incident **Status** (New/Analysis/Response/Closed...) is DIFFERENT from Event **Status** (Unhandled/Mitigated/Contained/Blank).
- ❌ Clicking a chart segment **filters the table** — the charts are interactive, not just decorative.
- ❌ **MITRE ATT&CK ICS** tab = specifically for **Industrial Control Systems** — not general IT incidents.
- ❌ **"not implemented"** as affected endpoint (IN0000001) = the endpoint detection feature wasn't configured for that incident.
# Creating an Incident
- Create an incident when an event **needs further analysis** or has **potential negative operational consequences**.
- **Two methods:** Manual (right-click in Event Monitor) OR Automatic (playbooks) — **preferred method is automatic/playbooks**.
- Manual method: **Event Monitor → right-click event → Create New Incident**.
- **Required fields:** Category / Severity / Status / Affected Endpoint.
- **Optional fields:** Description / MITRE Domain + Tech ID / Assigned To.
- **Status always starts as "New"** when an incident is first created.
- **MITRE Domain options:** N/A / Enterprise / ICS.
- **Analyst accounts must be pre-created** in FortiAnalyzer before they can be assigned to incidents.
- Created incident appears immediately in **Incidents & Events > Incidents**.
- **Incident Reporter** = who created the incident: "admin" (manual) or "Auto-Raised" (automatic).
- **Affected Endpoint** is auto-populated from the source event.
- **Description** is auto-populated from the event handler name (can be customized).
- ❌ **Automatic creation via playbooks is PREFERRED** over manual — don't say both methods are equally recommended.
- ❌ **Status always starts as "New"** — never starts as Analysis or any other status.
- ❌ MITRE attributes (Domain + Tech ID) are **optional** — not required to create an incident.
- ❌ **Assigned To is optional** — you can create an incident without assigning it immediately.
- ❌ You cannot assign an incident to someone who doesn't have a **FortiAnalyzer account** — accounts must be pre-created.
- ❌ Don't confuse incident **Category** (Unauthorized Access, Malicious Code, etc.) with event **Type** (Traffic, Web Filter, etc.) — these are different classifications.
- ❌ **MITRE ICS domain** = Industrial Control Systems — not general enterprise IT.
# Editing an Incident
- Every incident MUST reach Closed status — mandatory requirement.
- Two Closed states: Closed: Remediated / Closed: False Positive.
- ❌ "Closed: False Positive" ≠ doing nothing — it means the incident was investigated and determined to be false. You still need to investigate first.
- ❌ "Closed: Remediated" ≠ just blocking something — it means the full threat was contained and eliminated.
# FortiAI: How Does It Work?
+ The LLM is the external GenAI engine (e.g., cloud-based AI) that FortiAI calls for language understanding and generation
- FortiAI is NOT a direct call to public GenAI — it adds a proprietary knowledge layer.
- FortiAI integrates with: FortiManager, FortiAnalyzer, FortiSIEM, FortiSOAR.
- RAG = Retrieval-Augmented Generation — the process of supplementing GenAI with proprietary data.
- Security & Privacy guarantees:
  - Only explicit interaction content shared with cloud AI
  - Sensitive data can be automatically masked
  - FortiAI does NOT share or provide access to customer data
- FortiAI communicates bidirectionally with an external LLM (Large Language Model).
- ❌ The curated database is maintained by Fortinet specialists — it is NOT generated automatically by AI alone.
# FortiAI Data Protection for FortiAnalyzer
+ Images of network topology diagrams (IP addresses in a diagram can't be masked programmatically)
- The FortiAI assistant operates within explicitly defined boundaries. If an unsupported query is made (for instance, asking about weather, stock prices, or recipes), the AI will respond with 'I'm not sure' or 'I don't know'.
- RAG ensures the accuracy and relevance of the responses. This technology allows the AI to retrieve relevant information and provide references to support its answers.
- Four data protection mechanisms:
  - Function callback — LLM generates query structure; results processed locally on premises
  - Data masking — masks IP addresses, MAC addresses, usernames before LLM; unmasks locally after
  - Secure proxy — ALL AI interactions pass through Fortinet proxies for additional security checks
  - Data privacy warnings — warns user when data cannot be masked (e.g., images); requires user confirmation before uploading
- Two AI guardrails:
  - Defined boundaries — unsupported queries return "I'm not sure" or "I don't know"
  - RAG — improves accuracy by retrieving relevant info; provides references to support answers
- Three types of masked data: IP addresses / MAC addresses / Usernames
- Data processing happens LOCALLY — the cloud LLM never sees real results
- Images/visual content may trigger data privacy warnings — cannot be auto-masked
- RAG also serves as a guardrail against hallucination
- ❌ Function callback = LLM generates the query structure — NOT the results. Results are processed locally.
- ❌ Data is unmasked LOCALLY after function callback — not in the cloud.
- ❌ Secure proxy provides additional checks — it doesn't replace masking; both work together.
- ❌ Privacy warnings trigger when data cannot be masked — NOT for all data. Normal queries with maskable data don't trigger warnings.
- ❌ "I'm not sure" / "I don't know" are the specific FortiAI responses to unsupported queries — not error messages.
- ❌ RAG is listed as BOTH a processing step (slide 114) AND an AI guardrail (this slide) — it serves dual purposes.
- ❌ FortiAI masks three specific types: IP addresses, MAC addresses, usernames — NOT all data fields.
# FortiAI Data Protection Flow Visualized
- The FortiAI proxy is hosted by Fortinet — not a third-party cloud service.
- Data is masked BEFORE reaching the proxy — the proxy itself sees masked data.
- Protected data categories (examples — NOT full list):
  - IoT devices (MAC, vendors, hostnames)
  - FortiGate device name
  - The "root" keyword
  - VDOM names
  - IPv4 and IPv6 addresses
  - Hostnames
  - User (usernames)
  - MAC addresses
- Masking uses GDPR features — GDPR-compliant anonymization.
- Visual masking representation: Real data 123-456 → masked as XXX-XXX to LLM → unmasked back to 123-456 locally.
- RAG is implemented between the proxy and the LLM — not at the user interface.
- RAG knowledge sources: Administrator guides / Supporting documentation / Fortinet knowledge base / others.
- RAG ensures responses come from approved sources only — prevents hallucination.
- ❌ The FortiAI proxy is Fortinet-hosted — NOT a generic third-party proxy.
- ❌ Data is masked before the proxy — the proxy doesn't do the masking; masking happens before the request reaches the proxy.
- ❌ The protected data list is NOT complete — "These are examples and do not constitute the full list."
- ❌ "Root" keyword is protected — this refers to the ADOM name "root", not a Linux root user.
- ❌ Both IPv4 AND IPv6 addresses are masked — not just IPv4.
- ❌ RAG is positioned between proxy and LLM — not between the user and the proxy.
- ❌ RAG knowledge sources are approved Fortinet documentation — not general internet content.
- ❌ GDPR features are used for masking — this is a compliance-oriented implementation.
# Example of Data Protection
- ❌ The LLM does NOT see actual log data — it only generates query structure (function calls).
- ❌ Results are displayed locally — they are NOT processed by or returned through the LLM.
- ❌ The function call is what gets sent back (Call back step) — not actual data.
- ❌ FortiAI determines what's needed first — not all queries require sending sensitive data to the LLM at all.
# Token Usage
- FortiAI license = monthly token entitlement shared by ALL FortiAI users.
- Tokens measure BOTH input (prompt) AND output (response) — both count against the budget.
- Three token usage guidelines:
  - Both prompt and response are counted as tokens
  - More text = more tokens (general rule — not strictly 1:1 with words)
  - Long sessions consume more tokens — session history is included in context
- Long sessions use more tokens because FortiAI includes session history in each query's context.
- Monthly token budget is shared — heavy use by one analyst reduces availability for others.
- Longer queries generate larger outputs — scope limitation reduces both input AND output tokens.
- ❌ Tokens are NOT the same as words or characters — "no strict one-to-one link" between them.
- ❌ The token budget is shared across all users — not per-user individual allocations.
- ❌ Both input AND output consume tokens — don't think only the query costs tokens.
- ❌ Long sessions cost MORE than new sessions for the same query — session history adds to context size.
- ❌❌ Unused tokens do NOT carry over — the entitlement is monthly and resets.
- ❌ Query 1 is more expensive than Query 2 for two reasons — longer input AND larger output (not just one reason).
# Managing Monthly Tokens: Best Practices
### 🔸 Best Practice 1: Make Prompts Concise and Specific

> **"Make your prompts concise and specific — wordy queries use more text and therefore more tokens"**
> **Example:** *"Show recent logs for `10.10.10.10` (past week)"* rather than *"Can you show me all the log entries from endpoint `10.10.10.10` from the past week?"*

**Why this matters:**
- As established in slide 118, **more text = more tokens**.
- Wordy, conversational prompts waste input tokens on filler words and unnecessary phrasing.
- The two example queries in the slide ask for the **exact same information** — but one is significantly more concise.

**Comparison:**
| Version | Word Count | Token Efficiency |
|---|---|---|
| *"Can you show me all the log entries from endpoint 10.10.10.10 from the past week?"* | 18 words | ❌ Inefficient — filler words |
| *"Show recent logs for 10.10.10.10 (past week)"* | 7 words | ✅ Efficient — direct and precise |

**Key principle:** FortiAI is **not a chatbot** — you don't need to be polite or conversational. Direct, structured prompts are both more token-efficient AND more likely to produce accurate results.
### 🔸 Best Practice 2: Use Filters in Prompts

> **"Use filters in your prompts to receive concise and specific responses"**
> **Sub-point:** *"Include time ranges or specify limits for the number of results"*

**Why this matters:**
- Unfiltered queries (e.g., "show all logs for this endpoint") can generate **enormous responses** — consuming massive output tokens.
- Adding **filters** limits what FortiAI retrieves, making the response smaller and more focused.

**Types of filters to include:**
| Filter Type | Example |
|---|---|
| **Time range** | "past week", "last 24 hours", "today", "between Jan 1 and Jan 7" |
| **Result count limit** | "top 10", "first 5 results", "maximum 20 entries" |
| **Severity filter** | "only critical events", "high severity only" |
| **Device filter** | "from FortiGate HQ only" |
| **Action filter** | "blocked traffic only" |

**Benefit:** Filters reduce **output tokens** dramatically — the response only contains what you actually need.
### 🔸 Best Practice 3: Use FortiAnalyzer Function Words

> **"Use words that relate to FortiAnalyzer functions to specifically direct FortiAI"**
> **Examples:** *"Apply filter"*, *"Generate report"*, or *"Generate script"*

**Why this matters:**
- FortiAI is designed to perform **specific FortiAnalyzer functions**.
- Using **function-specific keywords** tells FortiAI exactly what type of action to perform — eliminating ambiguity and reducing the need for clarifying exchanges (which waste tokens).

**Function keywords and their effects:**
| Keyword | What it directs FortiAI to do |
|---|---|
| **"Apply filter"** | Apply a specific filter to the log view or event monitor |
| **"Generate report"** | Create a formal report based on specified parameters |
| **"Generate script"** | Produce a CLI script or configuration script |
| **"Show logs"** | Retrieve and display log entries |
| **"Create event handler"** | Build a new event handler configuration |

**Why this conserves tokens:** Function keywords produce **targeted, precise responses** rather than broad explanations — fewer output tokens consumed.
### 🔸 Best Practice 4: Leverage Predefined Assets

> **"Leverage predefined datasets, charts, reports, and event handlers whenever possible"**

**Why this matters:**
- FortiAnalyzer comes with **predefined assets** (event handlers, report templates, chart templates, datasets) that are already configured and optimized.
- Asking FortiAI to work WITH existing predefined assets is **more token-efficient** than asking it to build everything from scratch.
- Examples:
  - Instead of: "Create a new report showing all IPS events" → Reference an existing IPS report template
  - Instead of: "Build an event handler for malware detection" → Leverage a predefined malware detection handler
- Using predefined assets also produces **more accurate results** since FortiAI doesn't have to generate complex configurations from nothing.
### 🔸 Best Practice 5: Include Details in the Existing Thread

> **"Include details in the existing thread whenever possible"**
> **Sub-point:** *"Note that FortiAI does not retain previous threads"*

**Two parts to understand here:**

**Part A — Use the existing thread:**
- If you have follow-up questions or need to add context to an ongoing investigation, **continue in the same conversation thread** rather than starting a new one.
- Keeping related queries in one thread means FortiAI has the **accumulated context** from previous exchanges — it doesn't need to re-establish context from scratch.
- This is more token-efficient for closely related queries because FortiAI already "knows" what you've been discussing.

**Part B — FortiAI does NOT retain previous threads:**
- When you **start a new session/thread**, FortiAI has **zero memory** of previous conversations.
- Unlike a human analyst who remembers previous cases, FortiAI starts completely fresh each time.
- This means if you close a session and come back, you must **re-provide relevant context**.
- **Implication:** For ongoing investigations, keeping everything in one thread avoids re-establishing context (which costs tokens).
### 🔸 Best Practice 6: Restart After 10 Conversations

> **"Restart the FortiAI assistant after 10 conversations if you don't need to keep the historical context"**

**Why this is the most specific and exam-important best practice:**

- As established in slide 118, **long sessions consume more tokens** because session history is included in each query's context.
- After **10 conversation exchanges**, the accumulated history becomes large enough to significantly impact token consumption.
- **The specific number: 10 conversations** — this is the Fortinet-recommended threshold for restarting.

**Two scenarios:**

| Scenario | Action |
|---|---|
| **You need the historical context** (continuing the same investigation) | Keep the session open — accept the higher token cost for continuity |
| **You DON'T need historical context** (moving to a new topic) | **Restart the FortiAI assistant** to reset the context and save tokens |

**Study guide explains:**
> *"Restarting the FortiAI assistant and eliminating the historical context is important for conserving tokens, so you should do this unless you really need to reference the chat's historical context to continue processing."*

- **Six best practices** for managing monthly tokens:
  1. **Concise and specific prompts** — avoid wordy/conversational language
  2. **Use filters** — include time ranges, result limits
  3. **Use FortiAnalyzer function words** — "Apply filter", "Generate report", "Generate script"
  4. **Leverage predefined assets** — datasets, charts, reports, event handlers
  5. **Include details in existing thread** — FortiAI does **NOT retain previous threads**
  6. **Restart after 10 conversations** — if historical context not needed
- **FortiAI does NOT retain previous threads** — fresh start each new session.
- **Restart threshold: 10 conversations** — Fortinet's recommended point to restart.
- **Valid prompt** = clear, well-defined, specific, relevant, translatable to SQL queries.
- **Invalid prompt** = ambiguous, lacking detail, or outside FortiAI's scope.
- Valid prompts → translate to **precise SQL queries** on FortiAnalyzer.
- Function keywords: **"Apply filter"**, **"Generate report"**, **"Generate script"**.
- Prompts should be directly related to what FortiAI is **programmed to access**.
- ❌ **FortiAI does NOT retain previous threads** — every new session starts completely fresh.
- ❌ The restart threshold is specifically **10 conversations** — not 5, not 20.
- ❌ Restart is recommended **only if** you don't need historical context — if you do need it, keep the session.
- ❌ **"Generate script"** is a valid FortiAI function keyword — FortiAI can generate CLI scripts.
- ❌ Valid prompts translate to **SQL queries** specifically — not API calls or CLI commands directly.
- ❌ Invalid prompts include those that are **out of scope** (like weather questions) — consistent with the "I'm not sure" defined boundaries from slide 115.
- ❌ Best practice 5 has TWO components: use existing thread AND note that previous threads are not retained — don't forget either part.
- ❌ The dashboard uses cached data — NOT live real-time data queries
# IOCs (Compromised Hosts)
- IOC engine detects compromised end users by checking new AND historical logs against IOC signatures.
- Requires a FortiGuard subscription — not available without it.
- Updated daily — IOC database refreshed every 24 hours.
- Three log types analyzed: Web filtering / DNS / Traffic logs.
- Excluded log types: Antivirus logs / IPS logs (threats already caught by these services).
- Detection process: Log match → threat score assigned → scores aggregated per user → verdict delivered.
- Two verdicts:
  - Infected = confirmed breach (blacklisted IPs or DGA matches in web logs)
  - Suspicious = possible breach with varying degrees of confidence
- DGA = Domain Generation Algorithm — malware technique for generating random C&C domain names.
- FortiAnalyzer receives daily threat database updates from FortiGuard.
- The IOC engine finds threats that AV and IPS missed — focuses on subtle, stealthy compromise indicators.
- FortiGate sends web filtering, DNS, and traffic logs to FortiAnalyzer for IOC analysis.
- ❌ AV and IPS logs are NOT analyzed by the IOC engine — those threats are already handled.
- ❌ IOC engine requires a FortiGuard subscription — it's not a default feature.
- ❌ Infected ≠ Suspicious — Infected is confirmed, Suspicious is possible with varying confidence.
- ❌ Historical logs are also checked — not just new logs. New IOC signatures are applied retroactively.
- ❌ DGA = Domain Generation Algorithm — a malware technique, not a Fortinet feature.
- ❌ Updates are daily — not real-time or weekly.
- ❌ Suspicious has varying degrees of confidence — it's not a binary state like Infected.
- ❌ The IOC engine focuses on end users — not just devices or IP addresses.
# IOC Log Type and Data
- The IOC engine checks **different data fields** depending on the **log type**.
- **Six log types** are checked by the IOC engine with their corresponding data:

| Log Type | Data Checked |
|---|---|
| **Attack logs** | URLs, domains, **and** IP addresses |
| **DNS** | IP addresses **only** |
| **Email filter logs** | URLs, domains, **and** IP addresses |
| **Event logs** | **Threat type** only |
| **Traffic logs** | IP addresses **only** |
| **Web filter** | URLs, domains, **and** IP addresses |

- **Three log types check URLs + domains + IPs:** Attack / Email filter / Web filter
- **Two log types check IPs only:** DNS / Traffic
- **One log type checks threat type only:** Event logs
- IOC results are displayed **per end user** — not per device or IP
- **Three-level drill-down:** IOC summary → Host detail (detection pattern + method) → Raw log entries
- **Detection pattern** = the specific IOC signature that matched
- **Detection method** = which log type and data field triggered the match
- ❌ **DNS logs check IP addresses ONLY** — NOT domains. The resolved IP is what's checked, not the queried domain name.
- ❌ **Traffic logs check IP addresses ONLY** — NOT URLs or domains (those are in web filter/DNS logs).
- ❌ **Event logs check THREAT TYPE** — not IPs, URLs, or domains. This is the unique one.
- ❌ There are **exactly six log types** — don't add or omit any.
- ❌ The **three-level drill-down** goes: IOC summary → detection pattern/method → **raw logs** (not incidents or events).
- ❌ IOC results are per **end user** — not per device or network segment.
