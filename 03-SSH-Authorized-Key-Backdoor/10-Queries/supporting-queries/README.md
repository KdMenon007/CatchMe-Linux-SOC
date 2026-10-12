# Supporting Query Notes

These queries are supporting investigation patterns. Run them only in the authorized lab and set the intended historical time window in Discover.

## Auditd ingestion baseline
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log"
```

## Candidate target-path field checks
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND auditd.log.path:"/home/socadmin/.ssh/authorized_keys"
```
```kql
host.name:"soc-linux" AND data_stream.dataset:"auditd.log" AND file.path:"/home/socadmin/.ssh/authorized_keys"
```

## Guardrail
No results in one field/time range do not prove an event never existed. A generic `write` syscall must not be attributed to `authorized_keys` without reliable path-level evidence.
