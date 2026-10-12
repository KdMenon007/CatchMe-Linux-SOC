# YARA Analysis — Scope and Applicability

## Status
No YARA rule has been validated or deployed for this project.

## Applicability
YARA is primarily suited to identifying byte/string patterns in files or memory. The current documented scenario concerns SSH authentication and modification of an authorized-keys file. The available evidence does not provide a safe, distinctive file-content pattern suitable for a defensible YARA signature.

## Decision
Do not invent a YARA rule simply to populate the placeholder. Use Elastic KQL and event correlation for the current telemetry-focused detection, and revisit YARA only if a relevant artifact with a justified signature is collected.

## Safety and evidence
Do not include private key material, passwords, or sensitive contents of authorized-keys files in public artifacts.
