# Hunt Timeline — Project 04

## Timezone
All stated times use `Asia/Kolkata` (IST, UTC+05:30).

## Task 8 controlled cron demonstration
| Date | Time | Activity | Classification |
|---|---|---|---|
| 2026-10-13 | Approximately 12:37–12:48 | Temporary cron validation script executed in root context; host log recorded root identity | Controlled attack evidence |
| 2026-10-13 | 12:37–12:49 review window | Elastic process events reviewed for cron/dash and validation script activity | Correlation window |
| 2026-10-13 | Later in session | Temporary cron entry found absent | Cleanup evidence |
| 2026-10-13 | Later in session | Script reference search across selected cron/systemd locations returned no paths | Limited cleanup check |
| 2026-10-13 | Later in session | Script-removal command executed | Final absence requires verification |

## Later baseline hunts
SUID/SGID, shared-library, environment, shell, history/configuration, SSH-key, NFS, kernel, and tool-discovery checks occurred later in the session. Their exact timestamps must be taken from their individual Elastic screenshots or terminal records.

## Interpretation
This timeline distinguishes the known controlled demonstration from subsequent baseline activity. The review interval does not imply that every event occurred exactly at the start or end of that interval. Do not manufacture timestamps for commands whose time was not recorded.
