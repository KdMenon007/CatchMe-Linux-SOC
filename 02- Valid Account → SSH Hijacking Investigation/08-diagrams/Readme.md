# Project 02 — Diagram Documentation

## Valid Account → SSH Hijacking

This directory contains the final visual documentation for the CatchMe Linux SOC Project 02.

The diagrams cover the complete security operations lifecycle from lab architecture and attack execution through telemetry, threat hunting, detection engineering, investigation, evidence correlation, containment, remediation, hardening, forensic analysis, and lessons learned.

---

## Diagram Index

### 01 — Lab Architecture

**File:** `01-Lab-Architecture.png`

Visual representation of the Project 02 laboratory environment, including the Kali attacker, Linux endpoint, Elastic SIEM infrastructure, network connectivity, and telemetry flow.

**Purpose:**
- Document the project infrastructure.
- Identify attacker, victim, and SIEM components.
- Establish the network relationships used during the investigation.

---

### 02 — Attack Flow

**File:** `02-Attack-Flow.png`

Visualizes the controlled Valid Account → SSH Hijacking attack scenario.

**Purpose:**
- Show attacker-to-endpoint access.
- Illustrate valid-account SSH authentication.
- Represent controlled post-authentication activity.
- Establish the attack sequence used for investigation.

---

### 03 — Telemetry Pipeline

**File:** `03-Telemetry-Pipeline.png`

Shows how Linux endpoint telemetry moves through Elastic Agent/Fleet into Elastic SIEM.

**Purpose:**
- Document the telemetry pipeline.
- Connect endpoint activity with SIEM visibility.
- Show where authentication and process telemetry are collected.

---

### 04 — Detection Workflow

**File:** `04-Detection-Workflow.png`

Illustrates the SOC detection lifecycle from suspicious SSH activity to alert generation and analyst investigation.

**Purpose:**
- Explain detection engineering.
- Show KQL-based detection processing.
- Visualize alert generation and analyst workflow.

---

### 05 — Investigation Timeline & Log Correlation

**File:** `05-Investigation-Timeline-Log-Correlation.png`

Maps the chronological investigation process against relevant Linux and Elastic telemetry.

**Purpose:**
- Correlate authentication events.
- Establish a reliable attack timeline.
- Connect timestamps with evidence sources.
- Separate attack activity from validation activity.

---

### 06 — SSH Session Sequence Flow

**File:** `06-SSH-Session-Sequence-Flow.png`

Visualizes the SSH authentication and session lifecycle.

**Purpose:**
- Show SSH connection establishment.
- Represent authentication.
- Show session creation and termination.
- Explain the relationship between SSH and endpoint telemetry.

---

### 07 — Attack Timeline & Evidence Correlation

**File:** `07-Attack-Timeline-Evidence-Correlation.png`

Connects attack stages with corresponding evidence collected during the investigation.

**Purpose:**
- Correlate events across multiple sources.
- Connect attack actions to evidence.
- Support evidence-based incident reconstruction.

---

### 08 — Detection Rule Logic & Flow

**File:** `08-Detection-Rule-Logic-Flow.png`

Documents the logic behind the Project 02 SSH activity detection.

**Purpose:**
- Visualize detection conditions.
- Show the relationship between SSH telemetry and detection logic.
- Explain how suspicious SSH activity reaches the alerting stage.

---

### 09 — Telemetry Source-to-Event Mapping

**File:** `09-Telemetry-Source-to-Event-Mapping.png`

Maps telemetry sources to the events and fields used during investigation.

**Purpose:**
- Connect Linux log sources with Elastic events.
- Identify authentication, session, and process telemetry.
- Support threat-hunting and detection development.

---

### 10 — Threat Hunting & KQL Investigation Flow

**File:** `10-Threat-Hunting-KQL-Investigation-Flow.png`

Visualizes the threat-hunting workflow used to investigate valid-account SSH activity.

**Purpose:**
- Show hypothesis-driven hunting.
- Represent KQL query development.
- Correlate user, source IP, host, process, and authentication activity.
- Support repeatable SOC investigations.

---

### 11 — MITRE ATT&CK Mapping

**File:** `11-MITRE-ATTCK-Mapping.png`

Maps the Project 02 scenario to relevant MITRE ATT&CK techniques.

**Purpose:**
- Connect observed behavior with ATT&CK techniques.
- Provide structured adversary-behavior classification.
- Support threat detection and reporting.

---

### 12 — SSH Attack Evidence Correlation Map

**File:** `12-SSH-Attack-Evidence-Correlation-Map.png`

Provides a visual relationship between the SSH attack, endpoint evidence, SIEM telemetry, and investigation findings.

**Purpose:**
- Correlate attack indicators.
- Connect evidence sources.
- Support investigation conclusions.

---

### 13 — Detection Engineering & Rule Validation

