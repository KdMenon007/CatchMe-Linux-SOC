# Technique Mapping — Project 04

| Activity | ATT&CK mapping | Status | Evidence / limitation |
|---|---|---|---|
| Temporary cron job used for controlled root-context validation | T1053.003 — Scheduled Task/Job: Cron | Candidate, supported by reported lab evidence | Confirm original cron configuration, process ancestry, and host validation log |
| Read-only cron configuration inspection | No successful technique mapping asserted | Baseline only | Enumeration does not establish exploitation |
| SUID/SGID inventory and dependency checks | No successful technique mapping asserted | Baseline only | No exploit demonstrated |
| History/configuration/SSH/NFS/kernel checks | No successful technique mapping asserted | Baseline only | No credential access or privilege escalation demonstrated |
| Privilege-escalation tool discovery | No successful technique mapping asserted | Baseline only | Tool absence/presence is not itself successful escalation |

## Mapping quality
A technique should be marked **confirmed** only when evidence supports the behavior and the relevant prerequisites. Use **candidate** where the behavior is plausible but the underlying event/configuration evidence still needs direct review.
