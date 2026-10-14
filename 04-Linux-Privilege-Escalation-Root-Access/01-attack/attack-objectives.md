# Attack Objectives — Phase 01

## Purpose
Record the privilege-escalation objectives and the evidence required to determine whether each objective was achieved. This phase documents the actual lab activity; it does not imply that every listed technique was executed.

## Objectives
- Assess the relevant Linux privilege boundaries identified during pre-attack checks.
- Record the controlled Task 8 cron-permission demonstration and its root-context execution evidence.
- Keep Task 9 cron PATH and Task 10 cron wildcard work classified as baseline-only.
- Document SUID/SGID, shared-library, environment, shell, history/configuration, SSH-key, NFS, kernel, and assessment-tool checks without presenting them as successful exploits.
- Record the exact execution time and Elastic time range only when supported by terminal or SIEM evidence.
- Preserve the lab VM for future projects and avoid introducing new system weaknesses.

## Scope
- Target: `soc-linux` (`192.168.1.16`)
- Attacker workstation: Kali Linux (`192.168.1.10`)
- SIEM: Elastic (`192.168.1.11`)
- Timezone: `Asia/Kolkata` (IST, UTC+05:30)

## Safety controls
- Do not alter system cron configuration, sudoers, `/etc/passwd`, `/etc/shadow`, SUID bits, or system executable permissions on the protected target.
- Do not run shared-object injection, shell-feature, or kernel exploits on this VM.
- Use a disposable VM or restorable snapshot for any attack requiring deliberate misconfiguration.
- Preserve `/opt/catchme-p04-lab/validation.log` as evidence.
- Remove temporary test artifacts only after confirming they are no longer referenced.

## Completion criteria
For each task, state the objective, actions actually performed, result, evidence, cleanup, and limitations. Mark an attack as successful only when outcome evidence supports the claim.
