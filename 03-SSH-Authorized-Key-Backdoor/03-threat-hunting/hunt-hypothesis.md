# Hunt Hypothesis — SSH Authorized-Key Backdoor

## Primary hypothesis
A successful SSH password login from the Kali lab host was followed by a modification to `socadmin`'s `authorized_keys` file, enabling later SSH public-key re-entry from the same source.

## Supporting observations
- Endpoint journal screenshots show accepted password authentication from `192.168.1.10` for `socadmin`.
- Endpoint terminal screenshots show the authorized-key line count changed from one to two, with permissions `600` and owner `socadmin`.
- Endpoint journal evidence shows accepted public-key authentication from `192.168.1.10` at approximately 14:45:53 IST.
- Elastic Discover shows a successful public-key `ssh_login` event at `14:45:53.827`.

## Unresolved element
The supplied Elasticsearch searches did not locate a `PATH` record or indexed path field identifying `/home/socadmin/.ssh/authorized_keys` in the searched historical window. Therefore, the persistence-to-re-entry chain is supported by separate endpoint and authentication evidence, but a specific indexed file-write event has not been correlated.

## Alternative explanations and cautions
- A generic `write` syscall may concern another file; do not attribute it to `authorized_keys` without path evidence.
- The `log.file.path` value `/var/log/audit/audit.log` is the source log, not the modified target.
- Events near SSH re-entry may relate to session handling and are not automatically evidence of persistence.
- The hypothesis is lab-scoped and must not be presented as a real-world compromise.

## Assessment
**Partially supported.** Authentication and endpoint file-change evidence exist; indexed path-level correlation remains unconfirmed.
