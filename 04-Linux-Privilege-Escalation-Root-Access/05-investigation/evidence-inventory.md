# Evidence Inventory — Project 04

| Evidence item | Reported location/source | Status |
|---|---|---|
| Task 8 validation log | `/opt/catchme-p04-lab/validation.log` on `soc-linux` | Preserve; reported to contain root-context validation entries |
| Task 8 Elastic process telemetry | Elastic Discover screenshot/query results | Reported; retain original screenshot and event details |
| Task 8 review window | `2026-10-13 12:37–12:49 IST` | Recorded |
| Task 9 cron PATH baseline | Elastic screenshot, around `15:00–15:10 IST` | Baseline evidence reported |
| Task 10 cron wildcard baseline | Elastic screenshot, around `15:15–15:20:05 IST` | Baseline evidence reported |
| Task 11 package/SUID inventory | Target command output | Inventory reported; no exploit demonstrated |
| Task 12 dependency inspection | Target command output and screenshot around `15:38:45–15:39:15 IST` | Baseline evidence reported |
| Task 13 environment/capability checks | Target output and screenshot around `15:41:13–15:41:14 IST` | Baseline evidence reported |
| Task 15 capability/shell baseline | Target output and screenshot around `15:44:20–15:44:40 IST` | Baseline evidence reported |

## Evidence handling
- Preserve originals; work from copies.
- Record source host, collection time and timezone, command/query, collector, and SHA-256.
- Sanitize secrets and unrelated personal data in published copies.
- Do not replace missing artifacts with reconstructed screenshots or invented event IDs.
- A screenshot is useful context but should not replace the underlying event document when available.
