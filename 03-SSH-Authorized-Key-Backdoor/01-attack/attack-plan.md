# Attack Plan — SSH Authorized-Key Backdoor

## Objective
In the authorized lab, assess whether a password-authenticated SSH foothold followed by an `authorized_keys` change can be correlated with later public-key re-entry.

## Fixed lab mapping
| Role | Host | Address |
|---|---|---|
| Attacker | Kali (`kiran`) | `192.168.1.10` |
| Target | `soc-linux` | `192.168.1.16` |
| SIEM | `elastic-siem` | `192.168.1.11` |

Target account in the evidence: `socadmin`. Timezone: IST (`UTC+05:30`).

## Planned stages
1. Record baseline and target identity.
2. Perform the controlled SSH password-access test.
3. Record limited post-access host and process discovery.
4. Record the controlled authorized-key change without publishing key material.
5. Verify file permissions, ownership, and line count.
6. Test and record public-key re-entry.
7. Correlate endpoint journal events with Elastic authentication telemetry.
8. Record query gaps and detection/response evidence separately.

## Safety controls
- Use only dedicated lab hosts and the documented test account.
- Do not include passwords, private keys, tokens, or raw key material.
- Do not repeat the attack merely to regenerate screenshots.
- Preserve timestamps and the `UTC+05:30` offset.
- Treat a successful login event as authentication evidence, not proof of detection or incident response.

## Observed outcome
The supplied screenshots support password login, an authorized-key line-count change, file metadata verification, and later public-key re-entry. They do not establish that every telemetry or response check passed.

## Evidence
See `attack-scenario.md` and the screenshots under `../09-Screenshots/Attack/`, `../09-Screenshots/Telemetry/`, and `../09-Screenshots/Hunting/`.
