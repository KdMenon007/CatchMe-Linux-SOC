# Threat-Hunting Hypothesis — Project 04

## Primary hypothesis
A Linux privilege-escalation attempt may leave a correlated sequence of process, authentication, scheduled-task, file-permission, or privileged-execution events on `soc-linux`.

## Supporting observations to hunt
- Unexpected cron child processes or scripts running as root.
- Changes to scheduled-task configuration or executable permissions.
- SUID/SGID executable enumeration followed by unusual execution.
- Unexpected use of privileged utilities or shell processes.
- Access to sensitive configuration, history, or SSH-key paths.
- Unexpected NFS mounts or suspicious mount configuration.
- Kernel or assessment-tool activity that requires investigation.

## Task 8 evidence-backed lead
The controlled Task 8 test produced cron/dash execution of the temporary validation script under root context. This is the confirmed demonstration in the current task set. Correlate the host-side validation log with Elastic process events and preserve the exact event timestamps.

## Negative and baseline findings
Tasks 9–21 primarily produced baseline enumeration. A query returning normal cron activity, a SUID file listing, or a process event does not prove exploitation. Record negative results with the query, time range, data view, and limitations.

## Example KQL starting points
Adapt fields to the actual Elastic data view and validate results before drawing conclusions.

```kql
host.name:"soc-linux" and event.category:process and
(
  process.command_line:"*p04-cron-validation.sh*" or
  process.command_line:"*P04_CRON_PRIVESC_VALIDATION*" or
  process.command_line:"*P04_ROOT_EXECUTION_PROOF*"
)
```

```kql
host.name:"soc-linux" and event.category:process and
(
  process.name:"find" or process.name:"stat" or
  process.name:"getcap" or process.name:"dpkg-query"
)
```

## Required recording fields
- Hypothesis and expected evidence.
- Query text and data view/index pattern.
- Absolute time range and timezone.
- `@timestamp`, `host.name`, `user.name`, `process.name`, `process.command_line`, `process.parent.name`, `event.action`, `message`, when available.
- Result count, representative events, interpretation, and limitations.

## ATT&CK mapping
Map techniques only after behavior is supported by evidence. Keep candidate techniques explicitly marked as unconfirmed for baseline-only tasks.
