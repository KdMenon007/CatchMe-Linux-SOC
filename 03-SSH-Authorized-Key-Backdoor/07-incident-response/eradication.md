# Eradication

## Status
**Not performed / not verified.**

The two public-key fingerprints currently present in `authorized_keys` have not been classified as expected or unauthorized. Therefore, removing either entry would be speculative and is not authorized by the current evidence.

## Evidence-backed facts
- Two public-key entries were parsed by `ssh-keygen`.
- A third line is blank.
- File mode is `600`; owner/group are `socadmin`.
- Historical endpoint evidence documented a prior authorized-keys file-state change and subsequent public-key re-entry.

## Required before eradication
1. Identify approved key fingerprints from a trusted source.
2. Determine which key, if any, is unauthorized.
3. Preserve the original file and its metadata.
4. Obtain explicit approval for a targeted change.
5. Remove only the confirmed unauthorized entry and retain known-good access.
6. Validate access, ownership, permissions, and monitoring after the change.

## Completion criteria
Eradication can be marked complete only when the unauthorized key is identified and removed, approved access still works, and post-change evidence is recorded. None of those criteria are established here.
