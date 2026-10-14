# Investigation Findings — Project 04

## Finding 1 — Controlled cron root-context execution
**Assessment:** Supported by reported host validation log entries and Elastic process telemetry for Task 8.

**Confidence:** Moderate based on the summarized evidence; raise confidence only after reviewing the original log and matching event documents.

**Impact in this lab:** Demonstrates the risk of a privileged scheduled task invoking a script writable by a less-privileged account.

**Limitations:** No dedicated Elastic alert is confirmed. On 2026-10-14, target-side checks confirmed that the temporary cron entry and validation script were absent, no references were found in the checked cron/systemd locations, and the validation log remained preserved.

## Finding 2 — Cron PATH and wildcard techniques
**Assessment:** No exploitation demonstrated. The checks observed ordinary scheduled-job behavior and baseline configuration.

## Finding 3 — SUID/SGID, shared objects, environment variables, and shell features
**Assessment:** Inventory and read-only checks were reported. No exploit was demonstrated; these checks do not establish universal absence of vulnerabilities.

## Finding 4 — History, configuration, SSH keys, NFS, kernel posture, and helper scripts
**Assessment:** Baseline checks only. No credential disclosure, NFS escalation, kernel exploit, or helper-script-based escalation was demonstrated in the available record.

## Overall conclusion
Project 04 has one reported controlled root-context cron demonstration plus multiple baseline-only investigations. Do not label the host compromised. Keep detection status separate from source-event evidence.
