# Threat Hunting Methodologies

---

## Overview

Threat hunting is a **proactive cybersecurity discipline** in which security analysts actively search for hidden threats within an organization's environment — before any alert is triggered. Unlike traditional security monitoring that depends on automated detection rules or SIEM alerts, threat hunting is driven by analyst intuition, structured frameworks, and threat intelligence.

The core philosophy is simple: **assume breach**. Threat hunters operate under the assumption that an attacker may already be inside the network, moving laterally or exfiltrating data without triggering any alarms. Their job is to find those threats before damage is done.

> **Key Objective:** Detect advanced, stealthy threats early by combining hypothesis-driven investigation, behavioral analysis, and multi-source data correlation.

---

## 1. Proactive Threat Hunting vs. Reactive Incident Response

Understanding the difference between proactive hunting and reactive response is foundational.

| Aspect | Proactive Threat Hunting | Reactive Incident Response |
|---|---|---|
| **Trigger** | Analyst hypothesis or threat intel | Alert or reported incident |
| **Timing** | Before a confirmed threat | After detection or escalation |
| **Goal** | Discover unknown threats | Contain and remediate known threats |
| **Approach** | Exploratory and iterative | Structured and process-driven |
| **Outcome** | New detections, tuned rules | Resolved incident, lessons learned |

### Hypothesis-Driven Hunting

Proactive hunting begins with a **hypothesis** — an educated assumption about how an attacker might behave in a given environment. Hypotheses are built from:

- **Threat Intelligence** – Knowledge of active threat actors and their TTPs (Tactics, Techniques, and Procedures)
- **MITRE ATT&CK Framework** – A globally accessible knowledge base of adversary behavior
- **Historical Incidents** – Patterns observed during previous breaches or near-misses
- **Environmental Baselines** – Understanding what "normal" looks like in the organization

### Practical Example: Hunting for T1078 – Valid Accounts Misuse

**MITRE ATT&CK Technique T1078** describes attackers using stolen or legitimately obtained credentials to gain unauthorized access — a common technique used in APT campaigns and ransomware attacks.

A hypothesis might be:
> *"An attacker has obtained valid credentials and is using them to log into systems at unusual hours from unfamiliar locations."*

An analyst would then search for:

- **Unusual login times** – Authentication events occurring outside business hours (e.g., 2:00 AM logins)
- **Logins from unknown IP addresses** – Particularly from foreign countries or anonymizing proxies (Tor, VPNs)
- **Privilege escalation events** – A standard user account suddenly accessing admin-level resources
- **Abnormal lateral movement** – Accounts accessing systems they've never touched before
- **Multiple failed logins followed by success** – Indicative of credential stuffing or brute-force attacks

This type of hunt surfaces threats that automated tools may completely miss because the credentials used are legitimate.

---

## 2. Threat Hunting Frameworks

Frameworks give threat hunters a structured, repeatable process — ensuring hunts are systematic rather than ad hoc.

---

### 2.1 SqRR Framework

**SqRR** stands for:

```
Search → Query → Retrieve → Respond
```

This is a practical, step-by-step workflow for executing a threat hunt from start to finish.

#### Step 1 — Search
- Identify **suspicious activity, anomalies, or indicators** that warrant investigation
- Inputs may come from threat intelligence feeds, EDR alerts, network anomalies, or analyst hunches
- Define the scope: What systems? What time range? What user accounts?

#### Step 2 — Query
- Use **SIEM platforms** (e.g., Splunk, Microsoft Sentinel, Elastic SIEM) to build targeted queries
- Write searches against logs to surface relevant events
- Example Splunk query to hunt for T1078:
  ```spl
  index=auth_logs sourcetype=windows_security EventCode=4624
  | stats count by src_ip, user, logon_type
  | where count > 5 AND logon_type=3
  | sort -count
  ```
- Refine queries iteratively to reduce noise and increase signal

#### Step 3 — Retrieve
- **Collect and preserve evidence** from endpoints, network devices, and cloud logs
- Pull raw log files, memory dumps, or packet captures as needed
- Correlate findings across multiple data sources to build a complete picture
- Document all retrieved artifacts for chain-of-custody purposes

#### Step 4 — Respond
- If a threat is confirmed, **initiate containment actions** — isolate the affected system, disable compromised accounts, block malicious IPs
- Escalate to the incident response team with documented findings
- Feed new detection logic back into SIEM rules and EDR policies to improve future coverage
- Write a hunt report documenting the hypothesis, methodology, findings, and response

