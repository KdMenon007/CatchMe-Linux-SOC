# File Investigation

## Target
`/home/socadmin/.ssh/authorized_keys`

## Existing endpoint observations
Prior-phase evidence documents:
- Line count changed from `1` to `2`.
- File permissions were `600`.
- Owner/group were `socadmin`.
- Modification time was recorded as `2026-10-03 14:40:56` IST (subsecond detail was present in the original terminal output).
- Key material must not be reproduced in public documentation.

## SIEM correlation
In the reviewed Oct 3 14:10–14:50 IST window, searches using `auditd.log.name`, `auditd.log.path`, `file.path`, and `auditd.log.record_type:PATH` did not return a matching indexed record for the target path.
A `write` syscall event around `14:40:11.939 IST` was indexed for executable `/usr/sbin/sshd`, but its target path was not present in the reviewed fields.

## Assessment
The endpoint evidence documents a file-state change. The reviewed indexed SIEM evidence does not currently provide a matching target-path record to correlate that change to a specific Auditd event.

## Do not conclude
- Do not say the endpoint never logged the file path.
- Do not treat `/var/log/audit/audit.log` as the target file; it is the source log path.
- Do not claim the syscall alone proves an authorized-keys write.
- Do not claim the key was removed or the file was restored unless fresh evidence proves it.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png`
- `../09-Screenshots/Attack/P03-ATT-05-authorized-key-verification.png`
- `../09-Screenshots/Telemetry/P03-TEL-04-authorized-key-persistence.png`
- `../09-Screenshots/Hunting/P03-HUNT-05-authorized-keys-activity.png`
- `../09-Screenshots/Detection/P03-DET-05-authorized-keys-path-no-results.png`
- `../09-Screenshots/Detection/P03-DET-06-historical-syscall-sequence.png`
