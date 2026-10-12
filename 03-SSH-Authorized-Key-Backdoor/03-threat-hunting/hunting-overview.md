# Threat-Hunting Overview — SSH Authorized-Key Backdoor

## Objective
Investigate the documented SSH password-access, authorized-key persistence, and public-key re-entry scenario using existing endpoint evidence and Elasticsearch events.

## Scope
- Attacker: Kali `192.168.1.10`
- Target: `soc-linux` `192.168.1.16`
- Account: `socadmin`
- Historical window: 2026-10-03, approximately 14:10–14:50 IST (`UTC+05:30`)
- SIEM: `elastic-siem` `192.168.1.11`

## Hunt questions
1. Were SSH authentication failures followed by successful authentication from the same source?
2. Can the later public-key login be tied to `socadmin` and the expected source host?
3. What endpoint and Auditd events are visible around the suspected persistence period?
4. Is there an indexed file-path event identifying `/home/socadmin/.ssh/authorized_keys`?
5. Which conclusions are corroborated, and which remain uncertain?

## Evidence-backed summary
- Elasticsearch screenshots show SSH authentication events from `192.168.1.10` for `socadmin`.
- A successful public-key `ssh_login` event at `2026-10-03 14:45:53.827` is visible for `soc-linux`, with `event.outcome:success` and `system.auth.ssh.method:publickey`.
- Endpoint terminal screenshots show the `authorized_keys` line count changing from one to two and display file metadata.
- Auditd documents are searchable; one broad query returned 153 documents in the 14:35–14:47 IST window.
- A syscall query returned 12 documents including `write` records, but the inspected documents did not identify the target path.
- Historical searches for `PATH` records and candidate target-path fields returned no results in the searched window.
- SSH lifecycle screenshots show successful `sshd` events including `was-authorized`, `acquired-credentials`, and `started-session` around 14:45:53–14:45:54.
- Sudo screenshots show `/usr/bin/sudo` events at 14:43:06.864 and 14:46:49.259. The earlier event predates public-key re-entry; the later event follows it. The visible `process.command_line` field is null, so the executed command is not established by those screenshots.

## Hunt outcome
The authentication sequence is corroborated by endpoint and Elastic evidence. Endpoint evidence supports the authorized-key file change. Indexed file-path correlation for that change remains unresolved. Sudo executable activity is visible, but the screenshot fields do not establish the exact command. Do not describe the hunt as proving an indexed Auditd write to the target file or a specific sudo command.

## Evidence references
All links below are relative to this Markdown file and point to existing screenshot files.

### Authentication and public-key re-entry
- [P03-HUNT-01 — SSH attack timeline](../09-Screenshots/Hunting/P03-HUNT-01-ssh-attack-timeline.png)
- [P03-HUNT-02 — Public-key event at 14:45:53](../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png)
- [P03-HUNT-02 — Authentication correlation](../09-Screenshots/Hunting/P03-HUNT-02-authentication-correlation.png)
- [P03-HUNT-03 — Authentication methods](../09-Screenshots/Hunting/P03-HUNT-03-authentication-methods.png)
- [P03-HUNT-04 — Public-key re-entry](../09-Screenshots/Hunting/P03-HUNT-04-publickey-reentry.png)
- [P03-HUNT-05 — SSH key re-entry](../09-Screenshots/Hunting/P03-HUNT-05-ssh-key-reentry.png)
- [P03-HUNT-07 — SSH key re-entry](../09-Screenshots/Hunting/P03-HUNT-07-ssh-key-reentry.png)

### Authorized-key and Auditd evidence
- [P03-HUNT-05 — Authorized-keys activity](../09-Screenshots/Hunting/P03-HUNT-05-authorized-keys-activity.png)
- [P03-HUNT-06 — Auditd persistence telemetry](../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png)

### Sudo and session lifecycle
- [P03-HUNT-07 — Sudo activity](../09-Screenshots/Hunting/P03-HUNT-07-sudo-activity.png)
- [P03-HUNT-07 — Sudo command activity (14:43)](../09-Screenshots/Hunting/P03-HUNT-07-sudo-command-activity.png)
- [P03-HUNT-08 — SSH session correlation](../09-Screenshots/Hunting/P03-HUNT-08-ssh-session-correlation.png)
- [P03-HUNT-08 — SSH session lifecycle](../09-Screenshots/Hunting/P03-HUNT-08-ssh-session-lifecycle.png)
- [P03-HUNT-09 — Post-re-entry sudo activity (14:46:49)](../09-Screenshots/Hunting/P03-HUNT-09-post-reentry-sudo-activity.png)

## Screenshot interpretation cautions
- The 14:43:06.864 sudo event occurs before the visible public-key re-entry at 14:45:53.827.
- The 14:46:49.259 sudo events occur after the visible public-key re-entry.
- `process.command_line` is null in the supplied sudo screenshots; do not invent the command or claim a specific privileged action.
- SSH lifecycle events support session activity but do not by themselves prove the authorized-key file was modified.
- The inspected Elasticsearch searches did not identify a `PATH` record or indexed path field tying a write to `/home/socadmin/.ssh/authorized_keys`.

## Phase status
Screenshot files and hashes were checked locally, and five screenshots were inspected in this review. This update repairs the documentation references and interpretation; it does not rerun KQL or independently revalidate the live Elasticsearch data.
