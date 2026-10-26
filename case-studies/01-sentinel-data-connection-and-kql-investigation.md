# Sentinel Data Connection and KQL Investigation

> Status: Complete

## Scenario

A Microsoft Sentinel workspace was reviewed to verify that Microsoft Entra ID identity telemetry was connected, retained, and available for investigation.

The assessment examined the connector status, enabled log tables, retention settings, UEBA options, and a Kusto Query Language query against recent sign-in activity.

## Objectives

* Verify the Microsoft Entra ID data connector
* Confirm that identity logs were reaching Microsoft Sentinel
* Review the available identity tables
* Examine analytics and total retention settings
* Review the connector’s UEBA options
* Use KQL to retrieve recent sign-in activity
* Identify the security value and limitations of the available telemetry

## Environment

* Authorized Microsoft 365 and Azure lab tenant
* Microsoft Sentinel
* Microsoft Defender portal
* Azure portal
* Log Analytics workspace named `ftj-sentinel-ws`
* Microsoft Entra ID data connector
* `AuditLogs` table
* `SigninLogs` table
* Kusto Query Language

## Activities and Findings

### 1. Data-Connector Review

The Microsoft Sentinel Data connectors page showed two configured connectors, with both reporting a connected status.

The Microsoft Entra ID connector was selected for detailed review. Its connector page showed:

* Status: Connected
* Recent log receipt
* `AuditLogs` enabled
* `SigninLogs` enabled
* Two related queries
* No related workbooks
* No analytics-rule templates

The connected status and displayed receipt times confirmed that Microsoft Entra ID telemetry had reached the Sentinel workspace.

A connected status alone does not confirm that every available identity data type is enabled or that detection rules have been configured. The individual tables and their data still required validation.

### 2. Identity-Table Review

The Microsoft Entra ID connector displayed two enabled tables:

* `AuditLogs`
* `SigninLogs`

`AuditLogs` provides records of directory and administrative activity, such as changes involving users, groups, applications, and roles.

`SigninLogs` provides authentication records that can be searched during identity investigations.

The table-detail panels showed that both tables used:

* Analytics tier
* 30 days of analytics retention
* 30 days of total retention
* Integrated data-lake support
* Supported data-collection rules

The retention period defined how long the records remained directly available under the displayed configuration.

### 3. UEBA Configuration Review

The connector’s advanced options displayed User and Entity Behavior Analytics settings for several identity-related tables, including:

* Audit Logs
* Sign-in Logs
* Managed Identity Sign-in Logs
* Service Principal Sign-in Logs

The page indicated that the Sentinel UEBA feature had to be enabled before UEBA could be activated for the connector tables.

UEBA was therefore reviewed as an available capability but was not enabled or tested during this investigation.

This did not prevent direct searches of the available `AuditLogs` and `SigninLogs` data with KQL. It meant that behavioral analytics and entity-based anomaly context were not part of the assessment.

### 4. Sentinel Workspace and Logs Review

The `ftj-sentinel-ws` workspace was selected in Microsoft Sentinel, and the Logs interface was opened.

The Queries hub displayed predefined query resources and provided access to the query editor. The editor was then switched to KQL mode so a custom query could be entered directly.

This established the workspace and interface used for the log investigation.

### 5. KQL Query Validation

The following query was executed:

```kusto
SigninLogs
| top 5 by TimeGenerated desc
```

The query returned five sign-in records from the selected 24-hour period, ordered from newest to oldest.

The results confirmed that:

* The `SigninLogs` table contained searchable records
* Microsoft Entra ID sign-in telemetry was available in the workspace
* KQL could retrieve and sort authentication activity by event time

This was a data-availability validation query. It was not designed to detect a specific threat or determine whether the returned sign-ins were malicious.

### 6. Security Relevance

Microsoft Entra ID telemetry can support investigations involving:

* Repeated authentication failures
* Password-spraying or brute-force patterns
* Sign-ins from unexpected locations
* Unusual devices or applications
* Authentication-method changes
* User, group, role, and application changes

The connector supplies the underlying telemetry, but effective monitoring also requires appropriate queries, analytics rules, alert logic, retention, and investigation procedures.

No suspicious or malicious activity was identified from the basic five-record query used in this assessment.

## Assessment

