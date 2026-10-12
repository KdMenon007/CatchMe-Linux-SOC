# Query Investigation Chain — SSH Authorized-Key Backdoor

## Query sequence
Run the queries in Discover with the relevant historical time range. These are documented query patterns; their outcomes below reflect the screenshots/results already supplied, not a new execution.

### 1. Confirm Auditd ingestion
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log"
```
**Observed:** 153 documents for 2026-10-03 14:35–14:47 IST.

### 2. Find syscall records
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.syscall:*
```
**Observed:** 12 documents, including `write` syscalls. This does not identify the target file.

### 3. Inspect one sequence
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.sequence:966
```
**Observed:** nine records at approximately 14:40:11.939, including `SYSCALL`, `PROCTITLE`, and `LOGIN`.

### 4. Search PATH records
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.record_type:"PATH"
```
**Observed:** no results in the historical window used during the review.

### 5. Search candidate target-path fields
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.name:"/home/socadmin/.ssh/authorized_keys"
```
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.path:"/home/socadmin/.ssh/authorized_keys"
```
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND file.path:"/home/socadmin/.ssh/authorized_keys"
```
**Observed:** no results for the historical window searched.

### 6. Correlate successful public-key re-entry
```kql
host.name:"soc-linux" AND process.name:"sshd" AND source.ip:"192.168.1.10" AND system.auth.ssh.method:"publickey" AND event.outcome:"success"
```
**Observed:** a successful public-key SSH event is visible in the supplied Discover screenshot at `2026-10-03 14:45:53.827`.

## Interpretation
The query chain confirms Auditd ingestion and supports authentication correlation. It did not find a target-path record for the authorized-key change. Keep that gap explicit.
