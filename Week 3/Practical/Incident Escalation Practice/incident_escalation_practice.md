# Task 3: Incident Escalation Practice

## Objective
Simulate SOC escalation, draft SITREP communication, and demonstrate escalation workflow.

## 1. Escalation Simulation Summary (Tier 2)

A high-priority unauthorized access incident was reviewed in TheHive. Initial triage identified suspicious authentication activity tied to source IP 192.168.1.200 and mapped behavior to MITRE ATT&CK T1078 (Valid Accounts). The impacted host (Server-Y) was isolated to limit spread and preserve integrity for investigation. Due to potential credential misuse and risk of continued unauthorized access, the incident was escalated from Tier 1 to Tier 2 for deeper analysis, credential exposure assessment, and containment validation. Investigation ownership was assigned to Tier 2 analysts for correlation, timeline reconstruction, and remediation recommendations.

## 2. SITREP Details

- Title: Unauthorized Access on Server-Y
- Detected: 2025-08-18 13:00
- Source IP: 192.168.1.200
- MITRE Technique: T1078
- Actions Taken: Isolated server, escalated to Tier 2

## 3. Workflow Automation Note

A simple high-severity auto-assignment workflow concept was prepared for Splunk Phantom playbook logic:
- If severity is High or Critical, route case to Tier 2 queue.
- Attach initial enrichment and notify on-call analyst.

## Artifacts

- `Tier2_escalation_ss.png`
- `SITREP – Unauthorized Access on Server-Y.pdf`
