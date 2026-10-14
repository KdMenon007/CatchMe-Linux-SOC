# Log Sources — Phase 02

## Environment
- Target: `soc-linux` (`192.168.1.16`)
- Elastic SIEM: `192.168.1.11`
- Endpoint agent observed: Elastic Agent 9.5.5
- Timezone: `Asia/Kolkata` (IST, UTC+05:30)

## Sources observed or expected
### Elastic Defend / process telemetry
Process events were observed for commands such as `find`, `stat`, `cat`, `grep`, `dpkg-query`, `getcap`, and `sudo`. Field availability varied: some syscall events showed process names without command-line or parent-process fields.

### Linux authentication events
Earlier lab work showed SSH and sudo-related authentication/authorization records. For any task-specific conclusion, query the appropriate data view and verify the actual event fields and timestamp.

### Host-side Task 8 validation log
The target-side file `/opt/catchme-p04-lab/validation.log` contains root-context identity output from the controlled cron test. Preserve this evidence. A repository copy should be sanitized and clearly labelled as a collected extract.

### Configuration and package command output
Terminal outputs from `stat`, `find`, `dpkg-query`, `ldd`, `getcap`, `findmnt`, `systemctl`, and related commands are host-side baseline evidence when saved. A screenshot of a command in a terminal is not the same as an indexed Elastic event.

## Data-source caveats
- Do not assume all Linux audit records are present in Elastic.
- Do not assume a process event is a detection alert.
- Do not infer success from an attempted command alone.
- Do not treat a zero-result query as proof of absence without recording the query, time range, data view, and field coverage.
- Record source and event category for every significant claim.

## Source inventory table to complete
| Source/data view | Events confirmed | Fields confirmed | Gaps / follow-up |
|---|---|---|---|
| Elastic process telemetry | Process events observed | Varies by event | Record per-query field availability |
| Linux authentication | Seen in earlier lab work | Validate per event | Link actual screenshot/query |
| Audit/security events | Not asserted here as complete | Validate before use | Confirm ingestion and coverage |
| Task 8 validation log | Root-context output reported | Text log fields | Preserve original and verify file state |
