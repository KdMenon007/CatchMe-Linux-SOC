# Detection Overview — SSH Authorized-Key Backdoor

## Objective
Document the existing Elastic detection for suspicious changes to SSH authorized keys and define what evidence is needed to validate it.

## Existing live rule — recorded configuration
The previously reviewed configuration was recorded as:
- **Name:** `CatchMe - Linux SSH Authorized Keys Modification`
- **Severity:** High
- **Risk score:** 73
- **Rule type:** KQL
- **MITRE ATT&CK:** T1098.004 — SSH Authorized Keys
- **Target host:** `soc-linux`
- **Data source:** Auditd log events

The live rule has not been queried or revalidated by this documentation update. Treat these properties as the previously recorded configuration, not as a fresh live check.

## Recorded rule query
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.key:"ssh_authorized_keys" AND auditd.log.record_type:"SYSCALL" AND NOT event.action:"changed-audit-configuration"
```

## Known validation history
Prior lab notes recorded five High alerts from one controlled append-comment/restore file operation. This reflects syscall-level alert granularity and does not by itself prove the alert's file-path attribution or detection precision. Do not retest or tune the live rule solely to reproduce that count.

## Evidence gap
The Detection screenshot directory contains four screenshots: a historical authorized-keys path search with no results, a historical syscall sequence, and Open/Closed alert-status views with no results for the reviewed window. These screenshots document investigation limitations; they do not establish that an alert fired for the historical attack. The query index is maintained at `10-Queries/README.md`; no separate detection-specific KQL file was identified in the searched project locations.

## Required validation evidence
1. Elastic Security rule details showing the rule name, enabled status, query, severity, risk score, and MITRE mapping.
2. Alerts view showing a representative alert, timestamp, host, rule name, and event details.
3. Alert event fields sufficient to assess file-path attribution and event granularity.
4. Any controlled validation evidence already retained; do not rerun a test unless necessary and explicitly approved.
5. Document whether the evidence is historical, current, or not available.

## Status
**Documented from prior records; current live rule state and historical attack alert outcome not independently verified in this phase.**

## Historical alert-status review

For the selected Oct 3, 2026 14:10–14:50 time range, Elastic Security displayed no results in the Open, Acknowledged, or Closed alert views. Recent rule executions succeeded but created zero alerts. This is a scoped observation, not proof that an alert could not have existed outside the selected range or view. Historical detection outcome remains **not established**. Indexed authorized-keys file-path correlation also remains unresolved.
