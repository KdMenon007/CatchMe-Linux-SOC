# Detection Logic — SSH Authorized-Key Modification

## Logic flow
1. Restrict events to host `soc-linux`.
2. Require `data_stream.dataset:"auditd.log"`.
3. Require audit key `ssh_authorized_keys`.
4. Require Auditd record type `SYSCALL`.
5. Exclude events whose `event.action` equals `changed-audit-configuration`.

## Recorded query
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.key:"ssh_authorized_keys" AND auditd.log.record_type:"SYSCALL" AND NOT event.action:"changed-audit-configuration"
```

## Expected behavior
A matching Auditd syscall can trigger an alert when the rule runs over the relevant data and its schedule/look-back includes the event. This document does not claim the rule ran against the historical attack window or that a matching alert was generated for that attack.

## Known granularity issue
Prior controlled validation notes report five High alerts from one append-comment/restore file operation. Inspect event timestamps, syscall/audit sequence, alert grouping, and file-path fields before deciding whether tuning is appropriate.

## Current validation gap
No detection-specific screenshots were found in the project screenshot directories during inventory. Capture genuine Elastic Security rule and alert evidence when reviewing the live UI; do not create illustrative screenshots and present them as evidence.
