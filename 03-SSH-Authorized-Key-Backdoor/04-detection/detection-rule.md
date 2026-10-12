# Detection Rule — Linux SSH Authorized-Key Modification

## Recorded rule metadata
| Property | Recorded value |
|---|---|
| Rule name | `CatchMe - Linux SSH Authorized Keys Modification` |
| Rule type | KQL |
| Severity | High |
| Risk score | 73 |
| ATT&CK technique | T1098.004 — SSH Authorized Keys |
| Host scope | `soc-linux` |
| Dataset | `auditd.log` |

These are values from earlier lab notes. Confirm against the live Elastic Security rule page before publishing them as current.

## Recorded KQL
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.key:"ssh_authorized_keys" AND auditd.log.record_type:"SYSCALL" AND NOT event.action:"changed-audit-configuration"
```

## Detection intent
Identify Auditd syscall events carrying the `ssh_authorized_keys` audit key, excluding the recorded audit-configuration-change action.

## Interpretation limits
- The query matches the configured audit key and event record type; it does not independently prove the target path without relevant path fields.
- One underlying file operation can generate multiple syscall-level alerts.
- The previously recorded five High alerts from one controlled append-comment/restore operation should be documented as alert granularity, not automatically as five distinct incidents.
- The historical attack timeline and a successful public-key login do not, by themselves, prove this detection fired for the attack.

## Change control
No live rule change is requested or performed by this documentation phase. Do not tune the query without inspecting representative alert documents and confirming the cause of any false positives.
