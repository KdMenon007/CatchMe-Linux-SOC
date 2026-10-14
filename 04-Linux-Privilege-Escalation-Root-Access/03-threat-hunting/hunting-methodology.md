# Hunting Methodology — Project 04

## Method
1. Define a testable hypothesis.
2. Establish target, account, OS, and time context.
3. Identify expected process, authentication, file, or scheduled-task telemetry.
4. Query Elastic with a scoped KQL filter.
5. Pivot using host, user, process, parent, command line, and timestamp when available.
6. Correlate SIEM events with host-side logs or command output.
7. Classify the result and record limitations.
8. Verify cleanup for controlled tests and preserve evidence.

## Result classes
- **Attack evidence:** supports an actual controlled action and outcome.
- **Baseline telemetry:** records enumeration or configuration checks.
- **Detection alert:** an actual alert record with rule and alert metadata.
- **Negative result:** no matching documents for a specified query and interval.
- **Telemetry gap:** expected source or fields are unavailable.
- **Pending:** test requires a disposable VM or evidence is incomplete.

## Time handling
Use `Asia/Kolkata` (IST, UTC+05:30). Select an absolute interval that includes the event timestamps. Task 8 was reviewed over approximately 12:37–12:49 IST on 2026-10-13. Do not reuse that interval for later baseline commands.

If terminal output lacks timestamps, recover them from terminal history or Elastic records. If they cannot be recovered, state that the exact timestamp is unavailable.

## Correlation priorities
- Host: `soc-linux`
- Account context: `p04ssh` or `socadmin`, as applicable
- Process chain: cron, shell, script, child process
- Command line and parent process, when present
- Host-side validation output
- Cleanup state and evidence integrity

## Documentation standard
For each hunt, preserve the hypothesis, query, data view, time range, result count, representative events, interpretation, evidence paths, confidence, and next step.
