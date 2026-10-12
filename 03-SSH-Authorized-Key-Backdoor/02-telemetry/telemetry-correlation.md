# Telemetry Correlation — SSH Authorized-Key Backdoor

## Correlation objective
Relate the password-authenticated SSH foothold, endpoint evidence of authorized-key persistence, and later public-key re-entry without overstating what each source proves.

## Evidence chain

| Stage | Endpoint evidence | Indexed evidence | Correlation status |
|---|---|---|---|
| SSH password attempts | Journal screenshot shows failed attempts and accepted password | Discover shows authentication-failure and `ssh_login` events | Corroborated at scenario level |
| Initial session | Journal shows session opened for `socadmin` from `192.168.1.10` | Authentication event visible | Corroborated |
| Authorized-key modification | Terminal screenshots show line count 1→2, permissions `600`, owner `socadmin`, modification time | No matching indexed Auditd PATH/path-field record found in searches performed | Endpoint evidence exists; indexed file-path correlation unresolved |
| Public-key re-entry | Journal shows accepted public key from `192.168.1.10` and session opened at 14:45:53 | Discover event at 14:45:53.827 has `ssh_login`, outcome `success`, method `publickey` | Corroborated |
| Post-re-entry privilege activity | Journal screenshot shows later sudo/session activity | Hunting screenshots show related sudo activity searches | Limit claims to fields visible in the evidence |

## Why the `write` syscall is insufficient
Inspected Auditd documents contain `auditd.log.syscall:"write"` and `auditd.log.record_type:"SYSCALL"`. One expanded event showed `process.executable:/usr/sbin/sshd`, `process.pid:4697`, and null `auditd.log.name`/`auditd.log.key`. These fields alone do not establish that the write targeted `/home/socadmin/.ssh/authorized_keys`.

A defensible file-write correlation would require an appropriate path record or another reliable event containing the target path and a defensible correlation key/timestamp. That evidence was not found in the searches performed.

## Known limitations
- This review is based on supplied screenshots and historical Discover searches, not a fresh export of every raw event.
- No-result queries describe only the data view and time window searched; they do not prove that no endpoint event ever existed.
- `log.file.path:/var/log/audit/audit.log` identifies the source log file and must not be confused with the modified target.
- Successful public-key re-entry supports authentication method and outcome, but is not standalone proof of the preceding file modification.
- Do not expose credentials, private keys, tokens, or unsanitized raw logs.

## Follow-up recommendation
During a future authorized validation window, compare a benign controlled authorized-keys file event with local `/var/log/audit/audit.log` and the corresponding Elasticsearch documents. Capture evidence before changing configuration. Do not rerun the historical attack solely to fill a documentation gap.
