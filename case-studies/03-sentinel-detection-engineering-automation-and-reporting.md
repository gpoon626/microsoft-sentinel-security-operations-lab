# Sentinel Detection Engineering, Automation, and Reporting

> Status: Draft

## Scenario

A security operations team needed a repeatable method for detecting failed Microsoft Entra ID sign-ins, reviewing available incident-notification automation, and preserving incident information for reporting.

Microsoft Sentinel was used to create a scheduled analytics rule based on `SigninLogs`. Microsoft-provided playbook templates were then reviewed to understand their triggers, connector requirements, and post-deployment configuration. Finally, an existing Microsoft Defender incident was exported as a PDF to document the available incident-reporting workflow.

## Objectives

* Review the existing Microsoft Sentinel analytics-rule inventory.
* Create a scheduled analytics rule for failed Microsoft Entra ID sign-ins.
* Configure the rule query, execution frequency, lookback period, severity, and incident settings.
* Validate and enable the analytics rule.
* Review email and Microsoft Teams playbook templates.
* Identify the connectors and accounts required by the templates.
* Confirm whether automation rules were configured in the workspace.
* Export an existing Microsoft Defender incident as a PDF report.
* Document detection and automation limitations without overstating the results.

## Environment

* Microsoft Sentinel
* Microsoft Defender portal
* Microsoft Entra ID
* Log Analytics workspace: `ftj-sentinel-ws`
* `SigninLogs` table
* Kusto Query Language
* Microsoft Sentinel Analytics
* Microsoft Sentinel Automation
* Microsoft Sentinel playbook templates
* Office 365 Outlook connector requirements
* Microsoft Teams connector requirements
* Microsoft Defender for Endpoint
* Windows 11 lab endpoint: `win11-lab`

## Activities and Findings

### 1. Reviewed the Existing Analytics Rules

The Microsoft Sentinel Analytics page initially displayed one active custom rule named `Test Failed Logins Rule`.

The rule was medium severity, scheduled, and enabled. Reviewing the existing rules established a baseline before another failed-sign-in detection was added.

### 2. Created a Scheduled Failed Sign-In Rule

A new scheduled analytics rule was configured with the following general settings:

* Name: `FL-Gavin.Poon`
* Severity: Medium
* Status: Enabled
* Description: Identifies users with failed Microsoft Entra sign-in attempts by counting failed sign-ins per user principal name.

No MITRE ATT&CK mapping was selected during the demonstrated configuration.

### 3. Defined the KQL Detection Logic

The rule used the following query:

```kusto
SigninLogs
| where ResultType != 0
| summarize FailedCount = count() by UserPrincipalName
```

The query filters Microsoft Entra ID sign-in records for unsuccessful authentication attempts and counts those failures for each user principal name.

The query produces one result row for each user with failed sign-in activity. The `FailedCount` field represents the number of failures associated with that user during the selected query period.

### 4. Configured the Rule Schedule and Threshold

The analytics rule was configured to:

* Run every five minutes.
* Search the previous five minutes of data.
* Start automatically.
* Generate an alert when the query returned more than zero results.
* Trigger an alert for each returned event.
* Create incidents from generated alerts.

The threshold applied to the number of query-result rows rather than the value of `FailedCount`. A user with one failed sign-in could therefore produce a result row and meet the configured alert condition.

This configuration demonstrated the mechanics of scheduled detection but would require additional tuning before production use.

### 5. Validated and Enabled the Rule

Microsoft Sentinel displayed **Validation passed** before the rule was saved.

The validation summary confirmed:

* Medium severity
* Enabled status
* Five-minute execution frequency
* Five-minute lookback period
* Alert threshold greater than zero results
* Incident creation enabled
* Alert grouping disabled
* Tenant-default incident correlation
* No configured suppression
* No configured entity mapping
* No configured custom details
* No configured alert details
* No configured automation rules

After creation, the Analytics page displayed two active rules, including the new `FL-Gavin.Poon` rule.

This confirmed that the rule was successfully created and enabled. The available evidence did not show the rule generating an alert or incident.

### 6. Reviewed the Playbook Template Catalog

The Microsoft Sentinel Automation page displayed 23 playbook templates for the selected workspace.

Available templates covered activities such as:

* Email notifications
* Microsoft Teams notifications
* Incident-owner notifications
* Incident enrichment
* Incident management
* Utility actions

The templates provided potential starting points for automated workflows. Reviewing a template did not deploy or activate it.

### 7. Reviewed the Basic Incident Email Template

The **Send basic email** template was reviewed.

The template used a Microsoft Sentinel incident trigger and required:

* A Microsoft Sentinel connector
* An Office 365 Outlook connector
* An Office 365 account with a mailbox
* Post-deployment Outlook connection configuration
* Playbook enablement
* Assignment through an automation rule

