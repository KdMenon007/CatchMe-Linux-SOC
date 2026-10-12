# Investigation Overview

## Purpose
Investigate the SSH authorized-key persistence scenario using the existing project evidence. This document separates observed facts from interpretation and unresolved questions.

## Scope
- Attacker/lab source: Kali, `192.168.1.10`
- Endpoint: `soc-linux`, `192.168.1.16`
- SIEM: `elastic-siem`, `192.168.1.11`
- Primary account observed in the scenario: `socadmin`
- Timezone: Asia/Kolkata (IST, UTC+05:30)
- Historical activity window used in prior SIEM review: 2026-10-03 14:10–14:50 IST

## Evidence reviewed
Existing screenshots under `../09-Screenshots/Attack/`, `../09-Screenshots/Telemetry/`, `../09-Screenshots/Hunting/`, and `../09-Screenshots/Detection/`, together with prior-phase documented observations.

## Confirmed observations from prior-phase evidence
- SSH password-attack activity and an initial SSH session were documented in the existing Attack/Telemetry screenshots.
- The authorized-keys file was documented as changing from one line to two; its recorded permissions were `600`, and the owner/group were `socadmin`.
- A successful public-key SSH re-entry was observed at `2026-10-03 14:45:53.827 IST`, with source `192.168.1.10`, host `soc-linux`, user `socadmin`, and `system.auth.ssh.method: publickey`.
- Auditd syscall telemetry exists in Elasticsearch for the historical window, including a `write` syscall attributed to `/usr/sbin/sshd` around `14:40:11.939 IST`.
- Searches for the target authorized-keys path in selected indexed fields returned no results in the reviewed window.
- The reviewed Open, Acknowledged, and Closed alert views showed no results for the selected Oct 3 window. The historical alert outcome is not established.

## Investigation limitations
- A screenshot inventory and file hashes do not establish the truth of every event depicted; screenshots must be interpreted in their original query/time-range context.
- The observed syscall does not, by itself, prove that the syscall wrote to `authorized_keys`.
- The absence of a matching indexed path field is not proof that the endpoint never recorded the path.
- Exact sudo commands were not established from the reviewed telemetry; `process.command_line` was null in reviewed sudo records.
- Containment, key removal, credential rotation, and remediation completion are not established by the current evidence reviewed here.

## Assessment
The evidence supports a documented SSH access and public-key re-entry sequence associated with an authorized-keys persistence scenario. Some path-level file correlation and historical detection outcome remain unresolved. Do not describe these as proven detection failure, root cause, or completed remediation.

## Related material
- [Account investigation](account-investigation.md)
- [Attack-chain investigation](attack-chain-investigation.md)
- [File investigation](file-investigation.md)
- [Network investigation](network-investigation.md)
- [Persistence investigation](persistence-investigation.md)
- [Process investigation](process-investigation.md)
- [Timeline analysis](timeline-analysis.md)
- [Investigation findings](investigation-findings.md)
