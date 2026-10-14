# Threat-Hunting Findings — Project 04

## Executive summary
The strongest confirmed lead in the current record is the controlled Task 8 cron-permission demonstration. The temporary validation script executed in root context, and the host-side validation log recorded root identity output. Elastic process events showed cron/dash activity associated with that script during the review window.

Tasks 9–21 primarily produced baseline observations. Do not treat enumeration activity as proof of exploitation.

## Findings
### Finding TH-01 — Controlled cron test executed as root
- **Status:** Supported by the recorded lab evidence.
- **Observed behavior:** Cron/dash execution associated with `/opt/catchme-p04-lab/p04-cron-validation.sh`.
- **Host-side evidence:** `/opt/catchme-p04-lab/validation.log` recorded root identity output.
- **Elastic review interval:** Approximately 2026-10-13 12:37–12:49 IST.
- **Interpretation:** The deliberately configured test demonstrated root-context execution through the temporary cron setup.
- **Limitations:** This does not prove the original system cron directories were writable or that the same weakness existed before the controlled setup.
- **Cleanup:** Temporary cron entry was later confirmed absent. Script removal command was executed; verify final absence. Preserve the validation log.

### Finding TH-02 — Cron PATH
- **Status:** Baseline only.
- Existing sysstat PATH and scheduled execution were inspected.
- No PATH hijack was demonstrated.

### Finding TH-03 — Cron wildcards
- **Status:** Baseline only.
- Existing schedules and scripts were inspected.
- Normal wildcard characters alone do not establish exploitability; no wildcard attack was demonstrated.

### Finding TH-04 — SUID/SGID and shell-related checks
- **Status:** Baseline only.
- SUID/SGID inventory, selected package versions, shell permissions, and file capabilities were inspected.
- No known-exploit or shell-feature escalation was demonstrated.

### Finding TH-05 — Credential/configuration, SSH, NFS, and kernel checks
- **Status:** Baseline only.
- Metadata, permissions, service status, kernel version, and mitigation status were recorded.
- No credentials were recovered and no NFS or kernel exploit was demonstrated.

## Confidence and gaps
- Task 8: moderate-to-high confidence in controlled root-context execution based on host-side proof and Elastic process telemetry; preserve source files and exact event records to support publication.
- Other tasks: high confidence that the listed commands/checks were performed where terminal output is retained; no confidence claim is made about absence of all vulnerabilities.
- No detection alert is claimed without an actual alert record.
