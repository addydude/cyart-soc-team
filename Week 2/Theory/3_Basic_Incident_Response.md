# 3. Basic Incident Response

## Overview

Incident Response (IR) is the **structured methodology** for handling the aftermath of a security breach or cyberattack. The goal is to manage the incident in a way that limits damage, reduces recovery time and costs, and prevents future incidents.

The industry-standard lifecycle is defined in **NIST SP 800-61 Rev 2**.

---

## Core Concepts

### 3.1 The Incident Response Lifecycle (NIST SP 800-61)

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   1. Preparation → 2. Identification → 3. Containment      │
│                                              ↓              │
│   6. Lessons Learned ← 5. Recovery ← 4. Eradication        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### Phase 1 – Preparation

**Goal:** Establish the capability to respond before an incident occurs.

Key activities:
- Develop and maintain **Incident Response Playbooks** (e.g., Phishing Playbook, Ransomware Playbook)
- Configure detection tools (SIEM, IDS/IPS, EDR)
- Define roles and responsibilities (Tier 1 / Tier 2 / Tier 3 / CISO)
- Establish communication protocols (internal escalation, law enforcement, legal, PR)
- Train SOC analysts through tabletop exercises and simulations
- Maintain up-to-date asset inventory and network diagrams

**Deliverables:** IR Policy, Playbooks, Contact Lists, Communication Templates

---

#### Phase 2 – Identification (Detection & Analysis)

**Goal:** Detect and confirm that an incident has occurred.

Key activities:
- Monitor SIEM alerts, EDR detections, and IDS/IPS signatures
- Perform **alert triage** to distinguish real incidents from false positives
- Gather initial evidence (logs, network captures, endpoint data)
- Assign a unique Incident ID and document in the case management system
- Classify the incident (type, severity, MITRE technique)
- Escalate to the appropriate tier if needed

**Deliverables:** Initial Incident Report, Priority Assignment, Case Ticket

**Indicators of Compromise (IOCs) to collect:**
- Malicious IP addresses / domains
- File hashes (MD5, SHA-1, SHA-256)
- Registry keys / persistence mechanisms
- Unusual process names or parent-child relationships
- Suspicious network connections or data transfers

---

#### Phase 3 – Containment

**Goal:** Limit the spread and impact of the incident.

**Short-term containment (immediate):**
- Isolate the affected system(s) from the network (VLAN change, firewall rule, or physical disconnect)
- Disable compromised user accounts
- Block malicious IPs/domains at the firewall or DNS

**Long-term containment (stabilisation):**
- Apply temporary patches or workarounds
- Implement enhanced monitoring on adjacent systems
- Preserve forensic evidence **before** making changes

> ⚠️ **Evidence First:** Always take forensic images and memory dumps **before** isolating or cleaning a system to preserve volatile data.

**Deliverables:** Containment Actions Log, Network Isolation Confirmation

---

#### Phase 4 – Eradication

**Goal:** Remove the threat from the environment completely.

Key activities:
- Remove malware, backdoors, and persistence mechanisms
- Patch the exploited vulnerability
- Reset compromised credentials
- Verify no other systems are infected (lateral movement check)
- Reimage systems if thorough malware removal cannot be guaranteed

**Deliverables:** Eradication Confirmation Report, Patch Record

---

#### Phase 5 – Recovery

**Goal:** Restore affected systems to normal operation safely.

Key activities:
- Restore from verified clean backups
- Bring systems back online in a phased manner
- Monitor intensively for 24–72 hours post-recovery
- Validate system integrity before returning to production
- Communicate restoration to stakeholders

**Deliverables:** Recovery Plan, System Restoration Log, Stakeholder Notification

---

#### Phase 6 – Lessons Learned (Post-Incident Activity)

**Goal:** Improve processes based on what happened.

Key activities:
- Conduct a **post-mortem** / **After Action Review (AAR)** within 2 weeks of incident closure
- Document: what happened, how it was detected, response timeline, what worked, what didn't
- Update playbooks, detection rules, and training materials
- Track action items and assign owners with deadlines

**Deliverables:** Post-Mortem Report, Updated Playbooks, Action Items Tracker

---

### 3.2 Key Procedures

#### System Isolation Procedure

```
1. Confirm the system is implicated (cross-reference alert + logs)
2. Notify the system owner and your team lead
3. Take forensic image / memory dump (BEFORE isolation)
4. Disconnect from network:
   - Virtual: Change VM network adapter to "host-only" or disable NIC
   - Physical: Unplug network cable / disable switch port via management console
5. Document: hostname, IP, MAC, time of isolation
6. Continue monitoring isolated host for attacker activity
```

#### Evidence Preservation Procedure

```
1. Document current state (screenshots, notes)
2. Collect volatile data first (RAM, active connections, running processes)
   - Tool: Velociraptor, WinPMem, LiME (Linux)
3. Acquire disk image
   - Tool: FTK Imager, dd, Autopsy
4. Hash all evidence files immediately after collection (SHA-256)
5. Record in Chain of Custody form:
   | Item | Collected By | Date/Time | Location Stored | Hash |
6. Store evidence in a secure, read-only location
7. Never perform analysis on original evidence – work on copies
```

#### Communication Protocol

| Audience | Timing | Channel | Content |
|----------|--------|---------|---------|
| Team Lead | Immediately | Slack / Phone | Alert details, initial assessment |
| Tier 2 Analyst | Per escalation criteria | Ticket + Slack | Full incident summary, IOCs |
| Management / CISO | High/Critical incidents | Email + Call | Business impact, containment status |
| Legal / Compliance | Data breach suspicion | Email | Regulatory notification requirements |
| Law Enforcement | Criminal activity | Formal process | Evidence, incident timeline |

---

### 3.3 SOAR Tools and Automation

**SOAR (Security Orchestration, Automation, and Response)** tools automate repetitive IR tasks.

| Tool | Use Case |
|------|---------|
| **Splunk SOAR (Phantom)** | Playbook automation – auto-enrich IOCs, auto-isolate hosts |
| **TheHive + Cortex** | Case management + automated analyser tasks (VirusTotal, etc.) |
| **Palo Alto XSOAR** | Enterprise SOAR with hundreds of integrations |
| **Shuffle** | Open-source SOAR for smaller SOCs |

**Example SOAR Automation – Phishing Alert:**
```
Trigger: New phishing alert in SIEM
    ↓
Auto-extract: Sender IP, URLs, attachment hashes
    ↓
Enrich: Query VirusTotal, OTX for IOCs
    ↓
Decision: Malicious IOC found?
  YES → Auto-block sender domain, quarantine email, open case in TheHive
  NO  → Mark as potential FP, assign to analyst for manual review
```

---

## Key Objectives

- Understand and execute each phase of the NIST IR lifecycle.
- Correctly perform system isolation and evidence preservation without destroying forensic data.
- Follow communication protocols to ensure the right stakeholders are informed at the right time.
- Leverage SOAR tools to automate repetitive triage and response tasks.

---

