# Query Investigation Chain — Project 04

## Purpose
Document the pivots used to move from a broad host query to a focused process chain. Query examples are starting points and must be adjusted to the actual Elastic data view and populated fields.

## Chain A — Task 8 controlled cron test
### Pivot 1: Host and event category
```kql
host.name:"soc-linux" and event.category:process
```

### Pivot 2: Validation script and proof markers
```kql
host.name:"soc-linux" and event.category:process and
(
  process.command_line:"*p04-cron-validation.sh*" or
  process.command_line:"*P04_CRON_PRIVESC_VALIDATION*" or
  process.command_line:"*P04_ROOT_EXECUTION_PROOF*"
)
```

### Pivot 3: Cron and shell process chain
```kql
host.name:"soc-linux" and event.category:process and
(
  process.name:"cron" or process.name:"dash" or
  process.name:"sh" or process.name:"id"
)
```

Inspect timestamps, user context, command line, and parent process when those fields are available. A broad process-name query can return unrelated scheduled jobs, so narrow by the actual event time and host evidence.

## Chain B — Baseline enumeration
```kql
host.name:"soc-linux" and event.category:process and
(
  process.name:"find" or process.name:"stat" or
  process.name:"getcap" or process.name:"dpkg-query" or
  process.name:"findmnt" or process.name:"systemctl"
)
```

This chain documents reconnaissance commands and should be classified as baseline telemetry.

## Required query notes
For each query, record:
- Data view/index pattern.
- Exact KQL.
- Absolute start/end time and timezone.
- Result count and representative event timestamps.
- Fields present and absent.
- What the query supports and what it cannot establish.
- Screenshot and raw evidence paths.

## Query limitations
- Missing command-line fields may require process-name/time pivots.
- Empty results may reflect time range, data view, mapping, or ingestion.
- Process telemetry is not itself an alert.
- Query results do not replace host-side proof of privilege impact.
