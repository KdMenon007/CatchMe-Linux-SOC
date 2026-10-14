# Attack Execution Record — Phase 01

## Execution status
This record separates actions observed during the lab from tasks that were only inspected. Do not infer execution from a task title.

## Task 8 — Cron file-permission validation
**Classification:** Controlled demonstration observed.

- A temporary lab script was created under `/opt/catchme-p04-lab/`.
- A temporary cron entry was used to execute the script.
- The script ran in root context; the validation log recorded `uid=0(root)` / `gid=0(root)`.
- Elastic showed cron/dash process activity associated with the validation script around 12:37–12:48 IST on 2026-10-13.
- A review window of 12:37–12:49 IST was used to inspect the repeated execution events.
- The temporary cron entry was later confirmed absent.
- A search for references to the temporary script returned no paths from the checked cron and systemd directories.
- The command to remove the temporary script was executed. Verify its absence from the target before declaring cleanup complete.
- Preserve `/opt/catchme-p04-lab/validation.log`.

## Task 9 — Cron PATH
**Classification:** Baseline only.

The existing sysstat scheduled job and PATH configuration were inspected. No PATH-hijack cron entry was created, and no PATH-based privilege escalation was demonstrated. Temporary preparation artifacts were removed.

## Task 10 — Cron wildcards
**Classification:** Baseline only.

Existing schedules and scripts were inspected. Normal `*` characters in cron schedules or scripts do not independently establish exploitability. No wildcard-based privilege escalation was demonstrated.

## Tasks 11–21
These tasks consisted of inventories, permissions checks, package/version checks, environment and capability checks, file metadata checks, NFS checks, kernel baseline checks, and assessment-tool discovery. No exploit success is claimed for these tasks.

## Evidence references
Add repository-relative paths after verifying the corresponding screenshots and raw evidence files:
- Task 8 attack screenshot: `09-Screenshots/Attack/P04-Task-08-Cron-Root-Execution.png` (verify filename exists before linking).
- Task 8 host validation log: originally `/opt/catchme-p04-lab/validation.log`; copy a sanitized evidence extract into the repository if appropriate.
- Task 9 baseline screenshot: `09-Screenshots/Telemetry/P04-Task-09-Cron-PATH-Baseline.png` (verify filename exists before linking).
- Task 10 baseline screenshot: `09-Screenshots/Telemetry/P04-Task-10-Cron-Wildcards-Baseline.png` (verify filename exists before linking).

## Limitations
Not every screenshot filename mentioned during the working session has been verified to exist in the repository. Confirm filenames and contents before publication. No detection alert is claimed unless an actual alert record is captured.
