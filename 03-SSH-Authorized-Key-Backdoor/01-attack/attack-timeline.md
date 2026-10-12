# Attack Timeline — SSH Authorized-Key Backdoor

All times use IST (`UTC+05:30`) unless an offset is explicitly shown. Some endpoint journal entries display second-level precision.

| Timestamp | Source | Observed event | Confidence / limitation |
|---|---|---|---|
| 2026-10-03 14:10:36–14:11:15 | Attack screenshot | Credential-discovery window; seven attempts and valid credential discovery recorded | Screenshot summary; password redacted. |
| 2026-10-03 14:10:47.830 | Elastic Discover | `authentication_failure`, user `socadmin`, source `192.168.1.10`, process `sshd` | Visible in SSH event table. |
| 2026-10-03 14:11:16.516 | Elastic Discover | `ssh_login` for `socadmin`, source `192.168.1.10`, process `sshd` | Event row visible; avoid claiming more than displayed fields. |
| 2026-10-03 14:40:11 | Endpoint journal | Accepted password for `socadmin` from `192.168.1.10`; session opened | Endpoint journal evidence. |
| 2026-10-03 14:40:11.935–14:40:12.311 | Elastic Discover | Lifecycle actions include `authenticated`, `was-authorized`, `acquired-credentials`, `started-session`, `ended-session`, and `disposed-credentials` | Visible in event table; some normalized fields are null. |
| 2026-10-03 14:40:56.572142549 +0530 | Endpoint file metadata | `authorized_keys` modification time; permissions `600`; owner/group `socadmin`; line count 2 | Metadata screenshot; exact write event in Elasticsearch not established. |
| 2026-10-03 14:45:53 | Endpoint journal | Accepted public key for `socadmin` from `192.168.1.10`; session opened | Endpoint journal evidence. |
| 2026-10-03 14:45:53.827 | Elastic Discover | Successful `ssh_login`; method `publickey`; source `192.168.1.10`; user `socadmin`; host `soc-linux` | Event detail shows `event.outcome: success`. |

## Timeline interpretation
Combined evidence supports password-based access, a later authorized-key file change, and public-key re-entry. It does not yet prove that the exact file-write event was searchable in Elasticsearch, nor does this timeline assert detection or incident-response success.

## Evidence references
Use the original screenshots in `../09-Screenshots/Attack/`, `../09-Screenshots/Telemetry/`, and `../09-Screenshots/Hunting/`. Specific screenshot filenames are listed in `attack-scenario.md`.