---

### 2.2 TaHiTI Framework

**TaHiTI** stands for:

```
Targeted Hunting integrating Threat Intelligence
```

Developed to operationalize the use of external threat intelligence within the hunting process, TaHiTI bridges the gap between intelligence teams and SOC analysts.

#### Core Principles of TaHiTI

- **Intelligence-Led Hunting:** Hunts are not random — they are directed by credible, current threat intelligence
- **Targeting:** Focus hunting effort on the most likely threats to the organization's specific industry, region, and technology stack
- **Integration:** Threat intelligence is not consumed passively; it is actively mapped to internal telemetry

#### TaHiTI Process Steps

1. **Identify Threat Intelligence Indicators**
   - Gather IOCs (Indicators of Compromise) such as malicious IPs, domains, file hashes, and behavioral signatures
   - Sources: AlienVault OTX, MISP, FS-ISAC, government CERTs, commercial intel platforms

2. **Map to Internal Environment**
   - Determine which TTPs from the intelligence are relevant to your environment
   - Ask: *"Does our organization use the software being exploited? Are we in the targeted industry?"*

3. **Investigate Systems for Indicators**
   - Search endpoint and network logs for matches against the identified IOCs
   - Use EDR platforms to scan for known malware hashes or suspicious behavioral patterns

4. **Analyze Attacker Tactics and Techniques**
   - Go beyond IOC matching — understand *how* the attacker operates
   - Map discovered activity to MITRE ATT&CK to understand the full kill chain

5. **Take Response Actions**
   - Contain confirmed threats and remediate affected systems
   - Update threat intelligence platforms with newly discovered indicators
   - Share findings with the broader community (ISACs, MISP) to improve collective defense

---

## 3. Data Sources for Threat Hunting

Effective threat hunting requires visibility across multiple data layers. A hunter with limited data sources will have significant blind spots.

---

### 3.1 Endpoint Logs (EDR)

Endpoint Detection and Response (EDR) platforms — such as CrowdStrike Falcon, Microsoft Defender for Endpoint, and SentinelOne — provide granular telemetry from individual devices.

Key data points include:

- **Process execution trees** – Identify parent-child process relationships that reveal malicious execution (e.g., `Word.exe` spawning `PowerShell.exe`)
- **File creation and modification** – Detect suspicious files dropped by malware or attackers
- **Registry changes** – Monitor persistence mechanisms like Run keys or scheduled tasks
- **Command-line arguments** – Review full command lines for encoded payloads or LOLBins (Living-off-the-Land Binaries)
- **Network connections initiated by processes** – Correlate process behavior with outbound network activity

**Example Hunt:** Search for PowerShell processes with encoded command-line arguments (`-EncodedCommand`), which is commonly used to obfuscate malicious scripts.

---

### 3.2 Network Traffic Logs

Network-level visibility is essential for detecting lateral movement, C2 communication, and data exfiltration.

Sources include:
- **Firewall logs** – Inbound/outbound connection attempts and blocks
- **DNS logs** – Requests to suspicious or newly registered domains; DNS tunneling activity
- **Proxy logs** – HTTP/HTTPS traffic from internal hosts to external destinations
- **NetFlow/IPFIX data** – Summary traffic flows for anomaly detection without full packet capture
- **Full packet capture (PCAP)** – Deep inspection of traffic content for confirmed threats

**Example Hunt:** Identify internal hosts making DNS queries to domains with high entropy names (e.g., `a8f3k2z.randomdomain.ru`) — a common indicator of domain generation algorithm (DGA) malware.

---

### 3.3 Threat Intelligence Feeds

Threat intelligence provides external context that enables hunters to connect internal observations to known attacker infrastructure and techniques.

| Platform | Type | Key Use |
|---|---|---|
| **AlienVault OTX** | Open Source | IOC sharing: IPs, domains, hashes |
| **VirusTotal** | Open Source | File/URL reputation analysis |
| **MISP** | Open Source | Structured IOC sharing and correlation |
| **Recorded Future** | Commercial | Predictive intelligence and dark web monitoring |
| **Mandiant Advantage** | Commercial | APT actor profiles and campaign tracking |
| **MITRE ATT&CK** | Open Framework | TTP mapping and adversary behavior |

