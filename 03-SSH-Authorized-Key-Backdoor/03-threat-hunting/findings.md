# Threat-Hunting Findings — SSH Authorized-Key Backdoor

## Finding 1 — SSH authentication sequence
**Status: Corroborated by supplied evidence.** Elastic Discover and endpoint journal screenshots show authentication failures and successful SSH authentication for `socadmin` from `192.168.1.10`.

## Finding 2 — Authorized-key file change
**Status: Endpoint evidence observed; SIEM file-path correlation unresolved.** Terminal screenshots show the line count changing from one to two, permissions `600`, owner `socadmin`, and modification time around `14:40:56.572 +0530`. Historical Elasticsearch searches did not locate a PATH record or target-path field identifying the file.

## Finding 3 — Public-key re-entry
**Status: Corroborated by endpoint and Elastic evidence.** The endpoint journal shows accepted public-key authentication from `192.168.1.10` around 14:45:53. Elastic Discover shows a successful `ssh_login` event at `14:45:53.827`, with `system.auth.ssh.method:publickey` and `event.outcome:success`.

## Finding 4 — Auditd ingestion
**Status: Confirmed for the searched historical window.** The broad Auditd query returned 153 documents; a syscall query returned 12 documents including `write` events. This confirms searchable Auditd data but does not prove the authorized-key write was indexed with path context.

## Finding 5 — Detection and response
**Status: Not assessed in this hunt document.** This evidence review does not establish whether a detection alert fired or whether containment/remediation succeeded. Those belong to the separate detection and incident-response phases.

## Overall conclusion
The scenario is supported by endpoint file metadata and correlated SSH authentication evidence. The specific indexed Auditd file-path event remains unconfirmed. Preserve that limitation in reporting.
