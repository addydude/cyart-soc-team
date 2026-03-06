# 1. Alert Priority Levels

## Overview

Alert priority levels define the **severity and urgency** of a security incident. Assigning the correct priority enables SOC analysts to focus limited resources on the most dangerous events first, ensuring timely containment and minimal business impact.

---

## Core Concepts

### 1.1 Priority Definitions

| Priority | Description | Example Scenario |
|----------|-------------|-----------------|
| **Critical** | Immediate threat with potential for catastrophic damage; active exploitation confirmed or imminent | Ransomware actively encrypting files on a production server |
| **High** | Significant threat requiring prompt action; may indicate a breach or serious vulnerability exploitation | Unauthorised admin access to a domain controller |
| **Medium** | Moderate risk; suspicious activity that warrants investigation but is not immediately destructive | Repeated failed login attempts from an external IP |
| **Low** | Minimal risk; informational or routine events that need monitoring | Port scan from an internal test host |

**Key dimensions used when defining priority:**

- **Impact** – How badly would this affect data, services, or reputation? (e.g., data breach vs. minor service disruption)
- **Urgency** – How quickly must this be addressed? (e.g., active exploitation vs. theoretical vulnerability)

---

### 1.2 Assignment Criteria

When assigning a priority level, consider:

1. **Asset Criticality** – Is the affected system a production server, domain controller, or a test VM?
   - Production database → higher priority
   - Isolated test VM → lower priority

2. **Exploit Likelihood** – Is there a known, publicly available exploit for the vulnerability?
   - Known CVE with public exploit code → higher priority
   - Theoretical vulnerability, no known exploit → lower priority

3. **Business Impact** – What is the potential financial, legal, or reputational damage?
   - PII/financial data at risk → higher priority
   - Internal documentation at risk → lower priority

**Practical Example – Log4Shell (CVE-2021-44228):**

```
CVSS Base Score : 10.0
Exploitability  : Remote, no authentication required
Impact          : Full system compromise (RCE)
Asset           : Internet-facing application servers
Assignment      : CRITICAL – immediate patching and monitoring required
```

---

### 1.3 CVSS Scoring System

The **Common Vulnerability Scoring System (CVSS)** provides a standardised numerical score (0.0–10.0) to quantify the severity of a vulnerability.

#### CVSS Score Ranges → Priority Mapping

| CVSS Score | Severity | SOC Priority |
|------------|----------|--------------|
| 9.0 – 10.0 | Critical | Critical |
| 7.0 – 8.9  | High     | High     |
| 4.0 – 6.9  | Medium   | Medium   |
| 0.1 – 3.9  | Low      | Low      |
| 0.0        | None     | —        |

#### CVSS Metric Groups

```
Base Score (inherent characteristics)
├── Attack Vector (AV)    : Network / Adjacent / Local / Physical
├── Attack Complexity (AC): Low / High
├── Privileges Required   : None / Low / High
├── User Interaction      : None / Required
├── Scope                 : Unchanged / Changed
├── Confidentiality Impact: None / Low / High
├── Integrity Impact      : None / Low / High
└── Availability Impact   : None / Low / High

Temporal Score (current exploit state)
├── Exploit Code Maturity
├── Remediation Level
└── Report Confidence

Environmental Score (organisation-specific context)
├── Modified Base Metrics
└── Asset Importance / Criticality
```

---

### 1.4 SOC Tool-Based Risk Scoring

Beyond CVSS, enterprise SOC tools provide additional risk scoring capabilities:

- **Splunk ES Risk Framework** – Assigns risk scores to assets and users based on correlated alert activity. Useful for detecting slow-moving threats (e.g., insider threats) that individually score low but accumulate over time.
- **Wazuh Rule Levels** – Uses levels 0–15; levels 12+ are considered high severity and can trigger automated responses.
- **TheHive Severity Field** – Low / Medium / High / Critical labels on case objects for workflow routing.

---

## Key Objectives

- Accurately assess and prioritise alerts using CVSS scores and asset context.
- Apply standardised criteria (impact + urgency) to assign priority levels efficiently.
- Leverage SOC tooling (Splunk, Wazuh, TheHive) to automate and visualise priority distribution.

---


### Real-World Example: Log4Shell (CVE-2021-44228)

```
CVE          : CVE-2021-44228
CVSS Score   : 10.0 (Critical)
Affected SW  : Apache Log4j 2.x
Vector       : CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H
Attack Type  : Remote Code Execution via JNDI lookup in log messages
Priority     : CRITICAL
SOC Action   : Immediate – patch, monitor, isolate exposed systems
```

**Why Critical?**
- Attack Vector: **Network** (exploitable remotely)
- Attack Complexity: **Low** (trivial to exploit)
- Privileges Required: **None** (no authentication needed)
- Impact: **Full** Confidentiality, Integrity, and Availability compromise

### Quick Priority Decision Framework

```
Is the system actively being compromised?
  YES → CRITICAL

Is there confirmed unauthorised access or data exfiltration?
  YES → CRITICAL / HIGH depending on scope

Is the vulnerability publicly exploited with no patch applied?
  YES → HIGH (at minimum)

Is this suspicious activity that needs investigation?
  YES → MEDIUM

Is this informational or background noise?
  YES → LOW
```