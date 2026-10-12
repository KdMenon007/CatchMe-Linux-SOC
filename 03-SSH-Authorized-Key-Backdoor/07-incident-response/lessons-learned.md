# Lessons Learned

## Evidence handling
- Endpoint file metadata and successful public-key authentication support the scenario, but do not alone identify the exact contents or authorization status of a key.
- The current file has two key lines and one blank line; the blank line is not a key.
- Fingerprint comments are labels, not proof of authorization.
- A source log path such as `/var/log/audit/audit.log` must not be confused with the target file path.

## Detection and telemetry
- Indexed Auditd syscall events do not prove a specific file write when target path context is missing.
- A query returning no results is scoped to its fields, data view, and time range.
- A technique mapping does not prove an alert fired.
- Service active state does not prove all expected events are indexed.

## Response discipline
- Preserve the current session until an alternate access path is verified.
- Do not remove a key when its authorization status is unknown.
- Record actions as planned, performed, or verified; never conflate these states.
- Do not claim containment or remediation without before/after evidence.

## Improvement opportunities
- Maintain a trusted, current key fingerprint inventory.
- Improve file-path event correlation and document integration/field limitations.
- Retain rule execution history and relevant alert status evidence.
- Capture timestamped before/after artifacts for any authorized response change.