**File:** `13-Detection-Engineering-Rule-Validation.png`

Documents the detection-engineering lifecycle used to create and validate the Project 02 detection rule.

**Purpose:**
- Represent detection creation.
- Show KQL validation.
- Connect rule execution with alert generation.
- Document detection validation.

---

### 14 — Alert-to-Incident Response Escalation

**File:** `14-Alert-to-Incident-Response-Escalation.png`

Shows the transition from Elastic SIEM alert generation into SOC investigation and incident response.

**Purpose:**
- Visualize alert triage.
- Show investigation escalation.
- Connect detection with response actions.

---

### 15 — Containment & Remediation Workflow

**File:** `15-Containment-Remediation-Workflow.png`

Documents the containment and remediation workflow performed after the investigation.

**Purpose:**
- Show containment decisions.
- Document SSH security remediation.
- Represent validation after remediation.
- Connect remediation with final-state verification.

---

### 16 — SSH Hardening Before & After

**File:** `16-SSH-Hardening-Before-After.png`

Provides a visual comparison of relevant SSH security configuration before and after remediation.

**Purpose:**
- Show password-authentication hardening.
- Show SSH feature hardening.
- Document firewall enforcement.
- Demonstrate preservation of authorized key-based access.

---

### 17 — Evidence Collection & Chain of Correlation

**File:** `17-Evidence-Collection-Chain-of-Correlation.png`

Visualizes how evidence is collected, correlated, validated, and used during the investigation.

**Purpose:**
- Show evidence sources.
- Connect raw telemetry with investigation findings.
- Support reproducible incident documentation.
- Maintain evidence integrity throughout analysis.

---

### 18 — Incident Investigation & Forensics Workflow

**File:** `18-Incident-Investigation-Forensics-Workflow.png`

Provides the detailed forensic investigation workflow from alert analysis through evidence review and root-cause analysis.

**Purpose:**
- Represent forensic investigation stages.
- Show artifact analysis.
- Correlate evidence and timelines.
- Support root-cause identification.

---

### 19 — Investigation Workflow Dashboard

**File:** `19-Investigation-Workflow-Dashboard.png`

Provides a consolidated visual representation of the Project 02 investigation workflow and its major investigation components.

**Purpose:**
- Present the investigation process in a dashboard-style view.
- Connect investigation stages.
- Provide a portfolio-friendly visual summary.

---

### 20 — Lessons Learned & Improvements

**File:** `20-Lessons-Learned-Improvements.png`

Summarizes lessons learned, challenges, improvements, skills developed, operational maturity, and future enhancements from Project 02.

**Purpose:**
- Document project lessons.
- Identify detection and response improvements.
- Highlight practical SOC skills developed.
- Connect the laboratory exercise with real-world SOC operations.

---

## Coverage Summary

The 20 diagrams collectively cover:

| Security Area | Covered |
|---|---|
| Lab Architecture | Yes |
| Attack Simulation | Yes |
| SSH Authentication | Yes |
| SSH Session Lifecycle | Yes |
| Telemetry Pipeline | Yes |
| Telemetry Source Mapping | Yes |
| Threat Hunting | Yes |
| KQL Investigation | Yes |
| Detection Engineering | Yes |
| Detection Rule Logic | Yes |
| Alert Generation | Yes |
| Investigation | Yes |
| Timeline Analysis | Yes |
| Evidence Correlation | Yes |
| Forensic Workflow | Yes |
| MITRE ATT&CK | Yes |
| Incident Response | Yes |
| Containment | Yes |
| Remediation | Yes |
| SSH Hardening | Yes |
| Lessons Learned | Yes |

---

## Final Diagram Structure

```text
08-diagrams/
├── 01-Lab-Architecture.png
├── 02-Attack-Flow.png
├── 03-Telemetry-Pipeline.png
├── 04-Detection-Workflow.png
├── 05-Investigation-Timeline-Log-Correlation.png
├── 06-SSH-Session-Sequence-Flow.png
├── 07-Attack-Timeline-Evidence-Correlation.png
├── 08-Detection-Rule-Logic-Flow.png
├── 09-Telemetry-Source-to-Event-Mapping.png
├── 10-Threat-Hunting-KQL-Investigation-Flow.png
├── 11-MITRE-ATTCK-Mapping.png
├── 12-SSH-Attack-Evidence-Correlation-Map.png
├── 13-Detection-Engineering-Rule-Validation.png
├── 14-Alert-to-Incident-Response-Escalation.png
├── 15-Containment-Remediation-Workflow.png
├── 16-SSH-Hardening-Before-After.png
├── 17-Evidence-Collection-Chain-of-Correlation.png
├── 18-Incident-Investigation-Forensics-Workflow.png
├── 19-Investigation-Workflow-Dashboard.png
├── 20-Lessons-Learned-Improvements.png
└── Readme.md
```
