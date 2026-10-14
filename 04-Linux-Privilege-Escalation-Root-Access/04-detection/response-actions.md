# Detection Response Actions — Project 04

## If suspicious cron-mediated execution is observed
1. Preserve the event and alert documents, process tree, command line, host identity, and relevant timestamps.
2. Determine whether the job and script are authorized.
3. Inspect cron configuration and script ownership/mode without changing them before evidence collection.
4. Compare the observed state with the approved baseline.
5. If unauthorized execution is credible, follow the lab's containment procedure and preserve volatile evidence where feasible.
6. Remove only known lab artifacts after recording their state.
7. Verify the final state and document each cleanup command.
8. Review the detection logic for coverage and false positives.

## Do not
- Delete the evidence log to make cleanup appear complete.
- Claim a detection alert from ordinary process telemetry.
- Alter system cron, SUID bits, sudoers, SSH configuration, or kernel settings on the protected VM to force a test.
- Treat the absence of a query result as proof that an activity did not occur.

## Closure fields
Incident/test ID, analyst, host, UTC/IST timestamps, evidence paths, classification, actions, cleanup verification, detection status, limitations, and follow-up owner.
