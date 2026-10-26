# Sentinel Data Connection and KQL Evidence

This folder documents the review of the Microsoft Entra ID data connector, identity-log tables, retention settings, UEBA options, Microsoft Sentinel workspace, and KQL query results.

The evidence was reviewed through the unified Microsoft Defender security experience and the Microsoft Sentinel Logs interface in the Azure portal.

## Evidence

### 1. Connected Data Connectors

![Connected data connectors](01-sentinel-connected-data-connectors.png)

The Microsoft Sentinel Data connectors page displayed two configured connectors:

* Microsoft 365 Insider Risk Management
* Microsoft Entra ID

Both connectors reported a connected status.

The Microsoft Entra ID connector was selected for further review because it supplied the identity telemetry used in the investigation.

A connected status confirms that a connector has been configured. It does not independently confirm that every available data type is enabled, that records are current, or that detection rules have been created.

### 2. Microsoft Entra ID Connector Status and Log Tables

![Microsoft Entra ID connector status and log tables](02-entra-id-connector-status-and-log-tables.png)

The Microsoft Entra ID connector displayed:

* Status: Connected
* Recent log receipt
* `AuditLogs` enabled
* `SigninLogs` enabled
* Two related queries
* No related workbooks
* No related analytics-rule templates

The connector’s data-received graph showed activity from the enabled identity tables.

The table-management section showed that both tables used the Analytics tier with 30 days of analytics retention and 30 days of total retention.

This evidence confirmed that the connector was configured and that the two enabled identity tables were receiving data. It did not demonstrate that every available Entra ID log type was enabled.

### 3. UEBA Configuration Options

![UEBA configuration options](03-entra-id-connector-ueba-options.png)

The connector’s advanced options displayed User and Entity Behavior Analytics settings for:

* Managed Identity Sign-in Logs
* Service Principal Sign-in Logs
* Audit Logs
* Sign-in Logs

The page indicated that the Microsoft Sentinel UEBA feature had to be enabled before UEBA could be activated for the connector’s tables.

UEBA was therefore available as a configuration option but was not enabled or tested during this assessment.

The disabled UEBA state did not prevent direct KQL searches of the available `AuditLogs` and `SigninLogs` data. It meant that behavioral profiles, anomaly context, and UEBA-based entity insights were not part of the investigation.

### 4. AuditLogs Table Details

![AuditLogs table details](04-auditlogs-table-retention-details.png)

The `AuditLogs` table-detail panel displayed:

* Tier: Analytics
* Analytics retention: 30 days
* Total retention: 30 days
* Data lake: Integrated
* Scope tag rule: None
* Data collection rules: Supported

The table description identified `AuditLogs` as an audit log for Microsoft Entra ID containing system activity involving users, groups, managed applications, and directory operations.

This table can support investigations into administrative actions and identity-object changes.

The screenshot confirmed the displayed table configuration. It did not demonstrate that every type of administrative event was present or that any suspicious directory activity had occurred.

### 5. SigninLogs Table Details

![SigninLogs table details](05-signinlogs-table-retention-details.png)

The `SigninLogs` table-detail panel displayed:

* Tier: Analytics
* Analytics retention: 30 days
* Total retention: 30 days
* Data lake: Integrated
* Scope tag rule: None
* Data collection rules: Supported

`SigninLogs` contains Microsoft Entra ID authentication activity that can be searched during identity investigations.

The 30-day analytics-retention period defined how long the displayed configuration kept the records directly available for interactive analysis.

The screenshot documented the table settings. It did not establish whether the retention period was sufficient for every investigation or compliance requirement.

### 6. Sentinel Workspace Selection

![Sentinel workspace selection](06-sentinel-workspace-selection.png)

The Microsoft Sentinel workspace `FTJ-Sentinel-WS` was identified and selected in the Azure portal.

Selecting the correct workspace was necessary because Sentinel queries operate against the data available within the selected workspace’s scope.

This screenshot established which workspace was used for the subsequent log query.

### 7. Sentinel Logs and Queries Hub

![Microsoft Sentinel Queries hub](07-sentinel-logs-queries-hub.png)

The Microsoft Sentinel Logs interface opened with the Queries hub displayed.

The Queries hub provided:

* Predefined query categories
* Example queries
* Query-pack selection
* Access to the query editor

The interface supplied starting points for investigating available workspace data. A custom query was used for this assessment rather than one of the displayed example queries.

### 8. KQL Query Mode

![KQL query mode](08-sentinel-logs-kql-mode.png)

The Logs interface was switched to KQL mode so a custom Kusto Query Language query could be entered directly.

The query editor was configured with:

* A 24-hour time range
* A maximum display of 1,000 results
* KQL mode selected

This established the interface and time range used for the sign-in data validation.

No query results were displayed at this stage because the query had not yet been executed.

### 9. Sign-In Log Query Results

![Sign-in log query results](09-signinlogs-kql-query-results.png)

The following query searched the `SigninLogs` table for the five most recent records:

```kusto
SigninLogs
| top 5 by TimeGenerated desc
```

The query returned five sign-in records from the selected 24-hour period.

The visible results included fields such as:

* `TimeGenerated`
* `ResourceId`
* `OperationName`
* `OperationVersion`
* `Category`
* `ResultType`

The returned rows confirmed that:

* The `SigninLogs` table contained searchable records.
* Microsoft Entra ID sign-in telemetry was available in the selected workspace.
* KQL could retrieve and sort authentication records by event time.

This was a data-availability validation query. It was not designed to identify a specific threat or determine whether the returned sign-ins were suspicious.

## Key Findings

* Two Microsoft Sentinel data connectors reported a connected status.
* The Microsoft Entra ID connector was connected and displayed recent log receipt.
* `AuditLogs` and `SigninLogs` were enabled.
* Both identity tables used the Analytics tier.
* Both tables displayed 30 days of analytics retention and 30 days of total retention.
* The tables showed integrated data-lake support and support for data-collection rules.
* UEBA required separate feature enablement and was not active during the assessment.
* The `FTJ-Sentinel-WS` workspace was used for the log investigation.
* The Logs interface supported predefined queries and direct KQL entry.
* The custom KQL query returned five recent Microsoft Entra ID sign-in records.
* The query confirmed identity-data availability but did not validate threat-detection coverage.

## Limitations

* The assessment involved one authorized Microsoft Sentinel workspace.
* Only the Microsoft Entra ID connector was examined in detail.
* Only `AuditLogs` and `SigninLogs` were enabled in the reviewed connector configuration.
* UEBA was not enabled or tested.
* The query reviewed only five records from the selected 24-hour period.
* The query was designed to validate data availability rather than detect suspicious activity.
* No malicious sign-in activity was identified or claimed.
* Analytics rules, alerts, and automated responses were not tested as part of this evidence set.
* The assessment did not evaluate historical data beyond the displayed 30-day retention configuration.
* The findings represent the connector configuration and data available when the evidence was collected.
