# Expected Telemetry — Phase 02

## Purpose
Describe the telemetry expected during Linux privilege-boundary assessment and the evidence needed to validate each claim. Expected telemetry is a collection plan, not proof that a source or field was present.

## Expected sources and events
| Source | Expected observation | Validation notes |
|---|---|---|
| Elastic Defend process events | Enumeration commands, process name, user, command line, parent process when available | Actual field availability varies by event |
| Linux authentication logs | SSH authentication and sudo authorization outcomes | Confirm source, event timestamp, and account context |
| Audit/security logs, if ingested | Relevant syscall or file activity | Do not assume auditd events are indexed without checking |
| Task 8 validation log | Root identity output from controlled cron test | Preserve the original log; sanitize any copy used in the repository |
| File metadata output | Ownership, mode, package ownership, file timestamps | Store output if it will be cited as evidence |

## Task 8 expected and observed behavior
Expected during the controlled test:
- A cron-launched process chain associated with the temporary validation script.
- Evidence that the script ran in root context.
- A corresponding validation-log record.

Observed in the lab record:
- Cron/dash process events associated with the test script.
- Root identity output in `/opt/catchme-p04-lab/validation.log`.
- A later check showed the temporary cron entry absent.

Do not infer that a detection alert fired unless an actual alert record is captured.

## Baseline task telemetry
For Tasks 9–21, the primary expected result was process telemetry for read-only enumeration commands and configuration checks. Such events document the assessment; they do not demonstrate privilege escalation.

## Required event fields
Where present, collect:
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

Missing fields should be recorded as a telemetry limitation, not treated as proof that an event did not occur.

## Evidence classification
Mark each item as attack evidence, baseline telemetry, detection alert, negative query result, or telemetry gap.
