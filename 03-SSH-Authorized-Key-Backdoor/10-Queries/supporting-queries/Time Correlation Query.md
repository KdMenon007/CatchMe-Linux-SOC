# Time Correlation Query

**Purpose:** Review Project 03 events within a bounded investigation window.

## Candidate KQL

```kql
host.name:"soc-linux" AND @timestamp >= now-30m
```

Replace the relative window with explicit absolute start/end values when documenting a historical investigation. Ensure the Kibana time picker and query use a consistent timezone.

## Correlation rules
- Keep original timestamps and timezone offsets.
- Note ingestion delay separately from event time.
- Correlate only on identifiers actually present in the documents.
- Capture the exact time range and query with the result.

The relative query is a convenience for current triage and is not suitable as the sole record of a historical result.
