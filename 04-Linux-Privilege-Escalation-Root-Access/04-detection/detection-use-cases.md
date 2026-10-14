# Detection Use Cases — Project 04

| ID | Use case | Data to correlate | Current status |
|---|---|---|---|
| DET-P04-01 | Cron launches a validation script in privileged context | cron/shell process chain, command line, host proof | Controlled Task 8 behavior observed; alert validation not claimed |
| DET-P04-02 | Scheduled job invokes shell with unusual command line | process name, command line, parent, time | Proposed; requires rule validation |
| DET-P04-03 | Cron configuration file modified | file events for `/etc/cron*`, `/var/spool/cron*` | Proposed; field coverage must be checked |
| DET-P04-04 | SUID/SGID bit changes | file path, mode/permissions, process/user | Proposed; requires file telemetry |
| DET-P04-05 | File capabilities changed | `setcap`/`getcap` activity and file metadata | Proposed; baseline inventory alone is not an alert |
| DET-P04-06 | Privileged process uses unusual writable path | process chain, executable path, ownership/mode | Proposed; requires enrichment and tuning |
| DET-P04-07 | Discovery burst preceding privilege attempt | `find`, `stat`, `getcap`, package/service commands | Hunting analytic; likely noisy without sequence/time thresholds |

## Tuning notes
- Allowlist approved administration and package-maintenance activity only with documented rationale.
- Scope by host role and known maintenance windows.
- Do not alert on `getcap`, `find`, or `stat` alone without context.
- Treat a process event and a detection alert as different evidence types.