The evidence showed the template requirements but did not show that the playbook was created, enabled, attached, or executed.

### 8. Reviewed the Teams Incident Owner Notification Template

A playbook template designed to notify a new incident owner through Microsoft Teams was reviewed.

The template required:

* A Microsoft Sentinel incident trigger
* A Microsoft Sentinel connector
* A Microsoft Teams connector
* A Microsoft Teams account authorized to send messages

The template could support incident-assignment notifications after deployment and authorization. No Teams message or deployed playbook was demonstrated.

### 9. Reviewed the Formatted Incident Email Template

A formatted incident email template was reviewed.

The template was designed to send an HTML-formatted incident report containing information such as:

* Incident title
* Severity
* Tactics
* Incident link

The template required Microsoft Sentinel and Office 365 Outlook connectors, an Office 365 mailbox, and post-deployment connection configuration.

The instructions also stated that the playbook would be disabled by default and would need to be enabled before assignment to an automation rule.

### 10. Confirmed the Automation-Rule Status

The Automation Rules page displayed zero configured standard automation rules.

This meant that no automation rule was available to invoke the reviewed playbook templates.

The evidence therefore demonstrated automation planning and template evaluation, not an active automated response workflow.

### 11. Reviewed the Incident Summary

An existing Microsoft Defender incident involving suspicious PowerShell behavior was reviewed.

The incident summary showed:

* Severity: Low
* Status: Active
* Assignment: Unassigned
* Classification: Not set
* Category: Suspicious activity
* Active alerts: 1 of 1
* Impacted devices: 1
* Impacted users: 1
* Process evidence: 1
* Impacted endpoint: `win11-lab`
* Associated user: `Gavin`

The incident contained a malicious `powershell.exe` process with a blocked remediation status.

### 12. Exported the Incident Report

The incident was exported as a PDF from the Microsoft Defender incident action menu.

The exported report contained:

* Incident overview
* Attack story
* Scope
* Impacted assets
* Evidence and response
* Supporting alert data

The report preserved the relationships between the alert, endpoint, user, and malicious PowerShell process.

The export represented the incident state at the time it was generated. The incident remained active, unassigned, and unclassified, so the report did not demonstrate final incident resolution.

## Assessment

| Area | Observed State | Assessment |
|---|---|---|
| Data source | `SigninLogs` | Appropriate for detecting Microsoft Entra ID authentication failures. |
| Detection query | Filters nonzero `ResultType` values and counts failures by user | Provides a basic user-level failed-sign-in summary. |
| Query frequency | Every five minutes | Supports frequent detection evaluation. |
| Lookback period | Previous five minutes | Matches the execution frequency but does not provide overlap for delayed ingestion. |
| Alert threshold | More than zero returned rows | Could generate an alert after a single failed sign-in. |
| Incident creation | Enabled | Allows qualifying alerts to create incidents. |
| Alert grouping | Disabled | Multiple alerts could increase incident volume. |
| Entity mapping | Not configured | Limits account enrichment and entity-based investigation. |
| MITRE ATT&CK mapping | Not configured | Limits technique-alignment and detection-coverage reporting. |
| Suppression | Not configured | Repeated matching activity could continue generating alerts. |
| Playbook templates | Available and reviewed | Demonstrates available automation options but not deployment. |
| Automation rules | None configured | No automated action was active. |
| Incident reporting | PDF export completed | Preserved the existing incident details in a portable report. |

## Security Considerations

### Detection Threshold

The rule should be tuned to require a meaningful number of failures before generating an alert. For example, the query could apply a condition such as:

```kusto
SigninLogs
| where ResultType != 0
| summarize FailedCount = count() by UserPrincipalName
| where FailedCount >= 5
```

The final threshold should be based on expected authentication behavior and organizational risk tolerance.

### Authentication Context

Failed sign-ins should be reviewed with additional fields such as:

* Failure reason
* Source IP address
* Geographic location
* Application
* Device information
* Authentication method
* Conditional Access result
* Time between attempts

This context helps distinguish ordinary password mistakes from password spraying, brute-force activity, or account compromise.

### Entity Mapping

Mapping `UserPrincipalName` to an account entity would improve enrichment and make it easier to connect the alert to other identity activity.

### Detection Coverage

MITRE ATT&CK mappings should be added only when they accurately represent the detection objective and observed behavior.

### Alert Management

Alert grouping and suppression should be tested to reduce duplicate alerts without hiding meaningful activity.

### Automation Permissions

Email and Teams playbooks require authenticated connector accounts. Those accounts should use only the permissions required for the workflow and should be reviewed before deployment.

