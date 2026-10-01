# Sanitized Evidence

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

# 1. Purpose

The `sanitized/` directory contains evidence prepared for safe inclusion in the public GitHub portfolio.

Sanitized evidence is derived from the original evidence stored in:

```text
evidence/raw/
```

The original evidence should remain unchanged.

---

# 2. Sanitization Objective

Before publishing evidence publicly, review it for information that should not be exposed outside the lab.

The objective is to preserve the investigation value while removing unnecessary sensitive information.

---

# 3. Evidence That May Require Sanitization

Review evidence for:

* Passwords
* API keys
* Authentication tokens
* Private keys
* Real credentials
* Personal information
* Unnecessary internal information
* Sensitive configuration details

Project 01 lab addresses such as the controlled lab IP addresses may be retained when they are required to understand the documented lab architecture.

---

# 4. Sanitization Workflow

```text
Raw Evidence
     ↓
Review
     ↓
Identify Sensitive Information
     ↓
Create Sanitized Copy
     ↓
Remove / Mask Sensitive Data
     ↓
Verify Investigation Context
     ↓
Publish Sanitized Evidence
```

---

# 5. Original Evidence Protection

The raw evidence must not be modified during sanitization.

```text
evidence/raw/
        │
        │ copy
        ▼
evidence/sanitized/
```

This preserves the original artifact while allowing a separate portfolio-safe version to be created.

---

# 6. Project 01 Evidence

Sanitized evidence may include portfolio-safe versions of:

* SSH authentication evidence
* Linux SSH journal evidence
* Elastic authentication events
* Detection alerts
* Investigation evidence
* Attack execution evidence

The sanitized evidence should continue to demonstrate:

```text
Attack
  ↓
Telemetry
  ↓
Detection
  ↓
Investigation
```

---

# 7. Quality Check

Before publishing a sanitized artifact, verify:

```text
Sensitive information removed
        ↓
Investigation context preserved
        ↓
Evidence remains readable
        ↓
No credentials exposed
        ↓
Artifact suitable for GitHub
```

