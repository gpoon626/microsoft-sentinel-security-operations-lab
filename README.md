# Microsoft Sentinel Security Operations Lab

> Repository Status: Complete

This repository documents hands-on Microsoft Sentinel security operations case studies completed in an authorized Microsoft 365 and Azure lab environment. The project covers data connectors, identity-log analysis, Kusto Query Language, incident correlation, endpoint hunting, detection engineering, automation assessment, and incident reporting.

## Case Studies

| Case study | Status |
| --- | --- |
| [Sentinel Data Connection and KQL Investigation](case-studies/01-sentinel-data-connection-and-kql-investigation.md) | Complete |
| [Sentinel Incident Correlation and Endpoint Hunting](case-studies/02-sentinel-incident-correlation-and-endpoint-hunting.md) | Complete |
| [Sentinel Detection Engineering, Automation, and Reporting](case-studies/03-sentinel-detection-engineering-automation-and-reporting.md) | Complete |

## Case Study Highlights

### Sentinel Data Connection and KQL Investigation

The Microsoft Entra ID data connector was reviewed to confirm that identity telemetry was connected to Microsoft Sentinel and available for investigation.

The assessment confirmed that `AuditLogs` and `SigninLogs` were enabled, stored in the Analytics tier, and configured with 30-day retention. The connector’s User and Entity Behavior Analytics options were reviewed, and KQL was used to retrieve recent sign-in activity from the Sentinel workspace.

[Read the complete case study](case-studies/01-sentinel-data-connection-and-kql-investigation.md)

### Sentinel Incident Correlation and Endpoint Hunting

A Microsoft Defender for Endpoint alert involving suspicious PowerShell behavior was investigated through the unified Microsoft Defender and Microsoft Sentinel experience.

The investigation reviewed the incident queue, attack-story graph, associated entities, correlated alert, process tree, and detection timeline. Defender reported that it detected and terminated the suspicious behavior. Additional KQL queries examined failed Microsoft Entra ID sign-ins and tested endpoint advanced-hunting data availability.

[Read the complete case study](case-studies/02-sentinel-incident-correlation-and-endpoint-hunting.md)

### Sentinel Detection Engineering, Automation, and Reporting

A scheduled Microsoft Sentinel analytics rule was created to identify users with failed Microsoft Entra ID sign-ins.

The rule used KQL to count failed sign-ins by user principal name, ran every five minutes, created incidents from qualifying alerts, and passed validation before being enabled. Microsoft-provided email and Teams playbook templates were reviewed, the workspace was confirmed to have no configured automation rules, and an existing Defender incident was exported as a PDF report.

[Read the complete case study](case-studies/03-sentinel-detection-engineering-automation-and-reporting.md)

## Key Findings

* The Microsoft Entra ID connector was connected and receiving identity telemetry.
* `AuditLogs` and `SigninLogs` were enabled in the Analytics tier with 30-day retention.
* UEBA options were available, but the UEBA feature required separate enablement.
* KQL successfully retrieved recent Microsoft Entra ID sign-in records.
* Defender correlated one endpoint alert with the affected Windows 11 device, user, and PowerShell process.
* The process tree and alert timeline provided execution and detection context for the suspicious PowerShell behavior.
* Defender reported that it detected and terminated the behavior.
* An identity-focused KQL query identified one account with two failed sign-ins during the selected period.
* `DeviceEvents` and `DeviceInfo` returned no records under the selected workspace and time-range conditions.
* A custom failed-sign-in analytics rule was successfully created, validated, and enabled.
* The rule’s threshold could be met by a user with one failed sign-in and would require additional tuning before production use.
* Email and Teams playbook templates were available but were not deployed or executed.
* No automation rules were configured in the workspace.
* Microsoft Defender supported exporting the investigated incident as a structured PDF report.

## Technologies and Concepts

* Microsoft Sentinel
* Microsoft Defender portal
* Microsoft Defender for Endpoint
* Microsoft Entra ID
* Azure Log Analytics
* Windows 11
* Kusto Query Language
* `AuditLogs`
* `SigninLogs`
* `DeviceEvents`
* `DeviceInfo`
* Data connectors
* Log ingestion
* Analytics and retention settings
* User and Entity Behavior Analytics
* Identity-log investigation
* Incident correlation
* Attack-story analysis
* Entity relationships
* Process-tree investigation
* Advanced hunting
* Scheduled analytics rules
* Detection engineering
* Alert thresholds
* Incident creation
* Playbook templates
* Automation rules
* Connector prerequisites
* Incident reporting
* Evidence documentation

## Repository Structure

```text
microsoft-sentinel-security-operations-lab/
├── README.md
├── case-studies/
│   ├── 01-sentinel-data-connection-and-kql-investigation.md
│   ├── 02-sentinel-incident-correlation-and-endpoint-hunting.md
│   └── 03-sentinel-detection-engineering-automation-and-reporting.md
└── evidence/
    ├── sentinel-data-connection-kql/
    │   ├── README.md
    │   └── Supporting screenshots
    ├── sentinel-incident-correlation-endpoint-hunting/
    │   ├── README.md
    │   └── Supporting screenshots
    └── sentinel-detection-engineering-automation-reporting/
        ├── README.md
        ├── Supporting screenshots
        └── Exported incident report
```

## Scope and Ethics

* All activities were performed in an authorized Microsoft 365 and Azure lab environment.
* No production users, endpoints, workspaces, or security policies were modified.
* The investigated endpoint activity occurred on an authorized Windows 11 lab device.
* Identity-query results were evaluated within the selected workspace and time ranges.
* Two failed sign-ins were not treated as proof of brute-force activity or account compromise.
* Zero-result endpoint queries were not interpreted as proof that the endpoint was secure.
* The scheduled analytics rule was created for lab validation and would require additional tuning before production deployment.
* The reviewed playbooks were templates and were not deployed or executed.
* No automated notification or response workflow was activated.
* The exported incident report represented the incident state when the report was generated and did not demonstrate final resolution.
* Findings are limited to the telemetry, configurations, and licensing available in the lab environment.