### Automation Testing

A playbook should be tested in the lab before being enabled for active incidents. Testing should confirm:

* The correct trigger
* Connector authorization
* Expected recipients
* Message contents
* Error handling
* Automation-rule conditions
* Successful execution history

### Incident Reporting

Exported reports may contain user, device, process, and incident details. Access to these reports should be limited according to the sensitivity of the investigation.

## Outcome

A custom Microsoft Sentinel scheduled analytics rule was created, validated, and enabled to identify users with failed Microsoft Entra ID sign-ins.

The lab also documented the available playbook templates for email and Teams notifications, the prerequisites required to use them, and the absence of configured automation rules.

An existing Microsoft Defender incident was exported as a PDF, demonstrating how incident information can be preserved for reporting and review.

The completed work demonstrated detection configuration, automation assessment, and incident reporting. It did not demonstrate an alert generated by the new rule, a deployed playbook, or an executed automated response.

## Limitations

* The analytics-rule evidence did not show an alert or incident generated by the new rule.
* The query counted all failed sign-ins without distinguishing different failure reasons.
* The alert threshold could be met by one failed sign-in.
* The five-minute lookback period did not include overlap for possible ingestion delays.
* Entity mapping, MITRE ATT&CK mapping, custom details, alert details, and suppression were not configured.
* Alert grouping was disabled.
* The reviewed playbooks were templates and were not shown as deployed or executed.
* Connector authorization and notification delivery were not tested.
* No automation rules were configured.
* The exported Microsoft Defender incident was not shown to have been generated by the new failed-sign-in analytics rule.
* The exported incident remained active, unassigned, and unclassified.
* The findings represent the available lab data and configuration state when the evidence was collected.

## Skills Demonstrated

* Microsoft Sentinel analytics-rule configuration
* Kusto Query Language
* Microsoft Entra ID sign-in analysis
* Scheduled detection engineering
* Detection-threshold assessment
* Incident-creation configuration
* Microsoft Sentinel playbook-template review
* Automation prerequisite analysis
* Connector dependency review
* Microsoft Defender incident review
* Incident-report generation
* Security-control documentation
* Detection and automation gap identification

## Evidence

| Evidence | What It Demonstrates |
|---|---|
| [Analytics rules before creation](../evidence/sentinel-detection-engineering-automation-reporting/01-analytics-rules-before-creation.png) | Establishes the analytics-rule baseline before the new rule was created. |
| [Failed sign-in rule general settings](../evidence/sentinel-detection-engineering-automation-reporting/02-failed-signin-rule-general-settings.png) | Shows the new rule’s name, description, severity, and enabled status. |
| [Failed sign-in rule query and schedule](../evidence/sentinel-detection-engineering-automation-reporting/03-failed-signin-rule-query-and-schedule.png) | Documents the KQL detection logic, schedule, lookback period, and threshold. |
| [Failed sign-in rule validation summary](../evidence/sentinel-detection-engineering-automation-reporting/04-failed-signin-rule-validation-summary.png) | Confirms that the rule configuration passed validation. |
| [Enabled failed-sign-in analytics rule](../evidence/sentinel-detection-engineering-automation-reporting/05-enabled-failed-signin-analytics-rule.png) | Confirms that the new rule was created and enabled. |
| [Sentinel playbook template catalog](../evidence/sentinel-detection-engineering-automation-reporting/06-sentinel-playbook-template-catalog.png) | Shows the available playbook templates for the workspace. |
| [Basic incident email playbook template](../evidence/sentinel-detection-engineering-automation-reporting/07-basic-incident-email-playbook-template.png) | Documents the basic email template and its connector requirements. |
| [Teams incident-owner notification template](../evidence/sentinel-detection-engineering-automation-reporting/08-teams-incident-owner-notification-template.png) | Documents the Teams notification template and its prerequisite. |
| [Formatted incident email report template](../evidence/sentinel-detection-engineering-automation-reporting/09-formatted-incident-email-report-template.png) | Documents the formatted email template and its configuration requirements. |
| [No automation rules configured](../evidence/sentinel-detection-engineering-automation-reporting/10-no-automation-rules-configured.png) | Confirms that no automation rule was active in the workspace. |
| [Incident summary export to PDF](../evidence/sentinel-detection-engineering-automation-reporting/11-incident-summary-export-to-pdf.png) | Shows the incident summary and PDF export function. |
| [Exported incident report](../evidence/sentinel-detection-engineering-automation-reporting/12-exported-sentinel-incident-report.pdf) | Provides the exported incident overview, attack story, scope, evidence, and supporting data. |

Additional descriptions and context are available in the [evidence documentation](../evidence/sentinel-detection-engineering-automation-reporting/README.md).
