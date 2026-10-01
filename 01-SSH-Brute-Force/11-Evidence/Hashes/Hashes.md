# Evidence Hashes

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

## Purpose

This directory stores cryptographic hashes for the project evidence artifacts.

Hashing provides an integrity reference so that collected evidence can later be checked for modification.

---

## Evidence Files

The following raw evidence artifacts are included in the project:

| Evidence File | Type | Integrity Reference |
|---|---|---|
| `raw/hydra-attack-output.md` | Attack evidence | SHA-256 |
| `raw/linux-ssh-journal.md` | Linux SSH journal evidence | SHA-256 |
| `raw/elastic-authentication-events.md` | Elastic authentication evidence | SHA-256 |
| `raw/detection-alert.md` | Detection alert evidence | SHA-256 |
| `raw/investigation-results.md` | Investigation evidence | SHA-256 |

---

## Hash Generation

Generate SHA-256 hashes from the project root:

```bash
sha256sum evidence/raw/hydra-attack-output.md
sha256sum evidence/raw/linux-ssh-journal.md
sha256sum evidence/raw/elastic-authentication-events.md
sha256sum evidence/raw/detection-alert.md
sha256sum evidence/raw/investigation-results.md
```

---

## Hash Manifest

After generating the hashes, store the actual output below.

```text
PASTE ACTUAL SHA-256 HASH OUTPUT HERE
```

Example format:

```text
<sha256-hash>  evidence/raw/hydra-attack-output.md
<sha256-hash>  evidence/raw/linux-ssh-journal.md
<sha256-hash>  evidence/raw/elastic-authentication-events.md
<sha256-hash>  evidence/raw/detection-alert.md
<sha256-hash>  evidence/raw/investigation-results.md
```

---

## Integrity Verification

Hashes can be verified later with:

```bash
sha256sum -c hashes.sha256
```

A successful verification should report:

```text
evidence/raw/<file>: OK
```

---

## Evidence Integrity Procedure

1. Collect the evidence.
2. Save the evidence artifact.
3. Generate its SHA-256 hash.
4. Store the hash in the hash manifest.
5. Preserve the original evidence.
6. Recalculate the hash whenever integrity verification is required.
7. Compare the newly generated hash with the recorded hash.

---

## Important

The SHA-256 values must be generated from the **actual files present in the repository**.

Do not manually invent or document hash values before running the hashing command.

---

## Status

**Hashing Procedure:** Defined

**Actual Hash Values:** Pending local generation

**Evidence Integrity:** Must be verified against the generated SHA-256 values


