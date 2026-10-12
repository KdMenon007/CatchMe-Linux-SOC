# CatchMe Linux SOC — Project 03 Cheat Sheet

Project: SSH Authorized-Key Backdoor Threat Hunt Goal: Investigate whether SSH access led to unauthorized key-based persistence, determine what telemetry is available in Elastic SIEM, and assess whether the activity can be detected and investigated.

Lab reference

Kali attacker

`192.168.1.10`

Linux endpoint

`soc-linux` · `192.168.1.16`

Elastic SIEM

`192.168.1.11`

Target account

`socadmin`

Investigation file

`~/.ssh/authorized_keys`

## 1. Investigation workflow

```

Baseline
  ↓
SSH authentication
  ↓
Post-login discovery
  ↓
authorized_keys modification
  ↓
File + process + user telemetry
  ↓
Public-key re-entry
  ↓
Elastic detection and alert review
  ↓
Timeline, evidence gaps, and response assessment

```

## 2. Linux commands

Run these on the appropriate lab system. The baseline and investigation commands below are read-only.

### A. Check the target and current user

```
hostname
ip -brief address
whoami
id
```

### B. Inspect the SSH key file

Run on `soc-linux`:

```
sudo stat /home/socadmin/.ssh/authorized_keys
sudo wc -l /home/socadmin/.ssh/authorized_keys
sudo ls -la /home/socadmin/.ssh/
```

Inspect key contents only within your authorized lab; redact public-key material before publishing evidence.

### C. Review SSH configuration

```
sudo sshd -T | grep -E '^(permitrootlogin|pubkeyauthentication|passwordauthentication|maxauthtries)'
```

### D. Check relevant services

```
systemctl is-active ssh
systemctl is-active auditd
systemctl is-active elastic-agent
sudo elastic-agent status
```

### E. Check audit health

```
sudo auditctl -s
sudo auditctl -l
```

### F. Search local authentication logs

On Debian/Kali-based systems, authentication messages may be in `/var/log/auth.log`; on other distributions, use the appropriate journal or authentication log.

```
sudo journalctl -u ssh --since "2026-10-03 14:30:00" --until "2026-10-03 15:00:00"
```

Adjust the time window to your actual investigation and local timezone.

## 3. Elastic SIEM — KQL hunting queries

Open Kibana → Discover, select the relevant data view, and set the time picker to cover your actual test or investigation window.

### Query 1 — Find authorized-key file events

```
host.name:"soc-linux" AND
data_stream.dataset:"auditd.log" AND
auditd.log.name:"/home/socadmin/.ssh/authorized_keys"
```

If this returns no events, do not conclude that the file was never modified. Check the event schema, dataset, and available fields.

### Query 2 — Search for the Auditd detection tag

```
host.name:"soc-linux" AND
data_stream.dataset:"auditd.log" AND
auditd.log.key:"ssh_authorized_keys"
```

### Query 3 — Inspect SSH authentication events

```
host.name:"soc-linux" AND
(
  system.auth.ssh.method:*
  OR event.category:authentication
)
```

Inspect returned documents for the actual username, source IP, authentication method, outcome, and timestamp. The available fields depend on your integration.

### Query 4 — Look for process execution

```
host.name:"soc-linux" AND
event.category:process
```

Look for process ancestry, executable paths, user context, and command-line information where available.

### Query 5 — Review all relevant Auditd syscall records

```
host.name:"soc-linux" AND
data_stream.dataset:"auditd.log" AND
auditd.log.record_type:"SYSCALL"
```

Use the results to inspect event fields and determine whether related `PATH` records provide file-path evidence.

### Query 6 — Existing detection rule

The existing rule is named:

`CatchMe - Linux SSH Authorized Keys Modification`

Its recorded configuration was:

- Severity: High
- Risk score: 73
- ATT&CK mapping: `T1098.004`
- Data source: Auditd

The previously documented KQL was:

```
host.name:"soc-linux" AND
data_stream.dataset:"auditd.log" AND
auditd.log.key:"ssh_authorized_keys" AND
auditd.log.record_type:"SYSCALL" AND
NOT event.action:"changed-audit-configuration"
```

Verify the current rule definition in Kibana before relying on it. A rule's configuration alone does not prove that a particular historical attack generated an alert.

## 4. Historical findings to remember

These are findings documented during your earlier Project 03 investigation, not fresh checks of the current lab.

| Item                  | Previously observed result                                                                                            |
| --------------------- | --------------------------------------------------------------------------------------------------------------------- |
| SSH re-entry          | Successful public-key authentication for `socadmin` from `192.168.1.10` on Oct 3, 2026, at approximately 14:45:53 IST |
| Key file              | Entry count increased from 1 to 2 during the historical investigation                                                 |
| Auditd                | A related syscall was observed, but the target path was not established by that event                                 |
| Detection validation  | Five High alerts were observed during a controlled file-operation test                                                |
| Historical alert      | No matching alert was found in the checked Oct 3 alert window                                                         |
| Historical conclusion | The relationship between the file modification and the historical detection outcome remained unresolved               |

Important: Two keys were later present in `authorized_keys`, including one with the comment `catchme-project03`. Their intended ownership was not established, so do not remove either key without verifying authorization.

## 5. MITRE ATT&CK quick reference

| Technique | Meaning in Project 03                                      |
| --------- | ---------------------------------------------------------- |
| T1098.004 | SSH Authorized Keys — primary hunt objective               |
| T1021.004 | SSH — remote access                                        |
| T1110.001 | Password Guessing — only if supported by observed activity |
| T1059.004 | Unix Shell — only if supported by process evidence         |

## 6. Final investigation checklist

Project 03 review

9/9

Confirm host, user, time range and data view

Search SSH authentication successes and failures

Inspect authorized_keys metadata and entry count

Inspect Auditd SYSCALL and related PATH records

Correlate process, account, source IP and timestamps

Review detection execution history and alerts

Record query results, screenshots and evidence hashes

Separate confirmed findings from unresolved gaps

Document remediation only if actually performed and validated

