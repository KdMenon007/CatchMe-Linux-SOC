# Evidence Hashes

This folder documents integrity checks for project evidence.

## Screenshot manifest
The screenshot SHA-256 manifest is maintained at:

- `../Hashes/screenshot-sha256-manifest.md` (if present in this project version)

Run verification from the project root with the manifest's recorded file paths and the corresponding `sha256sum -c` input file. If the manifest uses a different path or format, follow its instructions rather than assuming a command.

## Interpretation limits
- A matching SHA-256 value shows that a file matches the recorded reference hash.
- It does not independently prove when, where, or how the file was collected, nor that its interpretation is correct.
- Never replace a recorded hash to conceal a changed file. Record new versions and their provenance separately.

Do not add fabricated hashes. Confirm the actual manifest filename and location before running a verification.
