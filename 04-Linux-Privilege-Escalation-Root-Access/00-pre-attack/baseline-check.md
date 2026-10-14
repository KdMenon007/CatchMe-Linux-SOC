# Baseline Check — Project 04

## Host baseline observed
| Item | Observed value |
|---|---|
| Host | `soc-linux` |
| OS | Ubuntu 24.04.4 LTS |
| Architecture | ARM64 (`aarch64`) |
| Kernel | `6.8.0-146-generic` |
| Kernel package | `6.8.0-146.146` |
| Low-privilege test account | `p04ssh`, UID 1001 |
| Administrative account used for checks | `socadmin`, UID 1000 |
| SIEM | Elastic at `192.168.1.11` |
| Target IP | `192.168.1.16` |
| Timezone | `Asia/Kolkata` |

Values are observations from the lab session and should be rechecked if the VM changes.

## Baseline observations
- `/etc/passwd` was observed as `root:root`, mode `0644`.
- `/etc/shadow` and `/etc/gshadow` were observed as `root:shadow`, mode `0640`.
- Standard SSH configuration files were observed as root-owned, mode `0644`.
- The `p04ssh` account was not permitted to run sudo commands.
- Bash and Dash were not SUID/SGID in the checked paths.
- SUID/SGID executable inventories were collected.
- No NFS mounts or active NFS server/client targets were observed by the checks performed.
- Elastic process telemetry was observed for enumeration commands. Some syscall records lacked command-line and parent-process fields.

## Task 8 controlled demonstration
A temporary cron validation entry and script were used for a controlled root-execution demonstration. Elastic showed cron/dash execution of `/opt/catchme-p04-lab/p04-cron-validation.sh` in root context, and the validation log recorded root identity output.

The temporary cron entry was later confirmed absent. The script removal command was executed; final absence must be verified from the target. Preserve `/opt/catchme-p04-lab/validation.log`.

## Limitations
- This baseline is not a full vulnerability scan.
- Version strings alone do not establish vulnerability or patch status.
- No known-exploit SUID attack, shared-object injection, shell-feature escalation, SSH-key theft, NFS exploit, or kernel exploit was demonstrated in the recorded checks.
- The Task 9 PATH and Task 10 wildcard tasks remain baseline-only.
- Exact time ranges should be taken from terminal timestamps or Elastic event timestamps. Do not infer an exact attack time from a screenshot without a timestamp.

## Evidence to link
Add verified repository-relative paths for baseline screenshots, raw command output, and Elastic queries after those files are placed in the repository.
