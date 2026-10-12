# Attack-Chain Mapping

## Evidence-supported sequence

1. **Password guessing / credential discovery — T1110.001**
   - Evidence: `P03-ATT-01-credential-discovery.png`, `P03-TEL-01-ssh-password-attack.png`
   - Notes: supplied execution notes describe seven attempts and valid credential discovery. No password is reproduced.
2. **Remote access through SSH — T1021.004**
   - Evidence: `P03-ATT-02-ssh-initial-access.png`, `P03-TEL-02-ssh-initial-access.png`
   - Notes: initial SSH session is documented.
3. **Post-access discovery — technique-level mapping pending image-level confirmation**
   - Evidence: `P03-ATT-03-post-compromise-discovery.png`, `P03-TEL-03-post-compromise-discovery.png`
   - Notes: the project notes describe host/process/network discovery, but exact command/output evidence should be checked in the original image before finalizing individual technique IDs.
4. **SSH authorized-key persistence — T1098.004**
   - Evidence: `P03-ATT-04-authorized-key-persistence.png`, `P03-ATT-05-authorized-key-verification.png`, `P03-TEL-04-authorized-key-persistence.png`
   - Notes: file line count changed from one to two; permissions `600`, owner/group `socadmin`.
5. **Public-key re-entry over SSH — T1021.004, associated with the persistence scenario**
   - Evidence: `P03-ATT-06-key-based-reentry.png`, `P03-TEL-05-key-based-reentry.png`, `P03-HUNT-02-14-45-53-publickey-event.png`
   - Notes: successful public-key login at `2026-10-03 14:45:53.827 IST` from `192.168.1.10`.

## Correlation limits
The file-state change and subsequent public-key re-entry support the scenario. The reviewed Elasticsearch Auditd records did not expose a matching target-path field for the authorized-keys write. Do not claim complete event-by-event SIEM correlation.

## Not represented as confirmed
- Detection alert generation for the historical attack window
- Exact sudo commands and their effects
- Verified containment, key removal, credential rotation, or remediation
