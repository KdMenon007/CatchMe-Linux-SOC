# Sigma Analysis — SSH Authorized-Key Modification

## Status
Analysis document only. No Sigma rule has been validated, executed, or deployed as part of this phase.

## Mapping considerations
A portable Sigma rule would need to map the actual collected event fields for host, Auditd key, record type, syscall, and target path. Field names and semantics must be checked against representative raw/indexed events before authoring a production-ready rule.

## Why no rule is supplied yet
The historical review did not confirm an indexed PATH/path-field event for `/home/socadmin/.ssh/authorized_keys` in the searched window. Writing a Sigma rule with assumed field names or unsupported path semantics would overstate the evidence.

## Next step
Use the genuine Elastic alert and raw Auditd event documents to define field mappings, then author and test a Sigma rule separately. Do not treat the empty `sigma-rule.yml` placeholder as a working detection.
