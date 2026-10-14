# Investigation Overview — Project 04

## Purpose
Record evidence-led investigation of Linux privilege-escalation checks and the controlled Task 8 cron validation.

## Scope
- Host: `soc-linux` (`192.168.1.16`)
- Elastic SIEM: `192.168.1.11`
- Analyst host: Kali (`192.168.1.10`)
- Timezone for recorded local times: `Asia/Kolkata` (IST, UTC+05:30)

## Primary case
Task 8 used a temporary cron entry and a deliberately writable lab validation script to demonstrate root-context scheduled execution. The host-side validation log and Elastic process telemetry were reported for approximately `2026-10-13 12:37–12:49 IST`.

## Evidence boundary
The available record supports a controlled lab demonstration. It does not, by itself, establish an Elastic alert, a real-world compromise, or successful exploitation for Tasks 9–21.

## Investigation questions
1. Is the event attributable to `soc-linux` and the Task 8 lab path?
2. Does the process sequence align with cron launching a shell/script?
3. Is there corroborating host-side evidence of UID 0?
4. Was the temporary cron entry removed? **Verified on the target on 2026-10-14.**
5. Has the temporary script's final absence been verified? **Verified on the target on 2026-10-14.**
6. Are screenshots, raw events, and timestamps preserved?

## Outcome
Target-side cleanup checks passed on 2026-10-14: the temporary cron entry and script were absent, no references were found in the checked cron/systemd locations, and the validation log remained preserved. The investigation is not fully closed: outstanding evidence-preservation, process-ancestry, and alert-validation tasks must remain open until completed or explicitly documented as unavailable. Unknown or unverified facts must remain explicitly marked.
