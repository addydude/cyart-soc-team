# Threat Hunting Report — MITRE ATT&CK T1078

**Date:** 2025-08-18  
**Analyst:** SMOUGERR  
**Technique:** T1078 — Valid Accounts  
**Tools Used:** Elastic Security · AlienVault OTX · Velociraptor  

---

## Report

A threat hunt was conducted targeting MITRE ATT&CK T1078 (Valid Accounts — privilege escalation). The hypothesis stated that unauthorized privilege assignments were occurring in domain accounts. Elastic Security was queried for Event ID 4672, identifying user **testuser** receiving unexpected admin privileges on 2025-08-18. AlienVault OTX was used to search T1078-related IOCs; IP 205.210.31.161 returned 50 pulses, tagged as a honeypot sensor (cowrie, suricata) — assessed as a false positive. Velociraptor endpoint queries (SELECT * FROM processes) confirmed testuser self-elevated via `net localgroup administrators` with a follow-on encoded PowerShell payload. Hypothesis validated. Recommendation: isolate WS-CORP-042, reset credentials for testuser and jsmith, and enforce MFA on all privileged logins.

---

## MITRE ATT&CK Techniques Confirmed

| Technique ID | Name | Tactic |
|---|---|---|
| T1078 | Valid Accounts | Privilege Escalation |
| T1059 | Command and Scripting Interpreter | Execution |
| T1548 | Abuse Elevation Control Mechanism | Privilege Escalation |

---

## Recommendations

| Priority | Action |
|---|---|
| Critical | Isolate host WS-CORP-042 immediately |
| Critical | Reset credentials for testuser and jsmith |
| High | Enforce MFA on all privileged logins |
| High | Create Elastic detection rule for Event ID 4672 on non-admin accounts |
| Medium | Continue monitoring svc_backup account on SRV-FILE-01 |