| Area reviewed | Finding | Security significance |
|---|---|---|
| Data connectors | Two connectors reported connected | Sentinel had active data-source connections |
| Entra ID connector | Connected with recent log receipt | Identity telemetry was reaching the workspace |
| Audit logging | `AuditLogs` enabled | Directory and administrative changes could be reviewed |
| Sign-in logging | `SigninLogs` enabled | Authentication activity could be investigated |
| Table tier | Analytics | The tables were available for interactive analysis |
| Analytics retention | 30 days | Recent identity records remained directly searchable |
| Total retention | 30 days | No additional retained period was displayed beyond analytics retention |
| UEBA | Required separate feature enablement | Behavioral analytics were not included in the investigation |
| KQL validation | Five recent sign-in records returned | The identity data was searchable and sortable |
| Detection validation | Not performed | The query confirmed data availability but not alert coverage |

## Security Considerations

A connected connector does not automatically provide complete detection coverage. Administrators must confirm that expected tables are enabled, records are current, retention supports investigative needs, and appropriate analytics rules are configured.

Log retention affects how far analysts can investigate historical activity. A 30-day retention period may support recent investigations but may not cover incidents discovered after a longer delay.

KQL provides direct access to the collected telemetry. Analysts can use it to filter, sort, summarize, and correlate records, but query results must be interpreted within the selected tables, time ranges, and conditions.

UEBA can add behavioral and entity context to investigations. Because UEBA was not enabled or tested here, no claims were made about anomaly detection or behavioral risk scoring.

## Outcome

The investigation confirmed that the Microsoft Entra ID connector was connected and supplying identity telemetry to the Microsoft Sentinel workspace.

Both `AuditLogs` and `SigninLogs` were enabled in the Analytics tier with 30-day analytics and total retention.

A KQL query successfully returned the five most recent sign-in records within the selected 24-hour period. This validated that Microsoft Entra ID sign-in data was available for investigation.

The review also identified that UEBA required separate enablement and that connector health, table availability, retention, behavioral analytics, and detection coverage must be evaluated separately.

## Limitations

* The assessment involved one authorized Microsoft Sentinel workspace.
* Only the Microsoft Entra ID connector was examined in detail.
* The query was designed to validate data availability rather than detect a specific threat.
* Only five recent sign-in records were reviewed.
* No malicious sign-in activity was identified or claimed.
* UEBA was not enabled or tested.
* Analytics rules and automated detections were not tested.
* The investigation did not evaluate long-term retention beyond the displayed 30-day configuration.
* The findings represent the configuration and available data when the evidence was collected.

## Skills Demonstrated

* Microsoft Sentinel administration
* Data-connector validation
* Microsoft Entra ID telemetry review
* Log-table assessment
* Retention analysis
* Kusto Query Language
* Identity-log investigation
* Telemetry validation
* Security-control gap identification
* Evidence-based technical documentation

## Evidence

| Evidence | What it demonstrates |
|---|---|
| [Connected data connectors](../evidence/sentinel-data-connection-kql/01-sentinel-connected-data-connectors.png) | Two configured Sentinel connectors reported connected status |
| [Entra ID connector status and tables](../evidence/sentinel-data-connection-kql/02-entra-id-connector-status-and-log-tables.png) | The Entra ID connector was connected and the identity tables were enabled |
| [UEBA configuration options](../evidence/sentinel-data-connection-kql/03-entra-id-connector-ueba-options.png) | UEBA required separate feature enablement |
| [AuditLogs retention details](../evidence/sentinel-data-connection-kql/04-auditlogs-table-retention-details.png) | The audit table used the Analytics tier with 30-day retention |
| [SigninLogs retention details](../evidence/sentinel-data-connection-kql/05-signinlogs-table-retention-details.png) | The sign-in table used the Analytics tier with 30-day retention |
| [Sentinel workspace selection](../evidence/sentinel-data-connection-kql/06-sentinel-workspace-selection.png) | The workspace selected for the investigation |
| [Sentinel Queries hub](../evidence/sentinel-data-connection-kql/07-sentinel-logs-queries-hub.png) | The Logs interface and available query resources |
| [KQL query mode](../evidence/sentinel-data-connection-kql/08-sentinel-logs-kql-mode.png) | The query editor configured for KQL |
| [Sign-in query results](../evidence/sentinel-data-connection-kql/09-signinlogs-kql-query-results.png) | The query returned five recent sign-in records |

Additional descriptions and full-size screenshots are available in the [evidence documentation](../evidence/sentinel-data-connection-kql/README.md).
