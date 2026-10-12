# Project 03 — Diagram Library

This directory contains the CatchMe Linux SOC Project 03 infographic PNGs and a separate set of editable Mermaid source diagrams.

## PNG infographic diagrams

1. [`01-lab-architecture.png`](01-lab-architecture.png) — Lab hosts, IP addresses, and telemetry paths.
2. [`02-attack-chain.png`](02-attack-chain.png) — SSH access and authorized-key persistence sequence.
3. [`03-telemetry-pipeline.png`](03-telemetry-pipeline.png) — Auditd and Elastic Defend telemetry flow.
4. [`04-threat-hunting-flow.png`](04-threat-hunting-flow.png) — Threat-hunting workflow.
5. [`05-initial-triage-flow.png`](05-initial-triage-flow.png) — Initial alert triage.
6. [`06-query-investigation-chain.png`](06-query-investigation-chain.png) — Query pivots and investigation sequence.
7. [`07-detection-workflow.png`](07-detection-workflow.png) — Detection-rule evaluation and alert review.
8. [`08-persistence-flow.png`](08-persistence-flow.png) — SSH authorized-key persistence lifecycle.
9. [`09-file-investigation-flow.png`](09-file-investigation-flow.png) — File and authorized-key investigation.
10. [`10-account-investigation-flow.png`](10-account-investigation-flow.png) — User and account investigation.
11. [`11-network-investigation-flow.png`](11-network-investigation-flow.png) — Network activity investigation.
12. [`12-mitre-attack-mapping.png`](12-mitre-attack-mapping.png) — ATT&CK technique mapping.
13. [`13-cyber-kill-chain.png`](13-cyber-kill-chain.png) — Cyber Kill Chain view.
14. [`14-incident-response-flow.png`](14-incident-response-flow.png) — Incident-response lifecycle.
15. [`15-containment-remediation-flow.png`](15-containment-remediation-flow.png) — Containment and remediation.
16. [`16-evidence-correlation-map.png`](16-evidence-correlation-map.png) — Cross-source evidence correlation.
17. [`17-detection-improvement-cycle.png`](17-detection-improvement-cycle.png) — Detection engineering and tuning.
18. [`18-final-attack-timeline.png`](18-final-attack-timeline.png) — Attack timeline based on available evidence.
19. [`19-end-to-end-soc-workflow.png`](19-end-to-end-soc-workflow.png) — End-to-end SOC workflow.
20. [`20-investigation-summary.png`](20-investigation-summary.png) — Investigation summary and key findings.

**PNG inventory:** The project inventory check confirmed all 20 PNG files (numbered 01–20) are present in this directory.

## Mermaid source diagrams

The `mermaid/` folder is a separate editable source set. Its filenames and scope do not map one-to-one to the infographic PNG filenames.

1. [`mermaid/01-lab-architecture.mmd`](mermaid/01-lab-architecture.mmd)
2. [`mermaid/02-attack-chain.mmd`](mermaid/02-attack-chain.mmd)
3. [`mermaid/03-telemetry-pipeline.mmd`](mermaid/03-telemetry-pipeline.mmd)
4. [`mermaid/04-detection-workflow.mmd`](mermaid/04-detection-workflow.mmd)
5. [`mermaid/05-incident-response-lifecycle.mmd`](mermaid/05-incident-response-lifecycle.mmd)
6. [`mermaid/06-ssh-authentication-timeline.mmd`](mermaid/06-ssh-authentication-timeline.mmd)
7. [`mermaid/07-ssh-session-correlation.mmd`](mermaid/07-ssh-session-correlation.mmd)
8. [`mermaid/08-authorized-keys-file-state.mmd`](mermaid/08-authorized-keys-file-state.mmd)
9. [`mermaid/09-auditd-event-correlation.mmd`](mermaid/09-auditd-event-correlation.mmd)
10. [`mermaid/10-kql-hunt-coverage.mmd`](mermaid/10-kql-hunt-coverage.mmd)
11. [`mermaid/11-detection-rule-logic.mmd`](mermaid/11-detection-rule-logic.mmd)
12. [`mermaid/12-alert-review-status.mmd`](mermaid/12-alert-review-status.mmd)
13. [`mermaid/13-elastic-defend-telemetry.mmd`](mermaid/13-elastic-defend-telemetry.mmd)
14. [`mermaid/14-auditd-telemetry.mmd`](mermaid/14-auditd-telemetry.mmd)
15. [`mermaid/15-investigation-evidence-map.mmd`](mermaid/15-investigation-evidence-map.mmd)
16. [`mermaid/16-mitre-attack-mapping.mmd`](mermaid/16-mitre-attack-mapping.mmd)
17. [`mermaid/17-evidence-integrity-workflow.mmd`](mermaid/17-evidence-integrity-workflow.mmd)
18. [`mermaid/18-incident-decision-gates.mmd`](mermaid/18-incident-decision-gates.mmd)
19. [`mermaid/19-project-phase-workflow.mmd`](mermaid/19-project-phase-workflow.mmd)
20. [`mermaid/20-evidence-gaps-summary.mmd`](mermaid/20-evidence-gaps-summary.mmd)

## Fixed lab reference

- Kali attacker: `192.168.1.10`
- Linux endpoint (`soc-linux`): `192.168.1.16`
- Elastic SIEM (`elastic-siem`): `192.168.1.11`
- Gateway: `192.168.1.1`
- Lab subnet: `192.168.1.0/24`

These are the documented lab addresses, not a claim that connectivity was freshly tested.

## Evidence and accuracy notes

- Diagrams are explanatory visuals, not substitutes for original terminal, endpoint, or Kibana evidence.
- The historical detection outcome remains unresolved.
- Exact historical indexed `authorized_keys` target-path correlation remains unresolved.
- Current SSH public-key authorization was not confirmed; a key comment alone does not prove ownership or authorization.
- Containment, eradication, and recovery must not be described as completed without separate supporting evidence.
- Timelines and potential lateral movement must be labeled illustrative or unconfirmed when not directly supported.
- Refer to the phase documentation and screenshot hash manifest for evidence details.

## Viewing and maintenance

- Open PNG files directly for presentation.
- Open `.mmd` files in a Mermaid-compatible editor or renderer.
- Keep links relative for GitHub compatibility.
- Update this index when diagrams are added or renamed.
