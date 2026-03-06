# 2. Incident Classification

## Overview

Incident classification is the process of categorising a security event by **type**, **severity**, and **relevant metadata** so that SOC teams can route, prioritise, and investigate it effectively. Consistent classification enables faster triage, accurate reporting, and trend analysis.

---

## Core Concepts

### 2.1 Incident Categories

| Category | Description | Example |
|----------|-------------|---------|
| **Malware** | Malicious software infecting systems | Ransomware encrypting files, trojan exfiltrating credentials |
| **Phishing** | Deceptive communications to steal credentials or deploy malware | Email with malicious attachment or credential-harvesting link |
| **DDoS** | Distributed Denial of Service attack overwhelming resources | Volumetric attack flooding a web server |
| **Insider Threat** | Malicious or negligent action by an internal user | Employee exporting sensitive data before resignation |
| **Data Exfiltration** | Unauthorised transfer of data outside the organisation | FTP upload of a database dump to an external server |
| **Unauthorised Access** | Access to systems without proper authorisation | Brute-force attack gaining entry to an admin account |
| **Web Application Attack** | Exploitation of vulnerabilities in web applications | SQL injection, XSS, RFI/LFI attacks |
| **Supply Chain Attack** | Compromise via a trusted third-party vendor or software | SolarWinds-style Orion platform backdoor |

---

### 2.2 Classification Frameworks and Taxonomies

#### 2.2.1 MITRE ATT&CK

The [MITRE ATT&CK Framework](https://attack.mitre.org/) is a knowledge base of adversary **tactics** and **techniques** based on real-world observations.

**Structure:** Tactics → Techniques → Sub-techniques

| MITRE Tactic | ID | Example Technique |
|--------------|----|-------------------|
| Initial Access | TA0001 | T1566 – Phishing |
| Execution | TA0002 | T1059 – Command and Scripting Interpreter |
| Persistence | TA0003 | T1547 – Boot or Logon Autostart Execution |
| Privilege Escalation | TA0004 | T1068 – Exploitation for Privilege Escalation |
| Defence Evasion | TA0005 | T1027 – Obfuscated Files or Information |
| Credential Access | TA0006 | T1110 – Brute Force |
| Discovery | TA0007 | T1046 – Network Service Scanning |
| Lateral Movement | TA0008 | T1021 – Remote Services |
| Collection | TA0009 | T1005 – Data from Local System |
| Exfiltration | TA0010 | T1048 – Exfiltration Over Alternative Protocol |
| Impact | TA0040 | T1486 – Data Encrypted for Impact (Ransomware) |

**Usage Example – Phishing Incident:**
```
Incident Type : Phishing Email
MITRE Tactic  : Initial Access (TA0001)
MITRE Technique: T1566 – Phishing
Sub-technique : T1566.001 – Spearphishing Attachment
```

#### 2.2.2 ENISA Incident Taxonomy

The [ENISA Incident Taxonomy](https://www.enisa.europa.eu/topics/incident-response) provides a standardised classification for EU member states and organisations:

| Category | Sub-categories |
|----------|---------------|
| Abusive Content | Spam, Harassment, Child Sexual Abuse Material |
| Malicious Code | Virus, Worm, Trojan, Ransomware, Spyware |
| Information Gathering | Scanning, Sniffing, Social Engineering |
| Intrusion Attempts | Exploitation of Known Vulnerability, Login Attempts |
| Intrusions | Privileged Account Compromise, Unauthorised Access |
| Availability | DDoS, Sabotage, Outages |
| Information Content Security | Unauthorised Access to Information, Data Breach |
| Fraud | Phishing, Identity Theft |

#### 2.2.3 VERIS (Vocabulary for Event Recording and Incident Sharing)

[VERIS](http://veriscommunity.net/) provides a common language for describing security incidents. It uses an **A4 Threat Model**:

| Element | Description |
|---------|-------------|
| **Actor** | Who is responsible? (External, Internal, Partner) |
| **Action** | What did they do? (Hacking, Malware, Social, Misuse, Physical, Error, Environmental) |
| **Asset** | What was affected? (Server, User Device, Network, Data, Application) |
| **Attribute** | What was compromised? (Confidentiality, Integrity, Availability) |

**Example VERIS Classification – Phishing + Data Breach:**
```
Actor     : External – organised criminal group
Action    : Social (phishing) + Hacking (credential use)
Asset     : Data (customer PII), User Devices
Attribute : Confidentiality (data disclosure)
```

---

### 2.3 Contextual Metadata

Enriching incidents with metadata speeds up investigation and supports correlation across events.

#### Essential Metadata Fields

| Field | Description | Example |
|-------|-------------|---------|
| `incident_id` | Unique identifier | INC-2025-0042 |
| `timestamp` | Date/time of detection | 2025-08-18T14:00:00Z |
| `source_ip` | Originating IP address | 203.0.113.55 |
| `destination_ip` | Target IP address | 10.0.1.25 |
| `affected_systems` | Hostnames or asset IDs | web-server-01, db-server-02 |
| `user_accounts` | Involved usernames | jdoe@company.com |
| `ioc_hashes` | Malicious file hashes | `d41d8cd98f00b204e9800998ecf8427e` (MD5) |
| `ioc_domains` | Malicious domains | `evil.example.com` |
| `ioc_ips` | Malicious IP addresses | `45.33.32.156` |
| `mitre_technique` | MITRE ATT&CK technique ID | T1566.001 |
| `severity` | Priority level | Critical / High / Medium / Low |
| `status` | Current state | Open / In Progress / Closed |

#### Sample Enriched Incident Record

```
Incident ID   : INC-2025-0042
Type          : Phishing
MITRE Tactic  : TA0001 – Initial Access
MITRE Tech.   : T1566.001 – Spearphishing Attachment
Severity      : High
Status        : In Progress
Detected At   : 2025-08-18T14:00:00Z
Source IP     : 203.0.113.55 (External – flagged on OTX)
Destination   : jdoe@company.com
Affected Asset: User workstation WKSTN-042
IOC Hash      : e3b0c44298fc1c149afbf4c8996fb924 (email attachment)
IOC Domain    : malicious-phishing-site.com (VirusTotal: 35/70 engines)
Description   : User received email with ZIP attachment containing
                Emotet loader. Link redirects to credential harvesting page.
```

---

## Key Objectives

- Classify incidents using standardised taxonomies (MITRE ATT&CK, ENISA, VERIS).
- Enrich incident records with contextual metadata to accelerate investigation.
- Map observed behaviours to specific MITRE techniques for tracking adversary TTPs.



### Classification Workflow

```
1. Receive alert or report
2. Identify the incident type (Malware? Phishing? DDoS? etc.)
3. Map to MITRE ATT&CK technique(s)
4. Apply ENISA / VERIS category labels
5. Gather contextual metadata (IPs, hashes, affected systems)
6. Enrich with threat intelligence (VirusTotal, OTX)
7. Record in incident management platform (TheHive, JIRA, etc.)
8. Set priority level (see Alert Priority Levels doc)
9. Assign to appropriate analyst / team
```

### Common Classification Mistakes to Avoid

- Labelling all unknown events as "malware" – investigate first.
- Missing lateral movement after an initial compromise – always check for pivoting.
- Not updating the MITRE technique if the investigation reveals additional TTPs.
- Forgetting to document IOCs – these are critical for threat hunting and blocking.