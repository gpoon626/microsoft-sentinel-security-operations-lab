# Sentinel Data Connection and KQL Investigation

> Status: Draft

## Scenario

A Microsoft Sentinel workspace was reviewed to verify that Microsoft Entra ID telemetry was connected and available for investigation.

The review examined the connector status, enabled identity-log tables, retention settings, UEBA options, and a basic Kusto Query Language query against recent sign-in activity.

## Objectives

* Verify the Microsoft Entra ID data connector
* Confirm that identity logs were reaching Microsoft Sentinel
* Review the available log tables and retention settings
* Examine the connector’s UEBA configuration options
* Use KQL to retrieve recent sign-in activity
* Explain how identity telemetry supports security investigations

## Environment

* Authorized Microsoft 365 and Azure lab tenant
* Microsoft Sentinel
* Log Analytics workspace
* Microsoft Entra ID data connector
* `AuditLogs` table
* `SigninLogs` table
* Kusto Query Language

## Activities and Findings

### 1. Data-Connector Review

The Microsoft Sentinel Data connectors page showed two configured connectors, with both reporting a connected status. The Microsoft Entra ID connector was selected for detailed review.

The connector reported:

* Status: Connected
* Recent log ingestion
* `AuditLogs` enabled
* `SigninLogs` enabled

These findings confirmed that Microsoft Entra ID telemetry was connected to the Sentinel workspace.

### 2. Log-Table and Retention Review

The connector stored both enabled identity tables in the Analytics tier.

The table settings showed:

* `AuditLogs`: 30 days of analytics retention and 30 days of total retention
* `SigninLogs`: 30 days of analytics retention and 30 days of total retention

`AuditLogs` provides visibility into directory and administrative activity. `SigninLogs` provides authentication records that can be searched during identity investigations.

The retention period defines how long the data remains directly available for analysis in the workspace.

### 3. UEBA Configuration Review

The connector’s advanced options displayed User and Entity Behavior Analytics settings for several identity-related tables.

The page indicated that the Sentinel UEBA feature had to be enabled before UEBA could be activated for those tables. UEBA was therefore reviewed as an available enhancement but was not used during this investigation.

UEBA can add behavioral context to identity telemetry, but its absence did not prevent direct investigation of the raw sign-in and audit records with KQL.

### 4. KQL Query Validation

The Sentinel workspace was selected, and the Logs interface was opened. After reviewing the Queries hub, the editor was switched to KQL mode.

The following query was executed:

```kusto
SigninLogs
| top 5 by TimeGenerated desc
```

The query returned five sign-in records from the selected 24-hour time range, ordered from newest to oldest.

This confirmed that:

* The `SigninLogs` table contained searchable data
* Microsoft Entra ID events were reaching the workspace
* KQL could retrieve and sort recent authentication activity

### 5. Security Relevance

Identity telemetry can help analysts investigate activity such as:

* Repeated authentication failures
* Password-spraying or brute-force patterns
* Sign-ins from unexpected locations or devices
* Unusual authentication methods
* Changes to users, groups, roles, and applications

The query used in this investigation did not identify malicious activity. Its purpose was to validate the data source and demonstrate basic access to recent identity events.

More targeted KQL queries, analytics rules, and behavioral analysis would be required to detect and investigate specific threats.

## Assessment

| Area reviewed | Finding | Security significance |
|---|---|---|
| Entra ID connector | Connected | Identity telemetry was available in Sentinel |
| Log ingestion | Recent records received | The connection was actively supplying data |
| Audit logging | `AuditLogs` enabled | Directory and administrative activity could be reviewed |
| Sign-in logging | `SigninLogs` enabled | Authentication activity could be queried |
| Analytics retention | 30 days | Recent identity events remained available for investigation |
| UEBA | Feature required additional enablement | Behavioral analytics were not included in this investigation |
| KQL validation | Five recent sign-in records returned | The identity data was searchable and usable |

## Security Considerations

A connected data source does not by itself guarantee effective threat detection. Analysts must also confirm that the expected tables contain current records and that retention supports the organization’s investigative requirements.

KQL allows analysts to filter, sort, correlate, and summarize large volumes of telemetry. Basic validation queries are useful for confirming data availability before developing threat-hunting queries or analytics rules.

Connector status, ingestion health, retention, detection coverage, and investigation procedures should be reviewed as separate parts of Sentinel operations.

## Outcome

The investigation confirmed that the Microsoft Entra ID connector was connected and supplying identity telemetry to Microsoft Sentinel.

Both `AuditLogs` and `SigninLogs` were available in the Analytics tier with 30-day retention. A KQL query successfully returned the five most recent sign-in records within the selected time range.

This established that the workspace could be used for basic identity-log investigation and provided a foundation for more targeted threat hunting and detection engineering.

## Limitations

* The review covered one authorized Microsoft Sentinel workspace.
* Only the Microsoft Entra ID connector was examined in detail.
* The KQL query was designed to validate data availability rather than detect a specific threat.
* No malicious sign-in activity was identified or claimed.
* UEBA was not enabled or tested during the investigation.
* The findings represent the workspace configuration and available records at the time evidence was collected.

## Skills Demonstrated

* Microsoft Sentinel navigation
* Data-connector validation
* Identity-telemetry review
* Log-retention assessment
* Kusto Query Language
* Microsoft Entra ID log analysis
* Security-data validation
* Technical documentation
