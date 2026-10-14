# Attack Timeline — Phase 01

## Time standard
All times below use `Asia/Kolkata` (IST, UTC+05:30). Only timestamps supported by recorded terminal or Elastic evidence are presented as observed. Enumeration commands without a recorded timestamp are not assigned an invented time.

## Confirmed controlled demonstration
| Date | Time (IST) | Activity | Evidence meaning |
|---|---|---|---|
| 2026-10-13 | Approximately 12:37–12:48 | Temporary cron validation script executed through the controlled test configuration | Root-context execution was recorded in the validation log and corroborated by Elastic process events |
| 2026-10-13 | 12:37–12:49 review window | Elastic review of the repeated Task 8 executions | Query window used for correlation; not a claim that every event occurred at the boundary times |
| 2026-10-13 | Later in the session | Temporary cron entry checked and found absent | Cleanup check |
| 2026-10-13 | Later in the session | Search for script references in cron and systemd locations returned no paths | Limited reference check, not a complete system-wide reference audit |
| 2026-10-13 | Later in the session | Script-removal command executed | Final file absence must be verified separately |

## Subsequent baseline investigation
The SUID/SGID, shared-library, environment, shell, history, configuration, SSH-key, NFS, kernel, and assessment-tool checks were performed later in the session. Exact timestamps must be taken from the relevant Elastic screenshots or terminal records. Do not use Task 8's time range for these unrelated checks.

## Timeline interpretation
- The controlled Task 8 root execution is the demonstrated privilege-escalation event in the current record.
- Tasks 9–21 are predominantly baseline investigations; they should not be represented as successful attacks.
- A query review window is not itself an event timestamp.
- If an exact timestamp cannot be recovered, use “timestamp unavailable” rather than fabricating precision.

## Publication checklist
- [ ] Verify the Task 8 screenshot exists and shows the stated event.
- [ ] Preserve and reference the validation log.
- [ ] Verify the temporary script is absent.
- [ ] Verify the temporary cron entry remains absent.
- [ ] Add exact event timestamps from Elastic if available.
- [ ] Keep all later baseline checks separate from the Task 8 attack timeline.
