# Raw Evidence

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

# 1. Purpose

The `raw/` directory contains the original evidence collected directly from the Project 01 lab environment.

These files represent the evidence in its original form before sanitization or modification.

---

# 2. Evidence Sources

Raw evidence may include artifacts collected from:

- Kali attacker
- Linux endpoint
- Elastic SIEM
- SSH authentication logs
- Detection alerts
- Investigation queries
- Command output

---

# 3. Project 01 Evidence

The raw evidence supports the following investigation stages:

```text
Attack Execution
      ↓
SSH Authentication Activity
      ↓
Linux Endpoint Telemetry
      ↓
Elastic SIEM Events
      ↓
Detection Alert
      ↓
Investigation
```

---

# 4. Evidence Preservation

Original evidence should not be edited after collection.

The recommended workflow is:

```text
Evidence Collection
        ↓
Raw Evidence
        ↓
Hash / Integrity Verification
        ↓
Sanitized Copy
        ↓
GitHub Publication
```

---

# 5. Security Considerations

Do not store sensitive information in the raw evidence that is intended for public GitHub publication.

Potential sensitive information includes:

* Passwords
* API keys
* Authentication tokens
* Private keys
* Real credentials
* Unnecessary personal information

Raw evidence should remain separate from the sanitized evidence prepared for public documentation.

---
