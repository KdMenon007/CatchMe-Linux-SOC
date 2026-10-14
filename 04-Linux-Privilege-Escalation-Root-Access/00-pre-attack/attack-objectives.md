# Attack Objectives — Project 04

## Purpose
Assess common Linux privilege-escalation paths on the CatchMe `soc-linux` lab host, correlate observable activity with Elastic SIEM, and preserve evidence that distinguishes a confirmed outcome from baseline enumeration.

## Objectives
1. Record the target operating system, architecture, kernel, users, and relevant package versions.
2. Inspect permissions and configurations related to cron, SUID/SGID executables, shell behavior, credentials, SSH keys, NFS, and kernel security.
3. Validate endpoint process telemetry for commands executed during the assessment.
4. Document the controlled Task 8 cron-permission demonstration separately from baseline-only tasks.
5. Preserve raw evidence and screenshots while avoiding collection of secrets.
6. Verify cleanup of temporary test artifacts without removing the validation log.
7. Prepare evidence-based findings and MITRE ATT&CK mappings.

## Scope and guardrails
- Target: `soc-linux` (`192.168.1.16`).
- SIEM: Elastic (`192.168.1.11`).
- Attacker workstation: Kali Linux (`192.168.1.10`).
- Timezone for records: `Asia/Kolkata` (IST, UTC+05:30).
- The target VM is reserved for future portfolio work. Do not introduce additional misconfigurations or run kernel exploits, library injection, or other privilege-escalation exploits on it.
- Any attack requiring deliberately weakened configuration must be tested on a disposable VM or a restorable snapshot.

## Success criteria
- Each task has a documented objective, exact procedure, result, evidence reference, and honest status.
- Confirmed attack impact is supported by host-side proof and corroborating telemetry where available.
- Baseline checks are not labelled as successful attacks.
- Cleanup is verified and evidence is preserved.

## Out of scope
- Destructive changes to system configuration.
- Credential extraction or disclosure.
- Exploitation of unrelated systems.
- Claims of detection alerts unless an actual alert is present in evidence.
