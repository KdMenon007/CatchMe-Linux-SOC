# Hunt Timeline — SSH Authorized-Key Backdoor

All times are historical and use IST (`UTC+05:30`) unless otherwise shown.

| Timestamp | Evidence source | Observation | Confidence |
|---|---|---|---|
| 2026-10-03 14:10:47.830 | Elastic Discover | SSH `authentication_failure` for `socadmin` from `192.168.1.10` | Indexed event visible |
| 2026-10-03 14:11:16.516 | Elastic Discover / endpoint screenshots | SSH login event and endpoint session activity | Authentication/session evidence visible |
| 2026-10-03 14:40:11.939 | Elastic Discover | Auditd `SYSCALL` `write` record; sequence `966` has nine related documents | Write syscall indexed; target file not identified |
| 2026-10-03 14:40:56.572... +0530 | Endpoint terminal screenshot | `authorized_keys` metadata shows permissions `600`, owner `socadmin`, and line count 2 | Endpoint file metadata visible |
| 2026-10-03 14:45:53 | Endpoint journal screenshot | Accepted public-key authentication from `192.168.1.10` for `socadmin`; session opened | Endpoint evidence visible |
| 2026-10-03 14:45:53.827 | Elastic Discover | Successful public-key `ssh_login` for `socadmin` on `soc-linux` | Indexed authentication event visible |

## Timeline limitation
The time relationship supports the documented scenario, but the inspected Elasticsearch searches did not identify an indexed PATH/path-field record tying a write to `authorized_keys`. Do not infer that the generic write syscall at 14:40:11.939 was the file modification at 14:40:56.
