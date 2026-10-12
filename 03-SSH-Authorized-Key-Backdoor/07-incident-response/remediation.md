# Remediation Plan and Status

## Current status
No remediation is claimed as completed.

## Findings requiring disposition
1. **Authorized-key authorization:** two fingerprints are present, but the operator cannot confirm whether either is expected.
2. **Historical path correlation:** the reviewed Elastic searches did not return a matching target-path Auditd record in the selected window.
3. **Historical alert outcome:** the reviewed alert views showed no results in the selected October 3 time range; alert generation outside that scope is not established.
4. **SSH service startup:** service was active but disabled at boot at the time of the current-state check; whether this is intended is not established.
5. **Telemetry assurance:** Auditd and Elastic Agent were active at the endpoint, but service state alone does not prove complete indexing.

## Recommended work
- Resolve key authorization from an independent trusted inventory before any key change.
- Preserve the current state and maintain authorized access.
- Investigate path-level Auditd correlation and field mapping without assuming endpoint logging failed.
- Review detection execution history and selected time scope before drawing conclusions.
- Compare SSH startup configuration with the approved lab baseline.
- Validate end-to-end telemetry with controlled, authorized tests only after planning.

## Closure criteria
Close each item only when an evidence artifact proves the change or verification succeeded. Record owner, approval, timestamp, before/after state, and remaining limitations.
