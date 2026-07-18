# Common Cloud Security Tools

## Overview

- Traditional tools can't cover everything in the cloud, so specialized solutions have emerged:
  - **CASB (Cloud Access Security Broker)** — enforces security policies.
  - **CWPP (Cloud Workload Protection Platform)** — protects against malware.
  - **CSPM (Cloud Security Posture Management)** — alerts on misconfigurations.
- We can still set up solid monitoring and get decent SOC coverage using just a SIEM.

---

## Recommended Actions for SIEM-Based Cloud Monitoring

1. **List your clouds** — identify all cloud platforms where your organization stores or processes critical data.
2. **Know the risks** — prepare a plan for a potential cloud vendor breach, even if the likelihood is low.
3. **Enable cloud logs** — turn on vendor-provided cloud audit logs across all identified clouds, including SaaS.
4. **Enable workload logs** — for IaaS, don't forget your VMs need the same logging as on-premises systems.
5. **Collect the logs** — forward logs to your SIEM, since cloud providers don't retain them for long.
6. **Monitor for anomalies** — build SIEM detection rules to alert on suspicious logins and administrative actions.
