# Attack Scenario — SSH Authorized-Key Backdoor

## Purpose
Document the observed lab scenario in which SSH password authentication was followed by an authorized-key file change and later public-key SSH re-entry.

## Lab scope
- Attacker: Kali, `192.168.1.10`
- Target: `soc-linux`, `192.168.1.16`
- Account observed: `socadmin`
- Target file: `/home/socadmin/.ssh/authorized_keys`
- Timezone: IST (`UTC+05:30`)
- Evidence date: 2026-10-03

## Evidence-supported sequence
1. The credential-discovery screenshot records seven attempts and a valid credential discovered; the password is redacted.
2. The endpoint journal shows an accepted password login for `socadmin` from `192.168.1.10` at approximately `14:40:11` IST.
3. The endpoint terminal records the `authorized_keys` line count changing from 1 to 2. It shows permissions `600`, owner/group `socadmin`, and modification time `2026-10-03 14:40:56.572142549 +0530`.
4. The endpoint journal shows an accepted public-key login from `192.168.1.10` at `14:45:53`, followed by a session-open event.
5. A Kibana Discover event at `2026-10-03 14:45:53.827` shows `host.name: soc-linux`, `source.ip: 192.168.1.10`, `user.name: socadmin`, `event.action: ssh_login`, `event.outcome: success`, and `system.auth.ssh.method: publickey`.

## Interpretation
The screenshots support a sequence consistent with authorized-key persistence and subsequent public-key re-entry, corroborated by endpoint journal output and an Elastic authentication event.

## Limitations
- Passwords and key material are intentionally excluded.
- The displayed authorized-keys Discover query returned no results. That query-specific result does not prove all relevant telemetry is absent.
- The exact authorized-key write event in Elasticsearch has not been established by the supplied screenshots alone.
- This is a lab scenario, not evidence of activity on a production system.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-01-credential-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-02-ssh-initial-access.png`
- `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png`
- `../09-Screenshots/Attack/P03-ATT-05-authorized-key-verification.png`
- `../09-Screenshots/Attack/P03-ATT-06-key-based-reentry.png`
- `../09-Screenshots/Telemetry/P03-TEL-01-ssh-password-attack.png`
- `../09-Screenshots/Telemetry/P03-TEL-04-authorized-key-persistence.png`
- `../09-Screenshots/Telemetry/P03-TEL-05-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png`
- `../09-Screenshots/Hunting/P03-HUNT-05-authorized-keys-activity.png`
- `../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png`
