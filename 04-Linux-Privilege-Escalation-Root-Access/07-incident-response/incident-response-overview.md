# Incident Response Overview — Project 04

## Purpose
Document response steps for a suspected Linux privilege-escalation event, using the Task 8 controlled cron demonstration as the primary lab case.

## Case summary
- Host: `soc-linux` (`192.168.1.16`)
- SIEM: Elastic (`192.168.1.11`)
- Analyst host: Kali (`192.168.1.10`)
- Local timezone: `Asia/Kolkata` (IST, UTC+05:30)
- Primary review window: `2026-10-13 12:37–12:49 IST`

The available project notes report a controlled cron validation script running in root context and corresponding Elastic process telemetry. This is a lab validation, not a real compromise. No dedicated Elastic alert is confirmed.

## Response objectives
1. Preserve source evidence before cleanup.
2. Establish the process chain and execution identity.
3. Determine whether the job and script were authorized.
4. Contain only when justified and within the lab's approved scope.
5. Verify cleanup without destroying the validation log.
6. Document limitations and follow-up work.

## Protected-host rule
Do not introduce additional misconfigurations on the long-lived `soc-linux` VM. Use a disposable VM or snapshot for tests requiring weak permissions, modified sudo policy, SUID changes, or exploitable cron configurations.
