# Incident Report — SSH Authorized-Key Persistence Scenario

## Executive summary
A lab scenario documented SSH password attempts, initial access, an authorized-keys file-state change, and later successful public-key re-entry to `soc-linux`. The historical behavior is supported by endpoint and Elastic authentication evidence. Historical detection outcome and indexed path-level correlation remain unresolved.

## Scope and timeline
- Source: Kali `192.168.1.10`
- Endpoint: `soc-linux` `192.168.1.16`
- Account: `socadmin`
- Historical file metadata recorded around `2026-10-03 14:40:56 IST`
- Successful public-key SSH login observed at `2026-10-03 14:45:53.827 IST`
- Current-state review performed on `2026-10-12`

## Current-state findings
- Authorized-keys file has two public-key entries and one blank line.
- Current fingerprints: `SHA256:GLCdLwYcUo0gk+BTJBCMjNheRR6gf/Zw3RdAI0dxUVA` and `SHA256:ZS2FveMLbhH81yQ/g+FpmUtytCchrecZrOS13EDa5ww`.
- Operator could not confirm whether either fingerprint is expected.
- An active SSH session from Kali matches the operator's current lab session.
- Auditd and Elastic Agent were active at the time of the captured endpoint checks.

## Impact
No broader business impact, data exfiltration, or destructive impact is established by the evidence reviewed for this lab project.

## Response status
- Triage: performed through read-only metadata/session/service checks.
- Containment: not performed / not verified.
- Eradication: not performed / not verified.
- Recovery: not performed / not verified.
- Lessons learned: documented in `lessons-learned.md`.

## Key limitations
Do not label either current key malicious without independent authorization evidence. The current file state does not identify which entry, if any, corresponds to the historical persistence activity. No claim is made that detection fired, or that the endpoint has been remediated.
