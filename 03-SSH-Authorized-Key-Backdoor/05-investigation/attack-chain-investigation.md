# Attack-Chain Investigation

## Evidence-led sequence
1. **Credential discovery / SSH password attempts:** documented in existing Attack and Telemetry screenshots.
2. **Initial access:** an initial SSH session is documented in the existing evidence.
3. **Post-compromise discovery:** endpoint discovery activity is documented in existing screenshots; this document does not infer commands beyond what the screenshots show.
4. **Authorized-key persistence:** existing evidence records the authorized-keys file line count changing from one to two, with mode `600` and owner/group `socadmin`.
5. **Public-key re-entry:** a successful `publickey` SSH event was observed at `2026-10-03 14:45:53.827 IST` from `192.168.1.10` to `soc-linux` for `socadmin`.

## Correlation confidence
The sequence is supported by existing endpoint, SIEM, and screenshot evidence, but not every step is linked by a single event identifier. The reviewed Auditd `write` syscall does not identify the target path in the indexed event fields.

## Unresolved questions
- Can the historical file modification be linked to a specific Auditd `PATH` record in the indexed data?
- Was an alert generated outside the selected time range or alert view?
- What exact commands were run during the sudo activity? Reviewed `process.command_line` fields were null.
- Is there evidence of verified cleanup and post-remediation validation? Not established in the reviewed material.

## Conclusion
Describe this as an evidence-supported scenario sequence with unresolved telemetry correlation. Do not claim complete end-to-end event correlation or proven detection failure.
