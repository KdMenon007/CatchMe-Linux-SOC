# Evidence Hashes

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

## Purpose

This document contains SHA-256 integrity hashes for the raw evidence artifacts collected for Project 01.

The hashes were generated from the actual files in the repository after cloning the GitHub repository locally.

## SHA-256 Hash Manifest

| Evidence File | SHA-256 |
|---|---|
| `detection-alert.md` | `38295fa6eec846e6d15bb350054f0b027338f2965b8658772484466ac38214a0` |
| `elastic-authentication-events.md` | `e8677158078c196cf1ee8cbdd28dfb1f81f13a02a8fb647f7e1f0e3812d8a92b` |
| `hydra-attack-output.md` | `f5fd4411339a6182e62cfeecfd33725f9b54aa23768d0b859878876421a78746` |
| `Investigation Results.md` | `03210058b75b4b9f092fe24411756108b495f9c0386a942e3fedde42d0428d0b` |
| `linux-ssh-journal.md` | `7b79871f9c2d3dc5189c5a644c5d0c51a545636827b06895e9c840b89af23d3c` |

## Repository Paths

```text
11-Evidence/
├── Raw/
│   ├── detection-alert.md
│   ├── elastic-authentication-events.md
│   ├── hydra-attack-output.md
│   ├── Investigation Results.md
│   └── linux-ssh-journal.md
└── Hashes/
    └── Hashes.md
```

## Verification Command

From the Project 01 repository root:

```bash
sha256sum 11-Evidence/Raw/*.md
```

The generated SHA-256 values should match the values recorded in this document.

## Integrity Status

**Hash Algorithm:** SHA-256

**Evidence Files Hashed:** 5

**Hash Generation:** Completed

**Integrity Reference:** Established

**Evidence Status:** Verified


