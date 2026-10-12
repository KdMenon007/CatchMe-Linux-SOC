# Incident Response Playbook — SSH Authorized-Key Persistence

## 1. Preparation
- Maintain an approved key fingerprint inventory and an alternate administrative access method.
- Ensure endpoint, SSH, Auditd, and Elastic evidence can be preserved.
- Document lab IP mapping and operator authorization.

## 2. Identification
- Correlate authentication source, account, method, timestamp, endpoint, and session lifecycle.
- Capture authorized-keys metadata and fingerprints without publishing key material.
- Separate endpoint file-state evidence from SIEM path-level correlation.
- Record the selected time range and data view for all SIEM searches.

## 3. Containment
- Obtain approval before removing a key or terminating a session.
- Confirm which fingerprints are expected using a trusted independent source.
- Preserve the original state and confirm an alternate access path.
- Apply only targeted changes to confirmed unauthorized entries.

## 4. Eradication
- Remove only the confirmed unauthorized entry under an approved change.
- Preserve known-good keys and correct file ownership/mode if necessary.
- Record the before/after fingerprints and validation results.

## 5. Recovery
- Validate intended SSH access from a separate session.
- Verify Auditd and Elastic Agent health and end-to-end event indexing.
- Monitor for repeat authentication and persistence indicators.
- Do not treat a successful login test as proof that all telemetry is complete.

## 6. Lessons learned
- Preserve raw evidence and screenshot context.
- Do not infer target file paths from a syscall alone.
- Distinguish technique mapping from detection success.
- Track unresolved alert history and field correlation as explicit gaps.

## Current case state
The current key authorization is unresolved. Containment, eradication, and recovery are not verified. This playbook is guidance, not evidence that the steps were executed.
