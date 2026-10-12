# Initial Triage — SSH Authorized-Key Backdoor

## Triage summary
The evidence describes SSH password authentication from `192.168.1.10`, endpoint-side authorized-key file change, and later public-key SSH re-entry for `socadmin` on `soc-linux`.

## Key observations
- Elastic Discover shows an SSH authentication failure at `2026-10-03 14:10:47.830`.
- Elastic and endpoint screenshots support password-authenticated access around 14:11 and another accepted password session around 14:40.
- Endpoint terminal evidence records the `authorized_keys` line count changing from one to two, with permissions `600`, owner `socadmin`, and modification time around `14:40:56.572 +0530`.
- Elastic Discover shows a successful public-key login at `14:45:53.827`.
- Auditd documents exist in Elasticsearch, including `write` syscall records; inspected records did not identify the target file.

## Initial assessment
The authentication activity and later public-key re-entry are corroborated. Endpoint screenshots support the file change. Indexed file-path correlation is unresolved, so do not mark the specific Auditd persistence event as confirmed.

## Triage limitations
This is a retrospective lab investigation based on screenshots and searches, not a live incident response. No claim is made that an alert fired, an attacker was contained, or remediation was completed.

## Recommended follow-up
Preserve the current evidence. If additional validation is required later, compare a benign controlled file event with local Auditd records and corresponding Elasticsearch documents before changing any configuration.
