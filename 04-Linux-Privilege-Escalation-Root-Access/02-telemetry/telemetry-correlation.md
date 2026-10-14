# Telemetry Correlation — Phase 02

## Correlation objective
Correlate host-side evidence with Elastic process telemetry using a consistent host, account, process context, and time window. Keep attack evidence separate from baseline enumeration.

## Task 8 correlation
### Host-side evidence
The Task 8 validation log records root identity output from the temporary cron validation script.

### Elastic evidence
Elastic process events showed cron/dash execution associated with the script during the Task 8 review interval of approximately 12:37–12:49 IST on 2026-10-13.

### Interpretation
The host-side identity proof and process events jointly support that the controlled script executed in root context. This was a deliberately configured lab demonstration, not proof that the original system cron directories were writable.

### Follow-up checks
- Verify the exact event timestamps and process fields in Discover.
- Preserve the validation log.
- Verify the temporary cron entry is absent.
- Verify the temporary script is absent.
- Do not mark cleanup complete until the target's final state is confirmed.

## KQL starting query
Adjust to the actual Elastic data view and fields available:

```kql
host.name:"soc-linux" and event.category:process and
(
  process.command_line:"*p04-cron-validation.sh*" or
  process.command_line:"*P04_CRON_PRIVESC_VALIDATION*" or
  process.command_line:"*P04_ROOT_EXECUTION_PROOF*"
)
```

If the exact command-line fields are missing, broaden the query using confirmed process names and inspect the event document. Do not assume all fields are populated.

## Baseline correlation
For later tasks, correlate the exact time of the command with process telemetry. Record the query and time range. Enumeration events are baseline telemetry, not exploit evidence.

Example starting query:

```kql
host.name:"soc-linux" and event.category:process and
(
  process.name:"find" or process.name:"stat" or
  process.name:"getcap" or process.name:"dpkg-query" or
  process.name:"findmnt" or process.name:"systemctl"
)
```

## Correlation record template
| Field | Value |
|---|---|
| Task | |
| Host | `soc-linux` |
| Data view/index pattern | |
| KQL | |
| Absolute time range / timezone | |
| Host-side evidence | |
| Elastic event IDs/timestamps | |
| Key fields present | |
| Fields missing | |
| Classification | Attack evidence / baseline / alert / negative result / telemetry gap |
| Interpretation and confidence | |
| Screenshot/evidence path | |

## Common pitfalls
- Reusing the Task 8 time window for unrelated tasks.
- Treating process telemetry as an alert.
- Treating an empty query as proof that activity never happened.
- Claiming exact timestamps without source evidence.
- Confusing a controlled test configuration with a pre-existing production weakness.
