# Telemetry Requirements — Project 04

## Purpose
Define what should be collected and validated in Elastic for the Linux privilege-escalation assessment. Requirements describe desired evidence; they do not assert that every source or field was available.

## Expected sources
| Source | Useful evidence | Notes |
|---|---|---|
| Elastic Defend process events | Process name, command line, parent, user, execution events | Some observed events lacked command-line or parent fields |
| Linux authentication logs | SSH authentication, sudo authorization, account context | Validate the exact data view and fields |
| Auditd/security logs, if ingested | Relevant syscalls and file/security events | Confirm source and event coverage before relying on it |
| Host-side validation log | Root identity proof from Task 8 controlled test | Preserve original and sanitize copies if needed |
| File metadata output | Ownership, mode, package ownership, timestamps | Terminal output must be stored as evidence if needed for publication |

## Minimum event fields
Where present, retain:
- `@timestamp`
- `host.name`
- `user.name`
- `process.name`
- `process.command_line`
- `process.parent.name`
- `process.user.name`
- `process.real_user.name`
- `event.category`
- `event.action`
- `event.outcome`
- `message`

Do not treat missing fields as proof that an event did not occur. Record null or absent fields as a telemetry limitation.

## Time-range requirements
- Use `Asia/Kolkata` (IST, UTC+05:30) for the lab record.
- Use an absolute time range that covers the actual event timestamps.
- For Task 8, observed cron validation process events occurred around 12:37–12:48 IST on 2026-10-13; a broader query window of 12:37–12:49 IST was used to review the repeated execution evidence.
- For later enumeration tasks, use the event timestamps shown in the corresponding Elastic screenshot. Do not reuse Task 8's window for unrelated checks.
- If the command output has no timestamp, retrieve the time from terminal history or Elastic events; otherwise label the exact time as unavailable.

## Task 8 example query
```kql
host.name:"soc-linux" and event.category:process and
(
  process.command_line:"*p04-cron-validation.sh*" or
  process.command_line:"*P04_CRON_PRIVESC_VALIDATION*" or
  process.command_line:"*P04_ROOT_EXECUTION_PROOF*"
)
```

## Baseline enumeration query
```kql
host.name:"soc-linux" and event.category:process and
(
  process.name:"find" or process.name:"stat" or
  process.name:"getcap" or process.name:"dpkg-query"
)
```

## Evidence classification
Label each result as one of:
- **Attack evidence:** supports the execution and impact of a controlled attack.
- **Baseline telemetry:** records discovery or configuration checks.
- **Detection alert:** an actual alert record was observed and documented.
- **Negative query result:** no matching events returned for the stated query and time range.
- **Telemetry gap:** required fields or source data were unavailable.

A process event alone is not a detection alert. Preserve query text, data view, time range, fields, screenshot path, and interpretation with each finding.
