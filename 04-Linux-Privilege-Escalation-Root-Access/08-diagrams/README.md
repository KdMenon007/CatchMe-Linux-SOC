# Project 04 Diagrams

This directory contains PNG diagrams and editable Mermaid source files.

## Evidence and interpretation

- A diagram may describe a workflow or hypothetical attack path; it is not, by itself, evidence that the activity occurred.
- The controlled Task 8 root-context execution is the demonstrated privilege-escalation scenario in this project.
- Sudo-denial events are visible in Elastic Discover, but a dedicated Elastic Security alert has not been verified.
- Baseline-only checks must not be presented as successful exploits.
- Review each PNG against its intended source and the project evidence before publication.

## Mermaid source inventory

| Source | Subject |
|---|---|
| `mermaid/01-lab-architecture.mmd` | Lab architecture |
| `mermaid/02-attack-chain.mmd` | Attack-chain assessment |
| `mermaid/03-telemetry-pipeline.mmd` | Telemetry pipeline |
| `mermaid/04-threat-hunting-flow.mmd` | Threat-hunting workflow |
| `mermaid/05-incident-response-lifecycle.mmd` | Incident-response lifecycle |
| `mermaid/06-ssh-authentication-timeline.mmd` | SSH authentication timeline |
| `mermaid/07-sudo-session-correlation.mmd` | Sudo-session correlation |
| `mermaid/08-cron-file-state.mmd` | Cron validation and cleanup |
| `mermaid/09-audit-event-correlation.mmd` | Audit-event correlation |
| `mermaid/10-kql-hunt-coverage.mmd` | KQL hunt coverage |
| `mermaid/11-detection-rule-logic.mmd` | Detection-rule logic |
| `mermaid/12-alert-review-status.mmd` | Alert verification status |
| `mermaid/13-elastic-defend-telemetry.mmd` | Elastic Defend telemetry |
| `mermaid/14-auditd-telemetry.mmd` | Audit telemetry |
| `mermaid/15-investigation-evidence-map.mmd` | Investigation evidence map |
| `mermaid/16-mitre-mapping.mmd` | MITRE ATT&CK mapping |
| `mermaid/17-evidence-integrity.mmd` | Evidence integrity |
| `mermaid/18-incident-decision-gates.mmd` | Incident decision gates |
| `mermaid/19-project-phase-workflow.mmd` | Project workflow |
| `mermaid/20-evidence-gaps-summary.mmd` | Evidence gaps |

## PNG publication status

The existing PNGs have not yet been validated against these Mermaid sources. Do not assume a PNG and a same-numbered Mermaid file depict the same content until the correspondence has been reviewed.
