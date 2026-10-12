# Observed Telemetry — SSH Authorized-Key Backdoor

## Evidence-backed observations

| Time (IST, 2026-10-03) | Source | Observation | What it establishes |
|---|---|---|---|
| 14:10:47.830 | Elastic Discover | `authentication_failure`, user `socadmin`, source `192.168.1.10`, process `sshd` | An SSH authentication failure was indexed |
| 14:11:16.516 | Discover and endpoint journal screenshots | Successful password-based SSH login/session activity for `socadmin` from `192.168.1.10` | Initial access is supported by supplied evidence |
| 14:40:11.939 | Elastic Discover | Auditd `SYSCALL` record with syscall `write`, number `64`, executable `/usr/sbin/sshd`, PID `4697` in expanded document | A write syscall record associated with `sshd` was indexed; the document does not identify the target file |
| 14:40:11.939 | Elastic Discover | Sequence `966` query returned nine records including `SYSCALL`, `PROCTITLE`, and `LOGIN` | Multiple records share sequence and timestamp; target file remains unidentified |
| 14:45:53.828 | Elastic Discover | Auditd records include `SYSCALL`/`write`, `PROCTITLE`, `LOGIN`, and `CRED_ACQ`; displayed records share sequence `986` in part | Auditd activity was indexed near re-entry; inspected records did not show the target filename |
| 14:45:53.827 | Elastic Discover | Successful `ssh_login`; `event.outcome:success`, `host.name:soc-linux`, `source.ip:192.168.1.10`, `user.name:socadmin`, `system.auth.ssh.method:publickey` | Successful public-key re-entry is supported |
| 14:45:54.006–14:45:54.077 | Elastic Discover | `USER_START`, `CRED_ACQ`, and `USER_LOGIN` records visible | Login/session-related Auditd records were indexed around re-entry |

## Historical file-modification evidence
Endpoint terminal screenshots record the authorized-key line count changing from one to two, permissions `600`, owner `socadmin`, and modification timestamp `2026-10-03 14:40:56.572... +0530`. A later endpoint journal screenshot shows accepted public-key authentication from `192.168.1.10` at 14:45:53 for `socadmin`.

These endpoint screenshots support the historical scenario. However, the searches performed did not locate an indexed Auditd `PATH` record or target-path field for `/home/socadmin/.ssh/authorized_keys`. Do not claim the modification was independently correlated to an indexed Auditd file event.

## Searches performed and outcomes
- `host.name:"soc-linux" AND data_stream.dataset:"auditd.log"` — 153 documents in 14:35–14:47 IST.
- `host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.syscall:*` — 12 documents, including `write` syscall records.
- `host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.sequence:966` — nine documents, including `SYSCALL`, `PROCTITLE`, and `LOGIN`.
- Historical searches for `auditd.log.name`, `auditd.log.path`, `file.path`, and `auditd.log.record_type:"PATH"` — no results in the searched 14:10–14:50 IST window.

## Conclusions
1. Auditd ingestion into Elasticsearch is confirmed by returned documents.
2. SSH authentication and public-key re-entry are supported by supplied endpoint and Discover screenshots.
3. `write` syscall records alone do not identify the modified file.
4. Indexed path-level correlation for the authorized-key modification remains unconfirmed.
5. This is a historical evidence limitation; do not infer a current configuration failure without fresh endpoint-side validation.

## Evidence references
- `../09-Screenshots/Telemetry/P03-TEL-01-ssh-password-attack.png`
- `../09-Screenshots/Telemetry/P03-TEL-02-ssh-initial-access.png`
- `../09-Screenshots/Telemetry/P03-TEL-03-post-compromise-discovery.png`
- `../09-Screenshots/Telemetry/P03-TEL-04-authorized-key-persistence.png`
- `../09-Screenshots/Telemetry/P03-TEL-05-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png`
- `../09-Screenshots/Hunting/P03-HUNT-05-authorized-keys-activity.png`
- `../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png`
