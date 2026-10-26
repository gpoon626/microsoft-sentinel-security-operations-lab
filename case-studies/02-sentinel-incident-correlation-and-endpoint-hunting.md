# Sentinel Incident Correlation and Endpoint Hunting

> Status: Complete

## Scenario

A low-severity Microsoft Defender for Endpoint alert was grouped into an incident involving the `win11-lab` endpoint, its associated user, and a PowerShell process.

The investigation reviewed the incident queue, entity graph, correlated alert, process tree, and alert timeline. Additional KQL queries were used to examine failed Microsoft Entra ID sign-ins and determine whether endpoint records were available through advanced hunting.

## Objectives

* Review an endpoint security incident
* Identify the alert and entities associated with the incident
* Examine the process tree and detection timeline
* Determine how Defender responded to the detected behavior
* Use KQL to summarize failed sign-ins
* Search endpoint hunting tables for recent activity
* Document the meaning and limitations of zero-result queries

## Environment

* Authorized Microsoft 365 and Azure lab tenant
* Microsoft Defender portal
* Microsoft Sentinel
* Microsoft Defender for Endpoint
* Microsoft Entra ID sign-in telemetry
* Windows 11 virtual machine named `win11-lab`
* `SigninLogs` table
* `DeviceEvents` table
* `DeviceInfo` table
* Kusto Query Language

## Activities and Findings

### 1. Incident Queue Review

The incident queue contained one incident named `Suspicious 'BmTestOfflineUI' behavior was blocked`.

The incident record showed:

* Severity: Low
* Category: Suspicious activity
* Impacted endpoint: `win11-lab`
* One associated user
* Active alerts: 1 of 1
* Service source: Endpoint
* Detection source: Antivirus
* Product: Microsoft Defender for Endpoint

The incident was active, unassigned, and unclassified when it was reviewed.

### 2. Entity and Alert Correlation

The incident attack story connected the following entities:

* The `win11-lab` endpoint
* The associated user
* A PowerShell process
* One endpoint alert

The incident’s **Alerts** tab confirmed that one low-severity alert had been grouped into the incident. The alert had a status of **New** and was associated with the same endpoint and user.

This correlation allowed the related alert, device, user, and process information to be reviewed from one incident record.

### 3. Process-Tree Investigation

The alert process tree displayed the activity leading to the detected PowerShell process.

The visible process chain included:

* Windows system and logon processes
* `explorer.exe`
* `powershell.exe`
* Multiple PowerShell script-execution entries

The alert details identified:

* Category: Suspicious activity
* Detection source: Antivirus
* Service source: Microsoft Defender for Endpoint
* Detection technology: Client heuristic
* Detection status: Blocked
* Evidence entity: `powershell.exe`
* Evidence verdict: Malicious

The malicious evidence verdict applied to the PowerShell evidence associated with the alert. The incident itself remained unclassified at the time of review.

### 4. Detection Timeline Review

The alert timeline stated that Defender detected and terminated active `Behavior:Win32/BmTestOfflineUI` activity in `powershell.exe` during behavior monitoring.

The alert was displayed as low severity, blocked, and new.

The blocked status confirmed that Defender responded to the observed behavior. It did not remove the need to examine related processes, entities, alerts, and surrounding activity before determining whether additional remediation was required.

### 5. Failed Sign-In Hunting

The following KQL query was used to count unsuccessful sign-ins by user:

```kusto
SigninLogs
| where ResultType != 0
| summarize FailedCount = count() by UserPrincipalName
```

The query returned one account with two failed sign-ins during the selected 24-hour period.

The result demonstrated how KQL can summarize authentication failures and identify accounts that may require additional investigation.

Two failures alone were not sufficient to classify the activity as brute force, password spraying, or account compromise. Additional fields such as source IP address, location, application, authentication method, and event timing would be needed.

### 6. Endpoint Advanced Hunting

The following query searched for the ten most recent endpoint events:

```kusto
DeviceEvents
| top 10 by TimeGenerated desc
```

The query returned no records within the selected 30-day period.

A second query searched for recent endpoint information:

```kusto
DeviceInfo
| top 10 by Timestamp desc
```

This query also returned no records within the selected 30-day period.

The zero-result outcomes established only that the selected tables did not return records under the selected workspace and time-range conditions. They did not establish that the endpoint had no activity or was secure.

The screenshots did not determine whether the missing records were caused by workspace scope, table population, telemetry configuration, retention, or another data-availability issue.

## Assessment

