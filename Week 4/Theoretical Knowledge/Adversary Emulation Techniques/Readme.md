# Adversary Emulation Techniques

---

## Overview

Most organizations discover their security gaps one of two ways: during a real attack, or during a controlled simulation. **Adversary emulation** is the practice of choosing the second option deliberately.

Adversary emulation goes beyond traditional penetration testing. Where a pentest asks *"what vulnerabilities exist?"*, adversary emulation asks *"if a specific, real-world threat actor targeted us today — using their known playbook — would we detect them? Would we respond in time? Would our controls hold?"*

The answers are grounded in **threat intelligence**. Emulation scenarios are not invented — they are reconstructed from actual attacker behavior, mapped through frameworks like **MITRE ATT&CK**, and replayed in a controlled environment against real defensive tooling.

> **Key Distinction:**
>
> | Practice | Focus | Based On |
> |---|---|---|
> | Vulnerability Assessment | Find weaknesses | Scanning and enumeration |
> | Penetration Testing | Exploit weaknesses | Opportunistic attack paths |
> | **Adversary Emulation** | Replicate a specific threat actor | Real-world TTPs from threat intelligence |
> | Red Teaming | Full-scope covert attack | Goal-based, intelligence-driven |

Done well, adversary emulation transforms the SOC from reactive to **validated** — able to demonstrate, with evidence, that it can detect and respond to the threats most likely to target the organization.

---

## 1. Adversary Emulation — Core Concepts

### 1.1 What Are TTPs?

**TTPs — Tactics, Techniques, and Procedures** — are the building blocks of how attackers operate. They are the *what*, *how*, and *why* of adversary behavior:

