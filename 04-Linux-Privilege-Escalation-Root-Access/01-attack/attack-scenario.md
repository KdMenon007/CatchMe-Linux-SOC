# Attack Scenario — Linux Privilege Escalation

## Scenario
A low-privilege Linux account is used to assess whether misconfigured scheduled tasks or other privilege boundaries could permit unintended privileged execution. The SOC investigates host activity and Elastic telemetry to determine what occurred and whether the result is supported by evidence.

## Confirmed controlled scenario: Task 8
A temporary test script and cron entry were used to validate a cron file-permission weakness in the lab. The script ran in root context, and the validation log recorded root identity output. Elastic process events showed cron/dash execution associated with the validation script.

The temporary cron entry was subsequently confirmed absent. The script-removal command was executed later; final absence must be verified from the target. Preserve the validation log.

## Related baseline investigations
The following areas were assessed without demonstrating a successful exploit:
- Cron PATH and wildcard behavior.
- SUID/SGID executable inventory and selected package versions.
- Shared-library dependencies and loader configuration.
- Environment variables, shell permissions, and file capabilities.
- History, configuration, and SSH-key file permissions.
- NFS configuration and mounts.
- Kernel version and exposed mitigation status.
- Availability of common privilege-assessment tools.

## Security interpretation
The Task 8 demonstration is evidence of root-context execution under the deliberately configured test condition. It is not proof that the original system cron directories were writable or that the same weakness existed before the controlled test. Baseline enumeration in other tasks is not an attack success.

## Intended SOC questions
- Which process initiated the scheduled execution?
- Under which user context did the test script execute?
- Can host-side proof be correlated with Elastic process telemetry?
- Was the temporary cron entry removed?
- Were the validation log and other evidence preserved?
- Which remaining task results are baseline-only or pending?
