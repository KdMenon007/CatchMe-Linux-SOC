# Containment Runbook — Approval Required

This is a proposed runbook, not a record of actions already taken.

## Preconditions
- Obtain explicit authorization for the target account and host.
- Verify trusted key fingerprints independently.
- Preserve current file metadata, key fingerprints, relevant logs, and screenshots.
- Confirm a tested alternate administrative session before modifying SSH access.
- Define rollback and identify who will validate the change.

## Controlled procedure
1. Capture a timestamped evidence snapshot and file hash before changes.
2. Compare each key fingerprint with the approved key inventory.
3. Identify only the fingerprint confirmed unauthorized.
4. Back up the authorized-keys file to a protected, access-controlled location.
5. Remove only the approved unauthorized entry; preserve known-good entries and correct file ownership/mode if needed.
6. Validate SSH configuration and intended access from a separate session before closing the existing administrative session.
7. Record exact commands, operator, approval, timestamps, before/after fingerprints, and test results.
8. Monitor SSH authentication and relevant endpoint/SIEM telemetry.

## Stop conditions
Stop without changes if authorization is ambiguous, no alternate access path exists, the key inventory cannot be trusted, or evidence preservation fails.

## Current execution status
Not executed. No keys or sessions have been changed by this project documentation update.
