# Detection Engineering Overview — Project 04

## Objective
Document detection opportunities for Linux privilege-escalation activity while separating available telemetry from validated alerts.

## Current evidence status
- **Confirmed controlled demonstration:** Task 8 temporary cron validation executed in root context; host-side validation log and Elastic process telemetry were reported.
- **Baseline-only checks:** Tasks 9–21, including cron PATH/wildcards, SUID/SGID, shared-library dependencies, environment variables, shell features, history/configuration, SSH keys, NFS, kernel posture, and tool discovery.
- **Alert status:** No Elastic detection alert is claimed unless an actual alert record and rule metadata are captured.

## Detection objectives
1. Identify unusual cron-to-shell/script process chains.
2. Correlate privileged scheduled execution with writable script paths.
3. Flag suspicious changes to cron configuration and executable permissions.
4. Detect unexpected SUID/SGID or file-capability changes.
5. Provide triage context without treating routine enumeration as proof of compromise.

## Data prerequisites
Useful fields may include `host.name`, `event.category`, `event.type`, `process.name`, `process.command_line`, `process.parent.name`, `process.parent.command_line`, `user.name`, `user.id`, `file.path`, `file.mode`, `event.action`, and `@timestamp`. Field availability depends on the installed integrations and mappings.

## Validation standard
A rule is validated only after:
- the exact rule/query and version are recorded;
- the test event is present in the expected data view and time range;
- the alert document exists if alerting is claimed;
- false positives and limitations are documented;
- cleanup is verified.

## Safety
Do not weaken the protected `soc-linux` VM to manufacture detections. Use a disposable snapshot for intentionally vulnerable configurations.
