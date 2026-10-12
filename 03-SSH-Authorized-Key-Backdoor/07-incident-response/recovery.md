# Recovery

## Status
Recovery is **not verified**. No recovery changes are recorded in the current evidence.

## Proposed recovery checks
- Confirm approved SSH key fingerprints and access paths.
- Confirm the `socadmin` account and sudo privileges are appropriate for the lab.
- Validate SSH service configuration against the intended baseline.
- Confirm Auditd and Elastic Agent are active and telemetry reaches Elasticsearch.
- Run approved, non-destructive authentication validation from a separate session.
- Confirm relevant SSH, Auditd, and endpoint telemetry is searchable.
- Record before/after evidence and verify the historical detection limitation separately from recovery.

## Current caveats
- SSH is active but disabled at boot in the observed configuration.
- The current key authorization status is unresolved.
- Active SSH access from Kali matches the operator's current session.
- Historical detection outcome and indexed authorized-keys path correlation remain unresolved.

Do not change service startup behavior or SSH authentication settings solely to make a status appear healthier; first compare them with the intended lab baseline and obtain approval.
