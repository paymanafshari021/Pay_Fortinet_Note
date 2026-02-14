## FortiAnalyzer Event Status Management and Classification Triages

In FortiAnalyzer, the **event status** is a metadata field configured within an event handler that determines if a security team needs to take further action. Administrators can **manually specify** the status in the handler's advanced settings or enable the **"Allow FortiAnalyzer to choose"** option to let the system assign it automatically based on log details.

According to the sources, there are **four possible statuses** for an event:

- **Unhandled**: The risk is not contained or mitigated and is **considered open**. This is common for logs where the action was **"pass"** or for Botnet and Indicator of Compromise (IoC) detections. SOC analysts are advised to **prioritize events** with this status.
- **Contained**: The source of the risk has been **isolated**. A typical example is an antivirus log indicating a **quarantine** action.
- **Mitigated**: The security risk has been neutralized, usually because the traffic was **blocked or dropped** by the security device. These events are often acknowledged quickly since the immediate threat was stopped.
- **Blank**: This indicates the event falls into a scenario other than the three listed above. A blank status often occurs when an event is associated with **conflicting logs**, such as those containing both "allow" and "block" actions.

To understand event status, imagine a **hospital triage system**: An **Unhandled** status is like a patient who just arrived and needs immediate surgery; **Contained** is like a patient moved to an isolation ward to prevent spreading a virus; **Mitigated** is a patient who has already been treated and is no longer in danger; and a **Blank** status is like a patient with inconclusive test results who needs a different kind of classification.
## FortiAnalyzer Indicator Intelligence and Mitigation Strategies

**Indicators** are specific data points extracted by FortiAnalyzer from events that could be **harmful or malicious**. There are three primary types of indicators: **IP addresses, URLs, and domains**.

These indicators are generated in two ways:

- **Extraction**: They can be automatically pulled from generated events by configuring filters within **event handler rules**.
- **Manual Creation**: Analysts can also manually add indicators directly on the **Indicator page**.

### Enrichment and Reputation

Once identified, indicators can undergo **enrichment** using FortiGuard and VirusTotal services to evaluate the risk they pose to the network. This enrichment process provides analysts with comprehensive threat intelligence and context, allowing for more informed decision-making. After enrichment, an indicator is assigned one of four **reputation types**:

- **Malicious**
- **Suspicious**
- **Harmless**
- **Undetected**

### Analysis and Mitigation

FortiAnalyzer consolidates all detected indicators into an **interactive Indicators chart** for centralized analysis. This allows SOC analysts to filter items by type or reputation and see detailed information in tabular form.

If an indicator is deemed a threat, analysts can take direct action from FortiAnalyzer:

- **Blocking**: Suspicious indicators can be blocked by pushing them to a **FortiManager External Resource list**. This requires an authorized FortiManager connector and identical firmware versions on both devices.
- **Quarantining**: Analysts can trigger the **quarantine of affected endpoints** directly from the incident analysis page.

To understand indicators, imagine a **security guard at a high-end gala**: The **events** are general reports of people acting strangely, but **indicators** are the specific "fingerprints" left behind, such as a **suspicious ID card (IP address)** or a **sketchy business card (URL)**. **Enrichment** is like the guard running those items through a global police database to see if they belong to known criminals, allowing the guard to then **block** those individuals from future entry.
## FortiAnalyzer Indicator Enrichment and Threat Intelligence Analysis

**Indicator enrichment** is a specialized feature in FortiAnalyzer that provides SOC analysts with **comprehensive threat intelligence** and context regarding identified IP addresses, domains, and URLs. By enriching these artifacts, analysts gain a deeper understanding of security incidents, allowing them to make **more informed and effective decisions** during investigations.

### Enrichment Services and Automation

FortiAnalyzer utilizes two primary external services to evaluate the risks associated with indicators:

- **FortiGuard**: This service is built directly into FortiAnalyzer and is ready for immediate use.
- **VirusTotal**: This third-party service requires the configuration of an **API key** within the FortiAnalyzer Fabric connectors.

To streamline this process, FortiAnalyzer includes a predefined, read-only playbook titled **"Indicator Enrichment"**. This playbook is enabled by default and **executes automatically** every time an indicator is enriched.

### Results and Reputation

Once the enrichment process is complete, the indicator is assigned one of four specific **reputation types** based on the intelligence gathered:

- **Malicious**
- **Suspicious**
- **Harmless**
- **Undetected**

### Operational Details

- **Access**: Analysts can trigger enrichment manually from the **Indicators page** or directly from a specific **incident analysis page**.
- **History**: When an enrichment is saved, the system updates the existing entry in the indicator's history. A entirely new history entry is only created if there are **actual changes** in the enrichment data.
- **Actionable Intelligence**: Consolidating these enriched indicators into centralized charts allows analysts to filter by reputation and take swift mitigation actions, such as **blocking suspicious IP addresses** via FortiManager.

To understand indicator enrichment, imagine a **security guard finding a lost ID card** (**an indicator**) on a corporate campus. On its own, the card tells them very little; however, enrichment is like the guard **running that card through a global police database**. Suddenly, the guard receives a full profile (**threat intelligence**) that identifies if the card belongs to a trusted employee (**harmless**) or a known trespasser (**malicious**), allowing them to decide exactly how to respond.