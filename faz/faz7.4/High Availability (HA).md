https://docs.fortinet.com/document/fortianalyzer/7.4.8/administration-guide/506748/high-availability
# High Availability
### FortiAnalyzer High Availability (HA) Overview

**Key Features**

- **Real-time redundancy:** Automatically promotes a secondary unit if the primary fails.
- **Secure synchronization:** Logs, data, and some configurations are synchronized across units.
- **Load distribution:** Secondary units can handle tasks like report generation.

**Cluster Specifications**

- **Maximum size:** Up to 4 units (1 primary + 3 secondary).
- **Hardware requirements:** All units must be from the same FortiAnalyzer series.
- **Visibility:** All units are network-visible.
- **Operation mode:** All must run in the same mode — either _Analyzer_ or _Collector_.

> [!CAUTION] 
> **Licensing:** The cluster adopts the smallest managed-device limit among member licenses.
