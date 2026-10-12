# Incident Overview — SSH Authorized-Key Persistence

## Incident summary
This project investigates an SSH authorized-key persistence scenario on `soc-linux` (`192.168.1.16`), with Kali (`192.168.1.10`) as the observed source. Historical endpoint evidence documents a change to `/home/socadmin/.ssh/authorized_keys` followed by successful public-key SSH re-entry.

## Current state reviewed on 2026-10-12
- Endpoint hostname/IP: `soc-linux`, `192.168.1.16`.
- Account: `socadmin`, UID/GID 1000; member of `sudo`.
- SSH service: active at time of check; service disabled at boot.
- Current SSH connection: `192.168.1.10` connected to endpoint port 22; operator identified this as the current lab session.
- Authorized-keys file: owner/group `socadmin`, mode `600`, size 193 bytes, modified `2026-10-08 04:20:17.769639322 +0530`.
- File structure: two public-key lines and one blank line.
- Fingerprints observed:
  - `SHA256:GLCdLwYcUo0gk+BTJBCMjNheRR6gf/Zw3RdAI0dxUVA` (`kiran@kiran`)
  - `SHA256:ZS2FveMLbhH81yQ/g+FpmUtytCchrecZrOS13EDa5ww` (`catchme-project03`)
- The operator is unsure whether either fingerprint is intentionally authorized.
- Auditd was active; `lost 0` and `backlog 0` were shown in the captured status.
- Elastic Agent service was active in the captured endpoint check.

## Assessment
The historical persistence scenario is supported by prior endpoint evidence. Current key authorization is unresolved. A key comment is not proof of ownership or authorization, and current file state alone does not establish that either present key is the historical unauthorized key.

## Status
- Triage: current endpoint metadata collected.
- Containment: not performed / not verified.
- Eradication: not performed / not verified.
- Recovery: not performed / not verified.
- Detection outcome for the historical October 3 window: not established.
- Current key authorization: unresolved.

## Handling constraints
Do not publish key contents, remove keys, terminate sessions, rotate credentials, or change SSH configuration until authorization and access requirements are established. Preserve evidence before any authorized response action.
