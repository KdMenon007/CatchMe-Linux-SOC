# Timeline Analysis — Project 04

All local timestamps below use `Asia/Kolkata` (IST, UTC+05:30), unless an evidence source states otherwise.

| Time/window | Activity | Evidence status |
|---|---|---|
| 2026-10-13 12:37–12:49 | Task 8 controlled cron validation | Host validation entries and Elastic process telemetry reported |
| 2026-10-13 around 15:00–15:10 | Task 9 cron PATH baseline | Normal scheduled sysstat activity and enumeration reported |
| 2026-10-13 around 15:15–15:20:05 | Task 10 cron wildcard baseline | Normal scheduled-job activity reported |
| 2026-10-13 15:38:45–15:39:15 | Task 12 dependency/metadata review | Read-only inspection events reported |
| 2026-10-13 15:41:13–15:41:14 | Task 13 environment/capability baseline | Enumeration telemetry reported |
| 2026-10-13 15:44:20–15:44:40 | Task 15 shell/capability baseline | `getcap` telemetry reported |

## Timeline rules
- Use event timestamps from source records, not screenshot capture time, when available.
- Preserve the timezone and convert to UTC only as an additional normalized column.
- Do not assign precise times to actions when only an approximate window is known.
- Add document IDs and evidence paths before using this timeline as a final incident chronology.
