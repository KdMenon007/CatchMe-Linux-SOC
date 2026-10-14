# Lessons Learned — Project 04

## Technical lessons
- A privileged scheduled task that executes a less-privileged user's writable script can create a privilege-escalation path.
- Process ancestry, command line, execution identity, and host-side evidence are stronger together than any one field alone.
- Cron PATH, wildcard, SUID/SGID, shared-object, shell-feature, credential, NFS, and kernel checks must be evaluated against the actual host configuration.
- A baseline check is not a successful exploit and should not be documented as one.

## Detection lessons
- Validate field availability and event relationships before writing detection logic.
- Record actual alert IDs only when alert documents exist.
- Tune around process context and approved administration rather than suppressing all root activity.
- Preserve evidence and record cleanup separately from detection outcomes.

## Process improvements
- Use a disposable snapshot for intentional vulnerable-state tests.
- Capture a pre-test baseline, test event, post-test state, and cleanup proof.
- Keep raw evidence separate from sanitized portfolio screenshots.
