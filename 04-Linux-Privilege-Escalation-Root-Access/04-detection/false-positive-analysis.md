# False-Positive Analysis — Project 04

## Likely benign causes
- Package managers and system updates modifying executable metadata.
- Legitimate scheduled maintenance through cron or systemd timers.
- Administrators using `find`, `stat`, `getcap`, `dpkg-query`, `systemctl`, or `findmnt`.
- Monitoring agents executing scripts and collecting system information.
- Approved software installation creating SUID binaries or file capabilities.

## Reduce noise without hiding risk
1. Start with host, parent process, command line, and time context.
2. Prefer a process sequence or file-change-plus-execution correlation over a single command name.
3. Scope exceptions narrowly by executable path, parent, account, and maintenance context.
4. Review exception hits periodically.
5. Never suppress solely because an event runs as root.
6. Preserve the raw event and reason for each tuning decision.

## Task-specific note
The Task 8 cron event is a deliberate lab test. Mark it as controlled validation in the project record, not as evidence of a real compromise of an external system.
