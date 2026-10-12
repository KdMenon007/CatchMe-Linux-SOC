# Log Sources — SSH Authorized-Key Backdoor

## Confirmed sources

| Source | Observed evidence | Notes |
|---|---|---|
| SSH/system journal on `soc-linux` | Terminal screenshots show failed password attempts, accepted password authentication, session lifecycle, accepted public-key authentication, and session activity | Historical endpoint evidence dated 2026-10-03; source IP `192.168.1.10` |
| Auditd logfile integration | Discover returned documents from `data_stream.dataset:"auditd.log"` for `host.name:"soc-linux"` | One historical query returned 153 documents for 14:35–14:47 IST |
| Elastic authentication events | Discover shows successful `ssh_login` for `soc-linux`, `socadmin`, source `192.168.1.10`, method `publickey`, outcome `success` at `2026-10-03 14:45:53.827` | Authentication evidence, not a file-write alert |
| Elastic Defend endpoint telemetry | Previously validated in the lab baseline; process, file, and network datasets were observed | Do not attribute an individual event to this attack without inspecting that event |

## Auditd details observed during review
- `host.name:"soc-linux" AND data_stream.dataset:"auditd.log"` returned 153 documents for 2026-10-03 14:35–14:47 IST.
- `auditd.log.syscall:*` returned 12 documents, including `write` syscall records around 14:40:11.939, 14:45:01.394, and 14:45:53.828.
- `auditd.log.sequence:966` returned nine records at 14:40:11.939, including `SYSCALL`, `PROCTITLE`, and `LOGIN`.
- Around 14:45:53.828, displayed records included `SYSCALL`, `PROCTITLE`, `LOGIN`, and `CRED_ACQ`; nearby records included `USER_START`, `CRED_ACQ`, and `USER_LOGIN`.
- In inspected documents, `auditd.log.name` and `auditd.log.key` were null. `log.file.path` pointed to `/var/log/audit/audit.log`, the source log file rather than the modified target.

## Search limitation
Historical searches for the target filename using `auditd.log.name`, `auditd.log.path`, `file.path`, and `auditd.log.record_type:"PATH"` returned no results in the searched October 3, 2026 14:10–14:50 IST window. This describes the searches performed; it does not prove no endpoint event ever existed.
