# Process Investigation

## Scope
Review process-level evidence associated with SSH activity and the post-re-entry session.

## Observations
- An indexed Auditd `SYSCALL` event around `2026-10-03 14:40:11.939 IST` recorded syscall `write` (number 64), executable `/usr/sbin/sshd`, PID `4697`, and sequence `966`.
- In the reviewed event, `auditd.log.name` and `auditd.log.key` were null. The source log path `/var/log/audit/audit.log` is not the target file path.
- Sudo activity was observed before and after the public-key re-entry, but reviewed records had null `process.command_line`; exact commands are not established.

## Interpretation
These records provide process-level context but do not independently prove which target file was written or what exact privileged command was executed.

## Evidence references
- `../09-Screenshots/Detection/P03-DET-06-historical-syscall-sequence.png`
- `../09-Screenshots/Hunting/P03-HUNT-07-sudo-activity.png`
- `../09-Screenshots/Hunting/P03-HUNT-07-sudo-command-activity.png`
- `../09-Screenshots/Hunting/P03-HUNT-09-post-reentry-sudo-activity.png`

## Gaps
Do not infer process ancestry, full command lines, or successful privilege escalation unless supported by additional event fields or endpoint evidence.
