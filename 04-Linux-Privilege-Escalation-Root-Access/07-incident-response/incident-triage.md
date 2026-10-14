# Incident Triage — Project 04

## Initial classification
**Type:** Controlled Linux privilege-escalation validation  
**Severity:** Not assigned as a real incident; determine severity only if unauthorized activity is identified  
**Alert status:** No dedicated Elastic alert confirmed

## Triage sequence
1. Confirm host identity and event time window.
2. Preserve the validation log and relevant Elastic source events.
3. Review `process.name`, `process.command_line`, parent-process fields, `user.name`, and `user.id` where available.
4. Compare the script path and cron entry against the documented lab procedure.
5. Determine whether the observed root-context execution was expected.
6. Check cleanup state without deleting evidence.
7. Escalate to incident handling only if the activity is unauthorized or unexplained.

## Avoid premature conclusions
- Root-context execution in this controlled test is not evidence that an external attacker compromised the host.
- Process telemetry does not equal an alert.
- Missing query results do not prove that activity did not occur.
