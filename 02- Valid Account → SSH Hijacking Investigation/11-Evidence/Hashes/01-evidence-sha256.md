# Project 02 — Evidence SHA-256 Hash Manifest

## Purpose

This manifest records the SHA-256 checksums of the finalized evidence Markdown files contained in:

`11-Evidence/Raw/`

`11-Evidence/Sanitized/`

The hashes were generated from the GitHub repository clone using the Linux `sha256sum` utility.

## Hash Scope

- Raw evidence files: 7
- Sanitized evidence files: 5
- Total hashed evidence files: 12
- Hash algorithm: SHA-256
- Hash manifest itself is excluded from the checksum list.

## Raw Evidence

| File | SHA-256 |
|---|---|
| `Raw/01-linux-ssh-authentication-evidence.md` | `c33188cb65000560e27d03b45ff652a7b50a2f74cdc104828396b26f9303830d` |
| `Raw/02-elastic-ssh-authentication-evidence.md` | `637f03c5bf401eecda3f98aa4b6592724d1168d526bb419b9b40d3ad3f317672` |
| `Raw/03-detection-alert-evidence.md` | `e958a2d6b6ef62d9a5db566003fca75dbb77a1e5030bc31e26c349fc9f3b9908` |
| `Raw/04-investigation-evidence.md` | `053f511c258301ebc158fd7ed5c48724212c6878d8459a1b988ec26eb92bdba5` |
| `Raw/05-containment-remediation-evidence.md` | `5b6ee075288d548fc60004db8caaac789efc5fa29f4b7ae31b52382c807a8ec0` |
| `Raw/06-final-remediation-state.md` | `db135687e5f9596aff3b5e78272a7a4029f206a8c769f501d5f945e012f258ac` |
| `Raw/07-attack-evidence-summary.md` | `8670e190487ecc4a0e12d8c5ab4b612e9468ca9c1ee2e7619dbab3e3ceb55823` |

## Sanitized Evidence

| File | SHA-256 |
|---|---|
| `Sanitized/01-sanitized-attack-timeline.md` | `64f1fb562a964d03d06918b9ba515f52681e039a0db0a2948833cf894c22785d` |
| `Sanitized/02-sanitized-detection-evidence.md` | `40e0962fdecc982f14435cba2a60cae172c4be4f130035416d2213abd91b5574` |
| `Sanitized/03-sanitized-investigation-evidence.md` | `98bf3a498d2c41a3debbca7aa292125da5105a4e11294c39d4ef0a77a08aad0e` |
| `Sanitized/04-sanitized-remediation-evidence.md` | `aba16ffa5a348f0801689926fc96f48904b52bef4dbdeb9fc6c8a821c296d074` |
| `Sanitized/05-sanitized-evidence-index.md` | `c4077451f127ca1a46adb85a250713dffa06534d3541c06f6e6a38bd84e17d7c` |

## Verification Command

The evidence hashes can be independently verified from a local clone of the repository with:

```bash
find "02- Valid Account → SSH Hijacking Investigation/11-Evidence" -type f ! -path '*/Hashes/*' -exec sha256sum {} \; | sort
```

## Integrity Notes

* These checksums represent the files as they existed in the GitHub repository clone at the time of collection.
* Any modification to a hashed evidence file will produce a different SHA-256 value.
* The hash manifest is intentionally excluded from its own checksum set.
* No secrets, passwords, private keys, or other sensitive authentication material are included in this manifest.


