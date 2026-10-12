# Triage Playbook

## Purpose
Collect enough evidence to scope the SSH authorized-key persistence scenario without disrupting the lab or destroying access.

## Initial triage observations
- Historical activity: SSH password attempts, initial access, authorized-keys file-state change, and later public-key re-entry.
- Current endpoint: `soc-linux` at `192.168.1.16`.
- Current source session: `192.168.1.10` to endpoint SSH port 22; this matches the operator's current lab session.
- Current authorized-keys file: two public-key entries and one blank line; neither fingerprint is confirmed authorized.
- Auditd and Elastic Agent services were active in the captured endpoint state.

## Safe triage sequence
1. Record timestamp, hostname, IP, account identity, and active sessions.
2. Record file metadata and SHA-256 fingerprints without copying key contents into public notes.
3. Preserve relevant SSH journal/audit logs and relevant Elastic screenshots/query context.
4. Confirm key authorization through a trusted source such as the operator's local public-key inventory or an approved lab record.
5. Check the active session is the expected operator session before planning containment.
6. Record any unavailable evidence or command errors; do not infer success.

## Do not
- Remove a key because its comment looks unfamiliar.
- Assume the third line is a key; current inspection showed it is blank.
- Treat an active SSH connection from the known Kali host as malicious without further context.
- Claim detection, containment, or remediation from file metadata alone.

## Escalation criteria
Escalate to an authorized containment decision if a fingerprint is confirmed unauthorized, unexpected access is identified, or evidence shows continued unauthorized use. Preserve the current session until an alternate access path is confirmed.
