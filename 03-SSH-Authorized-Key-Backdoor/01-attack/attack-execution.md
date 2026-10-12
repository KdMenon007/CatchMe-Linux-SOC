# Attack Execution — SSH Authorized-Key Backdoor

## Evidence handling
This document summarizes the supplied screenshots and does not claim to reproduce every command from the original session. No password or key material is included.

## Observed execution record
| Approximate IST time, 2026-10-03 | Source | Observed event | Limitation |
|---|---|---|---|
| 14:10:36–14:11:15 | Attack screenshot | Credential-discovery window; seven attempts and valid credential discovery recorded | Password intentionally redacted. |
| 14:10:47–14:11:16 | Endpoint journal | Failed password attempts followed by accepted password and session activity | Endpoint-side evidence. |
| Post-access | Attack screenshot | Terminal capture of host/process/network discovery | Exact output should be read from the original image. |
| 14:40:56.572142549 +0530 | File metadata screenshot | Target file, permissions `600`, owner/group `socadmin`, size 192 bytes, line count 2 | Shows resulting metadata; does not expose key content. |
| Around 14:40:11 | Endpoint journal and Elastic table | Password authentication and session lifecycle activity | Some normalized fields are null in the Elastic table. |
| 14:45:53 | Endpoint journal | Accepted public key from `192.168.1.10` for `socadmin`; session opened | Endpoint-side evidence. |
| 14:45:53.827 | Kibana Discover | Successful SSH login with method `publickey` from `192.168.1.10` | Elastic authentication event; not proof of a detection alert. |

## Interpretation notes
- The `authorized_keys` line-count change is direct terminal evidence of a file change, but does not identify the exact contents of the added line.
- Public-key re-entry is corroborated by endpoint journal output and an Elastic event.
- The authorized-keys Discover search shown in the screenshot returned no results for its displayed query/time range. Keep this as a query-specific gap until fields, query, data view, and time range are checked.
- The supplied evidence does not prove the exact file-write event was ingested into Elasticsearch.
- Successful authentication does not prove that a detection alert fired or incident response was completed.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-01-credential-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-02-ssh-initial-access.png`
- `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png`
- `../09-Screenshots/Attack/P03-ATT-05-authorized-key-verification.png`
- `../09-Screenshots/Attack/P03-ATT-06-key-based-reentry.png`
- `../09-Screenshots/Telemetry/P03-TEL-01-ssh-password-attack.png`
- `../09-Screenshots/Telemetry/P03-TEL-02-ssh-initial-access.png`
- `../09-Screenshots/Telemetry/P03-TEL-03-post-compromise-discovery.png`
- `../09-Screenshots/Telemetry/P03-TEL-04-authorized-key-persistence.png`
- `../09-Screenshots/Telemetry/P03-TEL-05-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png`
- `../09-Screenshots/Hunting/P03-HUNT-05-authorized-keys-activity.png`
- `../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png`
