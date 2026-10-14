# MITRE ATT&CK Mapping — Project 04

## Purpose
Map only the behavior supported by the Project 04 evidence. ATT&CK mappings are analytical interpretations, not proof that a technique succeeded.

## Evidence-backed candidate
### Scheduled task / job: Cron
- **Candidate technique:** T1053.003 — Scheduled Task/Job: Cron
- **Why considered:** Task 8 used a temporary cron entry to run a validation script in root context.
- **Evidence:** Reported host validation log and Elastic process telemetry, approximately `2026-10-13 12:37–12:49 IST`.
- **Confidence:** Moderate from the summarized evidence; confirm original event documents and the cron configuration evidence before finalizing.
- **Scope:** Controlled lab validation only. Do not characterize it as an actual intrusion.

## Not mapped as confirmed
Tasks 9–21 were primarily baseline checks. Do not assign successful exploitation techniques to PATH hijacking, wildcard abuse, SUID/SGID exploitation, shared-object injection, credential access, NFS, kernel exploitation, or automated privilege escalation without evidence that the technique was actually used and succeeded.

## Reporting rule
Record technique ID, name, observed behavior, supporting evidence IDs, confidence, and limitations. Keep observed behavior separate from inferred adversary intent.
