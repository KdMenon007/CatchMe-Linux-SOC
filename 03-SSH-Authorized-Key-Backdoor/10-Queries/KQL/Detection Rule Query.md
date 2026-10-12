# Detection Rule Query

**Purpose:** Record the Project 03 authorized-keys detection rule and related hunts.

## Current documented rule logic

```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.key:"ssh_authorized_keys" AND auditd.log.record_type:"SYSCALL" AND NOT event.action:"changed-audit-configuration"
```

## Interpretation
- This is the documented rule query, not a claim that every historical modification generated an alert.
- The rule's actual status, execution history, and alert results must be checked in Kibana.
- Preserve the rule's schedule, lookback, risk score, and exceptions as observed in the live configuration; do not change them from this document.
- Record the time range, execution status, and alert identifiers for each validation.

**Historical outcome:** The Project 03 notes describe the historical alert outcome as unresolved. Do not infer success or failure from this query text alone.
