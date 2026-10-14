# Attack Plan — Phase 01

## Execution model
Proceed task by task. On the protected target, perform read-only discovery and safe telemetry validation. Perform exploit demonstrations that require intentional misconfiguration only on a disposable VM or a restorable snapshot.

## Planned task sequence
| Task | Category | Action performed or planned | Current classification |
|---|---|---|---|
| 8 | Cron file permissions | Temporary validation script and cron entry were used to demonstrate root-context execution; entry was removed afterward | Controlled demonstration observed |
| 9 | Cron PATH | Inspected existing PATH configuration and normal scheduled execution | Baseline only; no PATH hijack |
| 10 | Cron wildcards | Inspected cron schedules and scripts | Baseline only; no wildcard exploit |
| 11 | SUID/SGID known exploits | Enumerated privileged executables and checked selected package versions | Baseline only; no known exploit run |
| 12 | Shared object injection | Inspected `ldd` dependencies and loader/library configuration checks | Baseline checks; no injection |
| 13 | SUID environment variables | Inspected environment variables and SUID metadata | Baseline only |
| 14–15 | Shell features | Checked Bash/Dash permissions, package versions, and capabilities | Baseline only |
| 16 | History files | Checked history-file metadata without printing contents | Baseline only |
| 17 | Config files | Checked standard configuration-file permissions | Baseline only |
| 18 | SSH keys | Checked `.ssh` metadata and selected key filenames | Baseline only |
| 19 | NFS | Checked mounts, services, exports metadata, and utility availability | Baseline only |
| 20 | Kernel | Recorded kernel, architecture, package, and mitigation status | Baseline only; no kernel exploit |
| 21 | Privilege-escalation tools | Searched selected locations for common assessment tools | Baseline only |

## Evidence collection
For every executed task:
1. Capture the terminal result without secrets.
2. Record exact timestamps where available.
3. Search Elastic using a task-specific KQL query and matching absolute time range.
4. Save screenshots under `09-Screenshots/` in the appropriate category.
5. Record whether the result is attack evidence, baseline telemetry, a detection alert, a negative result, or a telemetry gap.

## Stop conditions
Stop if a procedure requires weakening the protected VM, modifying system configuration, exposing credentials, or executing an unverified exploit. Mark the task pending and move the controlled test to a disposable environment.
