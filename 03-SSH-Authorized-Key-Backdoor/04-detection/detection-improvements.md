# Detection Improvements — Evidence-Driven Backlog

## Priority 1 — Improve evidence quality
- Capture the live rule configuration and a representative alert document.
- Confirm whether the alert includes `auditd.log.name`, another target path field, or only the configured audit key.
- Correlate records using timestamps and Auditd sequence/event identifiers where available.
- Separate one underlying file operation from multiple syscall-level alerts.

## Priority 2 — Evaluate alert granularity
Prior notes report five High alerts from one controlled append-comment/restore operation. Before changing logic, determine whether the alerts represent distinct syscalls, duplicate ingestion, rule scheduling overlap, or another cause. Do not assume which cause applies without inspecting the alert documents.

## Priority 3 — Review path attribution
The historical telemetry review did not find an indexed PATH/path-field record identifying `/home/socadmin/.ssh/authorized_keys` in the searched window. This is a telemetry correlation gap; it does not establish that the live rule is broken. Confirm the actual event fields before considering a rule change.

## Priority 4 — Coverage and response linkage
After validating rule behavior, document alert triage, investigation links, severity rationale, and response hand-off. Detection alone does not establish containment or remediation.

## Out of scope
- No live rule edits.
- No new attack or controlled file modification.
- No changes to Auditd, Elastic Agent, or Elastic Defend.
- No claim that a Sigma or YARA rule is deployed.
