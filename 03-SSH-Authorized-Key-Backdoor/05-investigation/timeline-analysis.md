# Timeline Analysis

**Timezone:** Asia/Kolkata (IST, UTC+05:30). Times below are the documented historical observations, not a complete event log.

| Approximate time (IST), 2026-10-03 | Observation | Evidence strength / limitation |
|---|---|---|
| Before 14:40 | SSH password attempts and initial access documented | Existing Attack/Telemetry screenshots; exact individual timestamps should be read from original evidence |
| 14:40:11.939 | Auditd `write` syscall indexed for `/usr/sbin/sshd`, PID 4697, sequence 966 | Target file path not present in reviewed fields; not proof of authorized-keys write |
| 14:40:56 | Endpoint evidence records authorized-keys file modification time | File-state observation; reviewed indexed path correlation unresolved |
| 14:43:06.864 | Sudo-related event observed | Exact command not established; `process.command_line` null in reviewed record |
| 14:45:53.827 | Successful SSH public-key re-entry from `192.168.1.10` for `socadmin` on `soc-linux` | Event fields included method `publickey` and outcome `success` |
| 14:46:49.259 | Sudo-related event observed after re-entry | Exact command not established; do not infer its effect |

## Correlation notes
- The table is intentionally not a complete attack timeline; use the source screenshots and phase notes for exact context.
- Events with close timestamps are not automatically the same operation.
- The detection alert outcome for the Oct 3 window remains not established.