| Area reviewed | Finding | Security significance |
|---|---|---|
| Incident severity | Low | Severity supported prioritization but did not replace investigation |
| Incident classification | Unclassified | An analyst determination had not been recorded |
| Associated alerts | One alert | The incident’s scope was limited to one grouped alert |
| Impacted entities | Endpoint, user, and PowerShell process | Entity correlation provided investigation context |
| Endpoint evidence | PowerShell evidence marked malicious | The process required review within the alert story |
| Defender response | Behavior detected and terminated | The observed behavior was blocked |
| Failed sign-ins | One account with two failures | Additional context was required before classifying the activity |
| `DeviceEvents` query | No records returned | Endpoint-event data was unavailable under the selected conditions |
| `DeviceInfo` query | No records returned | Endpoint-information data was unavailable under the selected conditions |

## Security Considerations

Incident severity should be treated as a prioritization aid rather than a final determination of risk. A low-severity alert can still require investigation when it involves scripting tools such as PowerShell or affects an important endpoint.

Process trees help analysts determine what launched a suspicious process and which related activity occurred before and after the detection. Alert timelines help establish when the activity occurred and how the security control responded.

Identity and endpoint telemetry should be correlated when possible. Authentication failures may provide account context, while endpoint tables may provide device, process, network, and execution details.

Zero-result queries require careful interpretation. They may reflect a lack of matching events, but they may also indicate an incorrect scope, unavailable table, ingestion problem, retention limitation, or telemetry-configuration gap.

## Outcome

The investigation confirmed that Defender grouped a suspicious PowerShell alert with the affected endpoint and user.

The process tree and alert timeline showed the activity chain and confirmed that Defender detected and terminated the behavior. A separate identity query found two failed sign-ins for one account during the selected period.

The endpoint advanced-hunting queries returned no records. This was documented as a data-availability limitation rather than evidence that the endpoint was secure.

The investigation demonstrated how incident correlation, process analysis, identity hunting, and telemetry validation contribute different forms of context during security triage.

## Limitations

* The investigation involved one authorized lab endpoint.
* The incident contained one associated alert.
* The incident remained unclassified during the evidence review.
* The evidence did not establish whether the detected behavior caused additional activity outside the displayed process chain.
* Two failed sign-ins were insufficient to establish malicious authentication activity.
* The cause of the empty `DeviceEvents` and `DeviceInfo` query results was not determined.
* No claim was made that zero query results proved the endpoint was secure.
* The incident was not closed, reassigned, or reclassified as part of the documented investigation.
* The findings represent the available telemetry and selected time ranges when the evidence was collected.

## Skills Demonstrated

* Microsoft Sentinel investigation
* Microsoft Defender for Endpoint alert review
* Incident and entity correlation
* Process-tree analysis
* Detection-timeline analysis
* Kusto Query Language
* Identity-log hunting
* Endpoint advanced hunting
* Telemetry-gap identification
* Evidence-based security documentation

## Evidence

| Evidence | What it demonstrates |
|---|---|
| [Incident attack story](../evidence/sentinel-incident-correlation-endpoint-hunting/01-incident-attack-story-entity-graph.png) | The relationships among the endpoint, user, process, and alert |
| [Incident queue](../evidence/sentinel-incident-correlation-endpoint-hunting/02-sentinel-incident-queue.png) | The incident severity, category, impacted assets, and detection sources |
| [Incident alert correlation](../evidence/sentinel-incident-correlation-endpoint-hunting/03-incident-alert-correlation.png) | The alert grouped into the incident |
| [Alert process tree](../evidence/sentinel-incident-correlation-endpoint-hunting/04-alert-process-tree.png) | The processes leading to the detected PowerShell activity |
| [Detection timeline](../evidence/sentinel-incident-correlation-endpoint-hunting/05-alert-timeline-detection-and-termination.png) | Defender detected and terminated the behavior |
| [Failed sign-in summary](../evidence/sentinel-incident-correlation-endpoint-hunting/06-failed-signin-summary-by-user.png) | One account had two failed sign-ins during the selected period |
| [DeviceEvents hunting query](../evidence/sentinel-incident-correlation-endpoint-hunting/07-advanced-hunting-deviceevents-no-results.png) | No device-event records were returned under the selected conditions |
| [DeviceInfo hunting query](../evidence/sentinel-incident-correlation-endpoint-hunting/08-advanced-hunting-deviceinfo-no-results.png) | No device-information records were returned under the selected conditions |

Additional descriptions and full-size screenshots are available in the [evidence documentation](../evidence/sentinel-incident-correlation-endpoint-hunting/README.md).
