# Response Playbook — Suspicious Cron Privilege Escalation

## Trigger
A process chain suggests cron launched a script or shell in privileged context, particularly from an unexpected or writable location.

## Procedure
1. **Validate:** Confirm host, timestamp, command line, parent process, execution user/UID, and whether the job is expected.
2. **Preserve:** Export source events and retain relevant host logs before cleanup.
3. **Scope:** Identify the cron entry, referenced script, owner/group/mode, and any related executions.
4. **Assess:** Compare against baseline and determine whether execution was authorized.
5. **Contain:** If unauthorized and credible, follow the approved isolation process.
6. **Remediate:** Disable/remove unauthorized scheduling and restore trusted file ownership/permissions only after evidence collection and authorization.
7. **Verify:** Confirm the scheduled entry and unauthorized script are no longer active, then verify telemetry remains healthy.
8. **Document:** Record actions, timestamps, evidence hashes, alert status, and remaining uncertainty.

## Lab-specific guardrails
- Do not weaken the protected VM to reproduce the behavior.
- Do not delete `/opt/catchme-p04-lab/validation.log`.
- Do not report a confirmed Elastic alert without the actual alert record.
- For this project, classify Task 8 as a controlled demonstration.
