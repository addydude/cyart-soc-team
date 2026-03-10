# Task 5: Capstone Project (Full IR Cycle)

## 1. Detection and Triage Log
Wazuh successfully detected the anomalous behavior originating from the attacker node.

| Timestamp | Source IP | Alert Description | MITRE Technique |
| :--- | :--- | :--- | :--- |
| 2026-03-06 11:56:43 | 192.168.147.128 | syslog: User authentication failure (vsftpd) | T1190 |

---

## 2. Incident Response Report
*(SANS Template format)*

**Executive Summary:** On March 6, 2026, a critical security incident occurred involving the direct exploitation of a known legacy vulnerability (vsftpd v2.3.4 backdoor) on a publicly exposed Metasploitable2 server. The hostile actor successfully leveraged this vulnerability to gain unauthorized root-level command execution, posing a severe organizational risk. Our Security Operations Center immediately detected the anomalous activity via Wazuh SIEM alerts. The threat was swiftly contained by isolating the compromised virtual machine and permanently blocking the malicious source IP address using CrowdSec, effectively preventing any lateral movement, persistence establishment, or data exfiltration.

**Timeline:**
* 11:45 UTC: Unauthorized external IP initiated targeted port scanning.
* 11:50 UTC: Successful vsftpd backdoor exploitation detected by the platform.
* 11:56 UTC: SOC actively alerted by Wazuh high-priority ruleset trigger.
* 12:05 UTC: Malicious IP explicitly blacklisted across the network via CrowdSec.
* 12:10 UTC: Ping verification confirmed absolute network isolation and containment.

**Recommendations:** To prevent future occurrences, the organization must immediately decommission or heavily segment all legacy training environments from primary production networks. Furthermore, strict firewall access control lists must be meticulously implemented to ensure administrative protocols like FTP are never exposed directly to the public internet. Finally, continuous vulnerability scanning must be actively enforced across all internal assets.

---

## 3. Stakeholder Briefing

**To: Executive Management**

Today, our security team intercepted and neutralized a cyberattack targeting one of our legacy training servers. An external attacker exploited an outdated file-sharing service to gain unauthorized system access. Fortunately, our automated defense tools instantly flagged the intrusion. The security team immediately isolated the compromised server and blocked the attacker's network address, stopping the attack in its tracks before any sensitive data could be accessed or stolen. We have verified the containment and are actively reviewing our network perimeter rules to ensure legacy systems are no longer accessible from the outside. No critical business operations were impacted.

---

## 4. Artifacts and Evidence

All screenshots are bundled in this PDF for easy submission:

- [Task 5 Screenshots PDF](Task_5_Screenshots.pdf)

**1. Attack Execution (Metasploit):**
![Metasploit Shell](screenshots/metasploit_attack.png)

**2. Failed FTP Login Attempt:**
![FTP Failed Login](screenshots/ftp_failed.png)

**3. Alert Detection (Wazuh):**
![Wazuh Alert](screenshots/wazuh_detection.png)

**4. Threat Containment (CrowdSec & Ping Fail):**
![CrowdSec Block](screenshots/crowdsec_block.png)
