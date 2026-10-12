# Account Investigation

## Scope
Review the `socadmin` account activity associated with SSH access to `soc-linux`.

## Observations
- Existing Attack/Telemetry evidence documents SSH password-attack activity and an initial SSH session.
- Existing Hunting evidence documents a successful public-key SSH re-entry at `2026-10-03 14:45:53.827 IST`.
- The re-entry event records source IP `192.168.1.10`, host `soc-linux`, user `socadmin`, method `publickey`, and outcome `success`.

## Interpretation
The evidence supports successful use of public-key authentication for `socadmin` from the Kali lab host during the documented scenario.

## Gaps
- The exact credential value must remain redacted and must not be copied into project documentation.
- The evidence summarized here does not establish credential rotation or account remediation.
- Do not infer account creation, password changes, or broader account compromise without corresponding events.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-01-credential-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-02-ssh-initial-access.png`
- `../09-Screenshots/Attack/P03-ATT-06-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png`
- `../09-Screenshots/Hunting/P03-HUNT-03-authentication-methods.png`