**Best Practice:** Don't just consume IOCs — enrich them with context. Knowing that an IP address is "malicious" is far less useful than knowing it is associated with APT29's C2 infrastructure targeting government organizations.

---

### 3.4 Cloud and Identity Logs

As organizations migrate to cloud environments, hunting must extend to:

- **Azure AD / Entra ID logs** – Sign-in logs, conditional access events, MFA bypass attempts
- **AWS CloudTrail** – API calls, IAM changes, S3 access patterns
- **Microsoft 365 Unified Audit Log** – Email access, file sharing, admin activity
- **Google Workspace Audit Logs** – Drive activity, login events, admin changes

**Example Hunt:** Search for service accounts in Azure AD authenticating from IP addresses outside the organization's known infrastructure — a potential sign of compromised cloud credentials.

---

## 4. Case Study: APT29 (Cozy Bear)

**APT29**, also known as **Cozy Bear**, is a sophisticated nation-state threat actor attributed to Russian intelligence services (SVR). The group has conducted high-profile espionage campaigns against government agencies, think tanks, and technology companies.

### Known TTPs (Mapped to MITRE ATT&CK)

| Technique ID | Technique Name | Description |
|---|---|---|
| T1078 | Valid Accounts | Use of stolen or compromised credentials for initial access |
| T1566.002 | Spearphishing Link | Targeted phishing emails with malicious links |
| T1071.001 | Web Protocols (C2) | Command and control over HTTP/HTTPS |
| T1027 | Obfuscated Files | Use of encoded scripts and payloads to evade detection |
| T1041 | Exfiltration over C2 | Data exfiltration through established C2 channels |
| T1003 | OS Credential Dumping | Extracting credentials from LSASS memory |

### Hunting for APT29-Like Behavior

Based on the above TTPs, a hunter investigating potential APT29 activity might:

1. **Search for T1566.002** – Review email gateway logs for spearphishing links clicked by employees; correlate with subsequent suspicious process execution
2. **Hunt for T1078** – Identify accounts logging in from unusual geolocations or at abnormal times, particularly accounts with elevated privileges
3. **Investigate T1071.001** – Look for processes making outbound HTTPS connections to low-reputation domains, especially with unusual user-agent strings
4. **Detect T1003** – Search EDR logs for access to `lsass.exe` by non-system processes, or use of tools like Mimikatz

Studying APT29's methodology helps analysts build detection logic that is relevant to real-world, sophisticated adversaries — not just generic malware.

---

## 5. Building a Threat Hunting Program

### Maturity Levels

| Level | Description |
|---|---|
| **Level 0 – Initial** | Relies entirely on automated alerts; no proactive hunting |
| **Level 1 – Minimal** | Occasional ad hoc hunting based on IOCs from intel feeds |
| **Level 2 – Procedural** | Regular hunts using established frameworks (SqRR, TaHiTI) |
| **Level 3 – Innovative** | Hypothesis-driven hunts; custom analytics; threat modeling |
| **Level 4 – Leading** | Automated hunt pipelines; continuous hunting; intel production |

### Recommended Learning Resources

- **SANS Threat Hunting Papers** – In-depth coverage of SqRR, TaHiTI, and operational hunting techniques
- **MITRE ATT&CK Navigator** – Interactive tool for mapping and visualizing adversary TTPs
- **Elastic Threat Hunting Guide** – Practical, hands-on guidance for hunting with Elastic SIEM
- **Sqrll Threat Hunting Reference Guide** – One of the foundational documents in the field
- **TaHiTI Documentation (NCSC-NL)** – Official methodology documentation from the Dutch National Cyber Security Centre
- **APT29 MITRE Profile** – Detailed breakdown of Cozy Bear's known techniques and tools

---

## Conclusion

Threat hunting is one of the most impactful capabilities a SOC team can develop. By combining **hypothesis-driven investigation**, **structured frameworks like SqRR and TaHiTI**, and **rich, multi-source telemetry**, analysts can surface sophisticated threats that evade automated detection entirely.

The most effective hunters operate with an attacker's mindset — always asking: *"If I were inside this network, how would I move, hide, and persist?"* Answering that question proactively is what separates threat hunting from simply waiting for alerts.

> **Next Steps:** Begin with a single hypothesis tied to a high-priority ATT&CK technique relevant to your environment, run your first structured hunt using SqRR, and iterate from there.
