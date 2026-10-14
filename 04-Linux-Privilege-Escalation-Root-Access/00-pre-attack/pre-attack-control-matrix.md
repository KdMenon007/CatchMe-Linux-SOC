# Pre-Attack Control Matrix — Project 04

| Area | Pre-check | Guardrail | Evidence needed | Current status |
|---|---|---|---|---|
| Scope | Confirm `soc-linux` and lab IPs | No testing outside authorized lab | Host/IP output | Recorded |
| Account access | Confirm `p04ssh` and `socadmin` context | Do not assume sudo access from group membership alone | `id`, sudo result, auth telemetry | Recorded |
| Cron permissions | Inspect cron configuration and file ownership | No new system cron entries on protected VM | Permissions, cron process telemetry, validation log | Task 8 demo observed; temporary entry absent |
| Cron PATH | Inspect existing PATH configuration | Do not add PATH-hijack job to protected VM | Configuration and telemetry | Baseline only |
| Cron wildcards | Inspect existing schedules and scripts | Do not plant wildcard payloads | Configuration and telemetry | Baseline only |
| SUID/SGID | Inventory files and verify package context | Do not change SUID/SGID bits or run unverified exploits | Inventory, package versions, telemetry | Baseline checks complete |
| Shared libraries | Inspect dependencies and directory permissions | No library injection | `ldd`, loader configuration, permissions | Checks performed; no injection |
| Environment variables | Inspect environment and executable permissions | Do not manipulate environment for privileged exploitation | Environment and process telemetry | Baseline only |
| Shell features | Inspect shell permissions, versions, capabilities | No privileged shell escape attempt | `stat`, package version, `getcap` | Baseline only |
| History/config/SSH | Check metadata and permissions only | Do not print or copy secrets | File metadata and query results | Baseline checks complete |
| NFS | Inspect mounts, services, exports, tools | Do not mount untrusted shares | `findmnt`, service status, config metadata | No local NFS setup observed |
| Kernel | Record version and mitigation interface | No kernel exploit | Kernel details and advisory review | Baseline only |
| Assessment tools | Locate tools without running exploit scripts | No automated exploit execution on protected VM | Tool discovery output | Checks complete |
| Cleanup | Verify temporary cron entry and script state | Preserve validation log and existing evidence | Final `ls` output, evidence hashes | Script removal command executed; verify final state |
| SIEM | Correlate exact process events | Do not equate process telemetry with an alert | KQL, time range, fields, screenshots | Telemetry observed; alert status must be evidenced separately |

## Decision gates
1. If a check requires a deliberate misconfiguration, stop on the protected VM and use a disposable environment.
2. If an event lacks enough fields to establish its identity or impact, record the limitation.
3. If an attack outcome is not proven, mark it as not demonstrated or pending.
4. Before publication, verify cleanup and evidence references.
