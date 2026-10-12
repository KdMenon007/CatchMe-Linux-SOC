# Expected Telemetry — SSH Authorized-Key Backdoor

## Purpose
Define expected telemetry for the documented lab scenario. Expectations are not proof that an event was collected or indexed.

## Scope
- Attacker: Kali, `192.168.1.10`
- Target: `soc-linux`, `192.168.1.16`
- Historical activity window: 2026-10-03, approximately 14:10–14:50 IST (`UTC+05:30`)
- Target file: `/home/socadmin/.ssh/authorized_keys`

## Expected sources

| Activity | Expected source and useful fields | Evidence status |
|---|---|---|
| SSH password attempts and successful password authentication | SSH/authentication logs; `host.name`, `source.ip`, `user.name`, `process.name`, `event.action`, `event.outcome` | Observed in supplied screenshots |
| Session activity | Endpoint journal and Auditd; `@timestamp`, `user.name`, `event.action`, `process.executable`, `process.pid` | Observed in supplied screenshots |
| Authorized-key modification | Auditd file-related records; `auditd.log.record_type`, `auditd.log.sequence`, `auditd.log.name`, `auditd.log.path`, `file.path` | Target-path event not located in historical Elasticsearch searches |
| Public-key SSH re-entry | SSH journal and Elastic authentication event; `source.ip`, `user.name`, `event.action`, `event.outcome`, `system.auth.ssh.method` | Observed in supplied screenshots |
| Post-re-entry commands/privilege activity | Auditd and Elastic Defend; `process.executable`, `process.command_line`, `user.name`, `event.action`, dataset | Related activity visible; assess individual claims against screenshots |

## Interpretation rules
- Expected telemetry is not observed telemetry.
- A `write` syscall alone does not identify the file written.
- A `PATH` record must be inspected and correlated before attributing a write to `authorized_keys`.
- Successful public-key login does not independently prove which file was modified.
- Never include passwords, private keys, tokens, or unsanitized raw logs.

## Current limitation
Historical searches showed Auditd ingestion and `write` syscall records, but did not locate a `PATH` record or indexed path field identifying `/home/socadmin/.ssh/authorized_keys` in the searched window. This is an unresolved evidence gap, not proof that the endpoint never recorded the modification.
