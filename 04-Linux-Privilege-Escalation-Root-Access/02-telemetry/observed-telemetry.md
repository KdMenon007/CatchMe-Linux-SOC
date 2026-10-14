# Observed Telemetry — Phase 02

## Evidence summary
The lab session produced Elastic process telemetry for multiple discovery and baseline commands. The most significant observed event sequence was the controlled Task 8 cron-permission demonstration.

## Task 8 — controlled cron test
- **Date:** 2026-10-13
- **Review interval:** approximately 12:37–12:49 IST
- **Observed behavior:** cron/dash execution associated with `/opt/catchme-p04-lab/p04-cron-validation.sh`.
- **Host-side proof:** the validation log recorded root identity output (`uid=0(root)` and `gid=0(root)`).
- **Classification:** controlled demonstration evidence.
- **Cleanup:** the temporary cron entry was later confirmed absent. A command to remove the temporary script was executed; verify final absence on the target. Preserve `validation.log`.

The review interval is the window used for correlation. Use exact event timestamps from the Elastic records when documenting individual events.

## Baseline telemetry from later tasks
Process events were observed for discovery commands and checks involving:
- SUID/SGID inventory (`find`).
- Package and executable metadata (`dpkg-query`, `stat`).
- Shared-library dependency checks (`ldd`).
- Environment and capabilities (`env`, `grep`, `getcap`).
- File metadata checks for history, configuration, and SSH files.
- NFS service/mount checks (`findmnt`, `systemctl`).
- Kernel and assessment-tool baseline checks.

These events show that the checks were performed. They do not demonstrate that the inspected paths were exploitable.

## Field availability
Some observed process/syscall records lacked command-line or parent-process details. Record those gaps in query notes; do not fabricate missing fields.

## Detection status
No detection alert is claimed in this document. A detection claim requires an actual alert record, rule name, alert timestamp, and supporting evidence.

## Evidence references
Add verified repository-relative screenshot and raw-log paths before publication. Do not assume filenames mentioned during troubleshooting exist until checked with `ls` or `tree`.
