# Threat-Hunting Overview — Project 04

## Mission
Investigate whether common Linux privilege-escalation paths are reflected in Elastic telemetry and corroborate any claimed outcome with host-side evidence.

## Environment
- Target: `soc-linux` (`192.168.1.16`)
- Elastic: `192.168.1.11`
- Attacker workstation: Kali Linux (`192.168.1.10`)
- Target OS: Ubuntu 24.04.4 LTS, ARM64
- Kernel observed: `6.8.0-146-generic`
- Timezone: `Asia/Kolkata`

## Hunt coverage
- Cron file permissions, PATH, and wildcard behavior.
- SUID/SGID executable inventory and selected package versions.
- Shared-library dependencies and loader configuration.
- Environment variables, shell permissions, and file capabilities.
- History/configuration file metadata and SSH-key permissions.
- NFS mounts/services/configuration.
- Kernel version and mitigation status.
- Availability of common assessment tools.

## Summary
Task 8 is the documented controlled root-execution demonstration. Tasks 9–21 are baseline-only or not demonstrated in the current record. The project does not claim the target is vulnerability-free, and it does not claim a detection alert without an actual alert record.

## Navigation
- `hunt-hypothesis.md` — hypotheses and status.
- `hunting-methodology.md` — repeatable hunt process.
- `initial-triage.md` — first-pass validation.
- `query-investigation-chain.md` — query pivots.
- `hunt-timeline.md` — time-correlated events.
- `findings.md` — evidence-backed findings.
