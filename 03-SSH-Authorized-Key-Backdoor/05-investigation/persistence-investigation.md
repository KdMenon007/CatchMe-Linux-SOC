# Persistence Investigation

## Technique under investigation
SSH authorized-key persistence, mapped in the existing detection configuration to MITRE ATT&CK `T1098.004` (SSH Authorized Keys).

## Endpoint observations
The existing scenario evidence records an authorized-keys file line count changing from one to two, file mode `600`, owner/group `socadmin`, and modification time around `2026-10-03 14:40:56 IST`. A later successful public-key SSH re-entry was observed at `14:45:53.827 IST`.

## Telemetry observations
Auditd syscall records exist in the historical window. However, the reviewed indexed sequence did not expose the target authorized-keys path in the searched fields. A syscall attributed to `/usr/sbin/sshd` is not enough by itself to prove a write to that file.

## Status
- Persistence scenario: supported by endpoint evidence.
- SIEM target-path correlation: unresolved in the reviewed indexed data.
- Historical alert outcome: not established.
- Removal of unauthorized key / remediation: not established.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png`
- `../09-Screenshots/Attack/P03-ATT-05-authorized-key-verification.png`
- `../09-Screenshots/Attack/P03-ATT-06-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png`
- `../09-Screenshots/Detection/P03-DET-05-authorized-keys-path-no-results.png`
