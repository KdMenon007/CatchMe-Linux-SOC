# Initial Triage — Project 04

## Triage goal
Determine whether observed Linux activity represents routine enumeration, a controlled privilege-escalation demonstration, or an actual detection alert.

## First-pass questions
1. Is the event from the expected host, `soc-linux`?
2. Which account initiated the process?
3. Is the process name, command line, and parent process available?
4. Does the event fall within the actual task execution interval?
5. Is there host-side evidence of impact?
6. Is the event an alert, ordinary telemetry, or a query result?
7. Were temporary test changes cleaned up?

## Task 8 triage result
- Cron/dash process activity was associated with the temporary validation script.
- The host-side validation log recorded root identity output.
- This supports a controlled root-context execution demonstration.
- The temporary cron entry was later found absent.
- The temporary script removal command was executed; final absence must be verified.
- Preserve the validation log.

## Tasks 9–21 triage result
The recorded activities are primarily baseline enumeration and configuration checks. No known-exploit SUID escalation, library injection, shell-feature exploit, credential exposure, NFS exploit, or kernel exploit is claimed.

## Suggested KQL: Task 8
```kql
host.name:"soc-linux" and event.category:process and
(
  process.command_line:"*p04-cron-validation.sh*" or
  process.command_line:"*P04_CRON_PRIVESC_VALIDATION*" or
  process.command_line:"*P04_ROOT_EXECUTION_PROOF*"
)
```

## Triage outcome labels
Use `confirmed controlled demonstration`, `baseline only`, `pending`, `negative query result`, or `telemetry gap`. Do not use `malicious` or `successful exploit` without supporting evidence.
