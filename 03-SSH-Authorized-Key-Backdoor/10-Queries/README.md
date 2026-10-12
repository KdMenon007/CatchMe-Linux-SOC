# Project 03 KQL Query Index

This index lists the existing defensive investigation queries for the SSH Authorized-Key Backdoor lab. Run them only in the authorized lab and set the intended historical time range in Elastic Discover before interpreting results.

## Lab scope

- Attacker/Kali: `192.168.1.10`
- Linux endpoint: `soc-linux` (`192.168.1.16`)
- SIEM: `elastic-siem` (`192.168.1.11`)
- Account: `socadmin`
- Historical investigation window used in prior notes: October 3, 2026, approximately 14:10–14:50 IST (`UTC+05:30`)

The time range above describes the documented historical investigation; it is not automatically applied by the KQL itself.

## Existing KQL queries

| Query | Purpose | Known caveat |
|---|---|---|
| [`01-ssh-authentication.kql`](KQL/01-ssh-authentication.kql) | Search SSH authentication and Auditd login records for the endpoint. | Validate which event-action values and fields are populated in the current data. |
| [`02-authorized-keys-path.kql`](KQL/02-authorized-keys-path.kql) | Search Auditd `PATH` records for `/home/socadmin/.ssh/authorized_keys`. | Prior searches returned no results in the searched window. This does not prove the file was never modified. |
| [`03-auditd-syscall.kql`](KQL/03-auditd-syscall.kql) | Review Auditd syscall records. | A generic `write` syscall does not identify the target file unless reliable path-level correlation is available. |
| [`04-publickey-reentry.kql`](KQL/04-publickey-reentry.kql) | Search for successful public-key SSH re-entry from the Kali lab host. | Uses fields observed in prior screenshots; confirm mapping and results in the target Elastic data view. |
| [`05-ssh-session-lifecycle.kql`](KQL/05-ssh-session-lifecycle.kql) | Review authentication and SSH session lifecycle events for `socadmin`. | Confirm `event.action` values and the selected time range against actual indexed events. |
| [`06-sudo-activity.kql`](KQL/06-sudo-activity.kql) | Search for sudo-related activity. | Sudo process events alone do not establish the executed command; prior screenshots showed a null `process.command_line`. |
| [`07-detection-rule-hunt.kql`](KQL/07-detection-rule-hunt.kql) | Search Auditd syscall records matching the recorded custom detection query. | A matching Discover result is not proof that the live detection rule generated an alert. Review rule execution history and alert evidence separately. |

## Supporting query notes

See [`supporting-queries/README.md`](supporting-queries/README.md) for the Auditd ingestion baseline and candidate target-path field checks.

## Evidence interpretation guardrails

- A zero-result query applies only to its selected data view, filters, fields, and time range.
- A candidate field name is not confirmed merely because it appears in a query. Verify that the field exists and has the expected values in the actual indexed documents.
- Do not attribute a generic Auditd syscall to `authorized_keys` without reliable path-level evidence.
- Keep observed facts, historical notes, inferences, and unresolved questions separate.
- Do not claim detection success or failure based only on a hunt query. Correlate the live rule definition, execution history, time range, and alert status.
- These queries are documented artifacts; this index update does not execute them or independently validate current Elasticsearch data.

## Evidence references

- Hunting findings and screenshot references: [`../03-threat-hunting/hunting-overview.md`](../03-threat-hunting/hunting-overview.md)
- Detection screenshot limitations: [`../09-Screenshots/Detection/README.md`](../09-Screenshots/Detection/README.md)
- Main project evidence strategy: [`../README.md`](../README.md), Section 18
