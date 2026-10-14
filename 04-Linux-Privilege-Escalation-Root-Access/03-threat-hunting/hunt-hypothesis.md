# Hunt Hypothesis — Project 04

## Primary hypothesis
A Linux privilege-escalation attempt may be visible as a sequence of discovery, configuration access, scheduled-task execution, and privileged process activity. Correlating process telemetry with host-side proof should distinguish an actual controlled escalation from routine enumeration.

## Hypothesis H-01: Cron-mediated privileged execution
If a scheduled task executes a script whose permissions can be altered by a lower-privilege user, then the scheduled process chain may execute the script in a privileged context. Expected evidence includes the cron process, shell child, script path, root identity output, and relevant timestamps.

**Status:** The controlled Task 8 demonstration produced evidence consistent with this hypothesis. It was performed using a temporary test configuration and must not be described as a pre-existing production weakness.

## Hypothesis H-02: PATH-based cron abuse
If a privileged scheduled job resolves a command through an attacker-controlled writable directory, command substitution may be possible. Relevant evidence would include the job's PATH, directory permissions, command resolution, and resulting privileged child process.

**Status:** Not demonstrated. The existing sysstat PATH was inspected; no PATH-hijack cron entry was created on the protected VM.

## Hypothesis H-03: SUID/SGID or environment-based escalation
If a privileged executable has an exploitable vulnerability or unsafe dependency/environment behavior, execution may create unexpected privileged behavior. Evidence requires a vulnerable configuration or advisory match plus controlled outcome proof.

**Status:** Not demonstrated. Inventory, package/version, dependency, environment, and capability checks were performed only.

## Hypothesis H-04: Credential or configuration exposure
If sensitive history, configuration, or SSH-key material is accessible beyond intended permissions, the exposure may be visible through metadata and access events.

**Status:** No credential exposure was demonstrated in the checks recorded.

## Hunting guardrails
- Query only authorized lab telemetry.
- Record exact time range, data view, query, and fields.
- Treat empty results as limited to that query and interval.
- Keep candidate ATT&CK techniques marked unconfirmed until evidence supports them.
