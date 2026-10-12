# Investigation Findings

## Confirmed observations
1. Existing screenshots document SSH password-attack activity and an initial SSH session involving the lab scenario.
2. Endpoint evidence records an authorized-keys file-state change: line count `1` to `2`, mode `600`, owner/group `socadmin`, and modification time around `2026-10-03 14:40:56 IST`.
3. A successful public-key SSH re-entry was observed at `2026-10-03 14:45:53.827 IST` from `192.168.1.10` for `socadmin` on `soc-linux`.
4. Auditd syscall telemetry was indexed during the historical window, including a `write` syscall attributed to `/usr/sbin/sshd`.
5. Searches for the target authorized-keys path did not find a matching record in the selected indexed fields/time window.
6. The reviewed Open, Acknowledged, and Closed alert views showed no results for the selected Oct 3 window. This does not establish that no alert existed outside the selected scope.

## Inferences (label as such)
- The observed file-state change followed by successful public-key re-entry is consistent with the SSH authorized-key persistence scenario under investigation.
- The reviewed indexed data does not provide complete path-level correlation for the file change.

## Unresolved
- Historical alert generation for the attack window.
- Whether a target-path Auditd `PATH` record exists outside the searched fields, index scope, or time window.
- Exact commands and effects associated with observed sudo events.
- Verified removal of the key, credential rotation, containment, and post-remediation validation.

## Root-cause statement
**Root cause is not fully established.** The scenario includes documented authorized-keys file modification and subsequent public-key re-entry, but the reviewed evidence does not establish every causal step or a complete detection pipeline outcome.

## Incident response status
No claim of completed containment or remediation is made in these documents. Record those actions only after verifying endpoint state and preserving the corresponding evidence.