- **Tactics** – The high-level goal (e.g., Initial Access, Lateral Movement, Exfiltration)
- **Techniques** – The specific method used to achieve the tactic (e.g., Phishing, Pass-the-Hash)
- **Procedures** – The exact implementation used by a specific threat actor (e.g., APT28's specific spearphishing lure templates and macro delivery mechanism)

The **MITRE ATT&CK framework** catalogs TTPs across 14 tactic categories and hundreds of techniques, each backed by real-world threat intelligence. This makes it the foundational reference for adversary emulation planning.

---

### 1.2 Key Techniques Commonly Emulated

#### T1566 – Phishing (Initial Access)

Phishing remains one of the most common initial access vectors used by both nation-state and financially motivated threat actors.

**Sub-techniques:**

| Sub-technique | Description | Example |
|---|---|---|
| **T1566.001** | Spearphishing Attachment | Malicious Word/Excel macro delivered via targeted email |
| **T1566.002** | Spearphishing Link | Link to credential harvesting page or drive-by download |
| **T1566.003** | Spearphishing via Service | Malicious link delivered through LinkedIn, Teams, or Slack |

**Emulation Scenario — T1566.001:**
```
1. Craft a convincing spearphishing email targeting a finance employee
2. Attach a macro-enabled Excel file mimicking an invoice
3. User opens attachment and enables macros
4. Macro executes a PowerShell command to download a payload
5. Payload establishes C2 communication

Detection Validation Checks:
  ✓ Did email gateway flag the attachment?
  ✓ Did EDR detect macro execution?
  ✓ Did SIEM alert on PowerShell download cradle?
  ✓ Did network tools flag the C2 beacon?
```

---

#### T1210 – Exploitation of Remote Services (Lateral Movement)

Once inside a network, attackers pivot to other systems by exploiting vulnerabilities in internal services.

**Common targets:**
- SMB (EternalBlue / MS17-010)
- RDP (BlueKeep / CVE-2019-0708)
- Exchange (ProxyLogon / CVE-2021-26855)
- Apache Log4j (Log4Shell / CVE-2021-44228)
- VPN appliances (Pulse Secure, Fortinet)

**Emulation Scenario — Internal SMB Exploitation:**
```
1. Attacker has foothold on Workstation A (via phishing)
2. Attacker scans internal network for SMB-exposed hosts
3. Attacker identifies unpatched server (MS17-010)
4. Attacker exploits vulnerability → gains SYSTEM on Server B
5. Attacker establishes persistence via scheduled task

Detection Validation Checks:
  ✓ Did NDR/IDS detect internal port scan?
  ✓ Did EDR flag exploit activity on Server B?
  ✓ Did SIEM correlate east-west movement between hosts?
  ✓ Did alerts fire for new scheduled task creation?
```

---

#### Additional High-Value Techniques to Emulate

| Technique ID | Name | Why Emulate |
|---|---|---|
| **T1078** | Valid Accounts | Tests identity monitoring and anomalous login detection |
| **T1059.001** | PowerShell | Tests script execution logging and behavioral detection |
| **T1003.001** | LSASS Memory Dump | Tests credential theft detection via EDR |
| **T1071.001** | C2 over HTTP/HTTPS | Tests network-layer C2 detection |
| **T1486** | Data Encrypted for Impact (Ransomware) | Tests ransomware behavior detection and response |
| **T1041** | Exfiltration over C2 | Tests DLP and outbound data monitoring |
| **T1053.005** | Scheduled Task (Persistence) | Tests persistence mechanism detection |
| **T1055** | Process Injection | Tests in-memory execution detection by EDR |

---

### 1.3 Planning an Emulation Engagement

A structured emulation engagement follows these phases:

```
[PHASE 1] THREAT INTELLIGENCE GATHERING
    ↓  Select target adversary profile (e.g., APT28, Lazarus Group)
    ↓  Map known TTPs to MITRE ATT&CK
    ↓  Identify techniques most relevant to your industry/environment

[PHASE 2] EMULATION PLAN DEVELOPMENT
    ↓  Define scope and rules of engagement
    ↓  Select specific techniques to emulate
    ↓  Map to detection coverage (what should fire, from which tool)
    ↓  Define success/failure criteria for each technique

[PHASE 3] EXECUTION
    ↓  Execute techniques in controlled environment
    ↓  Blue team monitors without prior knowledge of specific timing
    ↓  Document all actions with precise timestamps

[PHASE 4] DETECTION ANALYSIS
    ↓  Compare expected detections vs. actual detections
    ↓  Identify missed detections (visibility gaps)
    ↓  Identify false negatives (rules exist but didn't fire)

[PHASE 5] REPORTING AND IMPROVEMENT
    ↓  Document findings with ATT&CK technique mapping
    ↓  Create prioritized remediation plan
    ↓  Tune detection rules; add missing coverage
    ↓  Schedule follow-up emulation to validate fixes
```

---

## 2. Emulation Frameworks and Tools

### 2.1 MITRE Caldera

**MITRE Caldera** is an open-source, automated adversary emulation platform developed and maintained by MITRE. It is purpose-built for SOC validation and red team automation.

**Architecture:**

```
[Caldera Server]
      ↓  (HTTP/HTTPS C2)
[Caldera Agent (Sandcat)]  ←── deployed on target system
      ↓
Executes TTPs → Reports results back to server
```

**Key Features:**

| Feature | Description |
|---|---|
| **ATT&CK Integration** | Every ability maps directly to a MITRE ATT&CK technique |
| **Adversary Profiles** | Pre-built profiles based on known threat actors |
| **Automated Operations** | Full attack chains run autonomously based on defined profiles |
| **Plugin Architecture** | Extensible via plugins (Stockpile, Compass, FACTS, etc.) |
| **Multi-Platform Agents** | Windows, Linux, macOS agent support |
| **Human-in-the-Loop Mode** | Manual approval of each step for controlled execution |
| **Reporting** | Auto-generated operation reports with ATT&CK technique mapping |

**Caldera Workflow — Example Phishing Simulation:**

```
1. Define adversary profile: "APT28-like Initial Access"
2. Add abilities:
     - T1566.001: Send spearphishing attachment (simulated)
     - T1059.001: Execute PowerShell payload
     - T1071.001: Establish HTTP C2 beacon
3. Deploy Sandcat agent on test endpoint
4. Run operation: Caldera executes abilities in sequence
5. Review results: Which steps executed? What was detected?
6. Generate ATT&CK navigator heatmap of covered techniques
```

**Getting Started:**
```bash
# Clone and run Caldera locally
git clone https://github.com/mitre/caldera.git --recursive
cd caldera
pip install -r requirements.txt
python server.py --insecure
# Access UI at http://localhost:8888
```

---

### 2.2 Atomic Red Team

**Atomic Red Team** (by Red Canary) is a library of small, focused, independently executable tests mapped to MITRE ATT&CK techniques. Each "atomic test" simulates a single technique with minimal dependencies.

**Philosophy:** *"Atomic tests are the unit tests of security."* Each test is narrow, repeatable, and produces a specific observable artifact.

**Example — Atomic Test for T1003.001 (LSASS Credential Dumping):**

```powershell
# Atomic Test: Dump LSASS using Task Manager method
# ATT&CK: T1003.001
# Platform: Windows

# Execution:
rundll32.exe C:\windows\system32\comsvcs.dll MiniDump \
  (Get-Process lsass).id $env:TEMP\lsass.dmp full

# Expected Detection:
# - EDR should alert: lsass.exe memory access by non-system process
# - SIEM should log: rundll32 with comsvcs.dll argument
```

**Atomic vs. Caldera — When to Use Each:**

| Aspect | Atomic Red Team | MITRE Caldera |
|---|---|---|
| **Scope** | Single technique tests | Full attack chain automation |
| **Complexity** | Low — run individual scripts | Medium — requires setup and agents |
| **Best For** | Detection rule validation | End-to-end TTX and SOC validation |
| **Flexibility** | Very high — custom any test | High — customizable profiles |
| **Reporting** | Manual | Automated with ATT&CK mapping |
| **Learning Curve** | Low | Medium |

---

### 2.3 Other Notable Emulation Tools

| Tool | Description | Best Use Case |
|---|---|---|
| **Cobalt Strike** | Commercial C2 and red team platform | Professional red team engagements |
| **Metasploit Framework** | Exploitation and post-exploitation | Vulnerability validation and lateral movement testing |
| **Sliver** | Open-source C2 framework | C2 communication detection testing |
| **PurpleSharp** | .NET adversary simulation tool | Windows-focused TTP emulation |
| **Prelude Operator** | Automated emulation platform | Continuous validation |
| **VECTR** | Tracking and reporting platform | Purple team exercise management and metrics |

---

## 3. Red Team and Blue Team Collaboration

### 3.1 Traditional Red vs. Blue — The Problem

In a traditional model, Red and Blue teams operate in isolation:

```
[Red Team]                          [Blue Team]
Attacks environment       vs.       Monitors and defends
Results delivered as                Limited visibility into
a final report weeks later          what techniques were used
       ↓                                   ↓
  Findings often                  Detections either fired
  feel abstract                   or didn't — but no feedback
```

**The gap:** Red team findings don't immediately improve Blue team detection. Months pass between an emulation and any resulting tuning work. The cycle is too slow.

---

### 3.2 Purple Teaming — Collaborative Emulation

**Purple Teaming** eliminates the adversarial separation. Red and Blue work **side by side**, executing techniques one at a time and immediately analyzing whether detection fired, what the alert looked like, and how to improve it.

```
[Purple Team Session]

Red executes: T1059.001 — PowerShell encoded command
        ↓
Blue checks: Did SIEM fire? Did EDR alert?
        ↓
[IF DETECTED]                    [IF NOT DETECTED]
Document what fired →            Investigate why →
Confirm alert quality            Add/tune detection rule
Note false positive risk         Validate after rule change
        ↓                                ↓
Move to next technique ←────────────────┘
```

**Benefits of Purple Teaming:**

| Benefit | Impact |
|---|---|
| Real-time detection feedback | Tuning happens during the session, not months later |
| Shared understanding of TTPs | Blue team understands *how* attacks work, not just *that* they happen |
| Prioritized coverage improvements | Gaps identified and addressed against real threat actor profiles |
| Improved analyst confidence | Analysts see real attack artifacts and learn to recognize them |
| Measurable progress | Detection coverage can be quantified before and after each session |

---

### 3.3 Purple Team Session Structure

A well-run purple team session follows this format:

**Pre-Session:**
- Select adversary profile and specific techniques to test
- Define detection expectations for each technique ("this should trigger rule X in Splunk")
- Ensure all logging and tooling is configured and healthy
- Establish a shared communication channel (e.g., dedicated Slack channel or Teams chat)

**During Session:**
```
For each technique:
1. Red announces: "Executing T1053.005 — Scheduled Task persistence"
2. Red executes the technique on the agreed test system
3. Blue checks: Within 5 minutes, did an alert fire?
4. Both teams review the alert together:
   - Is the alert context sufficient for triage?
   - Is the severity appropriate?
   - Are there false positive risks?
5. If no alert: investigate log coverage and add/tune rule live
6. Document outcome: Detected / Not Detected / Partially Detected
7. Move to next technique
```

**Post-Session:**
- Generate detection coverage report (ATT&CK navigator heatmap)
- Prioritize gaps by risk and frequency of technique use by target adversaries
- Assign tuning tasks with owners and due dates
- Schedule follow-up session to validate improvements

---

### 3.4 Measuring Purple Team Effectiveness

Track detection coverage improvement using the **ATT&CK Coverage Score**:

```
Coverage Score = (Techniques Detected / Techniques Tested) × 100

Example:
  Tested: 20 techniques from APT28 profile
  Detected: 14 techniques
  Coverage Score: 14/20 × 100 = 70%

  Goal: Improve to 90%+ coverage for priority threat profiles
  within 2 purple team cycles
```

Use **ATT&CK Navigator** to visualize this as a heatmap — green for detected, red for missed, yellow for partial.

---

## 4. Case Study: APT28 (Fancy Bear) Emulation

**APT28**, also known as **Fancy Bear**, is a Russian nation-state threat actor attributed to the GRU (Russian military intelligence). The group has conducted high-profile campaigns targeting government agencies, political organizations, military targets, and critical infrastructure globally.

### APT28 TTP Profile (MITRE ATT&CK Mapped)

| Tactic | Technique ID | Technique | APT28 Procedure |
|---|---|---|---|
| Initial Access | T1566.001 | Spearphishing Attachment | Malicious Office documents with macros |
| Initial Access | T1566.002 | Spearphishing Link | Credential harvesting via fake login pages |
| Execution | T1059.001 | PowerShell | Encoded PowerShell for payload delivery |
| Persistence | T1053.005 | Scheduled Task | Tasks created for backdoor persistence |
| Credential Access | T1003.001 | LSASS Dump | Mimikatz for credential harvesting |
| Lateral Movement | T1021.001 | Remote Desktop Protocol | RDP using harvested credentials |
| C2 | T1071.001 | Web Protocols | HTTP/HTTPS C2 via custom implants (X-Agent) |
| Exfiltration | T1041 | Exfiltration over C2 | Data sent through existing C2 channel |

### Emulation Execution Plan (Condensed)

**Objective:** Validate SOC detection against APT28's known initial access and lateral movement TTPs.

**Phase 1 — Initial Access Simulation:**
```
1. Send simulated spearphishing email with macro-enabled DOCX
2. On test endpoint: enable macros → PowerShell executes
3. PowerShell downloads and runs simulated payload
4. Payload establishes HTTP beacon to Caldera C2

Expected Detections:
  - Email gateway: malicious attachment blocked/flagged
  - EDR: Office spawning PowerShell (T1059.001)
  - SIEM: PowerShell with encoded command (T1059.001)
  - NDR: HTTP beacon to test C2 domain (T1071.001)
```

**Phase 2 — Credential Theft Simulation:**
```
1. On test endpoint: simulate LSASS memory access via Atomic Test
2. Extract simulated credential from memory

Expected Detections:
  - EDR: Non-system process accessing lsass.exe (T1003.001)
  - SIEM: Alert for comsvcs.dll or ProcDump execution
```

**Phase 3 — Lateral Movement Simulation:**
```
1. Using harvested test credential, attempt RDP to adjacent test system
2. Create scheduled task for persistence on second system

Expected Detections:
  - SIEM: New RDP session with recently-seen credential (T1021.001)
  - EDR: Scheduled task creation by non-admin process (T1053.005)
  - SIEM: Lateral movement correlation rule (new host access)
```

### APT28 Emulation Findings — Sample Report Output

| Technique | Expected Detection | Actual Result | Gap Identified |
|---|---|---|---|
| T1566.001 Phishing Attachment | Email gateway alert | ✅ Detected | None |
| T1059.001 PowerShell | EDR + SIEM alert | ⚠️ Partial | SIEM rule missed encoded args |
| T1003.001 LSASS Dump | EDR alert | ✅ Detected | None |
| T1071.001 HTTP C2 | NDR alert | ❌ Missed | No DNS/proxy logging for test domain |
| T1021.001 RDP Lateral Movement | SIEM correlation | ⚠️ Partial | Alert fired but low severity — not escalated |
| T1053.005 Scheduled Task | EDR alert | ✅ Detected | None |

**Remediation Actions:**
1. Add SIEM rule for PowerShell `-EncodedCommand` argument detection
2. Ensure all outbound DNS queries are logged and analyzed by NDR
3. Increase severity of RDP lateral movement alert from Low to High
4. Add threat intel integration for new domain detection at DNS layer

---

## 5. Continuous Improvement Through Emulation

### Emulation Maturity Progression

| Stage | Description | Activities |
|---|---|---|
| **Ad Hoc** | Occasional, unstructured testing | One-off penetration tests |
| **Defined** | Structured emulation against known adversary profiles | Annual red team + MITRE ATT&CK mapping |
| **Managed** | Regular purple team sessions; tracked coverage metrics | Quarterly purple team cycles; ATT&CK navigator heatmaps |
| **Optimized** | Continuous automated validation + live detection tuning | Atomic Red Team in CI/CD pipeline; weekly coverage scoring |

### Building a Continuous Validation Pipeline

At the highest maturity level, organizations integrate atomic emulation tests into an automated pipeline:

```
[Weekly Automated Schedule]
        ↓
Atomic Red Team tests execute on isolated test endpoints
        ↓
SIEM/EDR alert telemetry captured and analyzed
        ↓
Coverage score calculated and compared to previous week
        ↓
Regressions (previously-detected techniques now missed) flagged automatically
        ↓
SOC team reviews and remediates coverage gaps
        ↓
Updated coverage score published to security dashboard
```

This transforms adversary emulation from a periodic event into a **continuous quality assurance process** for detection capabilities.

---

## 6. Recommended Learning Resources

| Resource | Focus | Access |
|---|---|---|
| **MITRE ATT&CK Framework** | TTP reference library and adversary profiles | Free — attack.mitre.org |
| **MITRE Caldera** | Automated emulation platform | Free, open-source — GitHub |
| **Atomic Red Team (Red Canary)** | Individual technique test library | Free, open-source — GitHub |
| **ATT&CK Navigator** | Visual TTP coverage mapping | Free — mitre-attack.github.io |
| **Red Canary Threat Detection Report** | Annual real-world TTP prevalence data | Free — redcanary.com |
| **MITRE CTID Emulation Plans** | Full adversary emulation plans (APT29, FIN6, etc.) | Free — GitHub |
| **APT28 MITRE Profile** | Detailed TTP breakdown with procedure examples | Free — attack.mitre.org |
| **VECTR** | Purple team tracking and reporting | Free, open-source — GitHub |

---

## Conclusion

Adversary emulation is the most rigorous method available for validating SOC capabilities against real-world threats. By grounding simulations in threat intelligence, leveraging frameworks like **MITRE Caldera** and **Atomic Red Team**, and closing the loop through **purple team collaboration**, organizations move from hoping their defenses work to knowing exactly which techniques they can and cannot detect.

The goal is not a perfect score — it is a continuously improving one. Each emulation cycle reveals gaps, each tuning cycle closes them, and each measured improvement demonstrates that the SOC is genuinely hardening against the threats that matter most.

> **Next Steps:** Pull the MITRE ATT&CK profile for the threat actor most relevant to your industry. Pick five of their highest-frequency techniques. Run the corresponding Atomic Red Team tests against a test endpoint. Check whether your SIEM and EDR fired. That gap analysis is your roadmap.
