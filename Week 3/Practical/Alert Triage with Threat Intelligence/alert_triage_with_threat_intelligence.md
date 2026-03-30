# Alert Triage with Threat Intelligence

## Objective
To triage a PowerShell-based alert in Wazuh and validate associated Indicators of Compromise (IOCs) using threat intelligence platforms such as VirusTotal and AlienVault OTX.

---

## Tools Used
- Wazuh (SIEM & alert monitoring)
- VirusTotal (Threat intelligence platform)
- AlienVault OTX (Open Threat Exchange)

---

## Activities Performed

1. Generated suspicious PowerShell activity on a Windows endpoint.
2. Monitored alerts in Wazuh SIEM dashboard.
3. Identified relevant PowerShell-based alert.
4. Extracted IOC (Source IP address).
5. Validated IOC using VirusTotal and AlienVault OTX.
6. Documented findings and analysis.

---

## Alert Details (Triage Simulation)

| Alert ID | Description                              | Source IP      | Priority | Status |
|----------|------------------------------------------|----------------|----------|--------|
| 004      | PowerShell executing process discovery   | 192.168.1.3    | High     | Open   |

---

## Triage Analysis

The alert indicates that PowerShell was used to perform process discovery on the system. This behavior is commonly associated with reconnaissance techniques where an attacker attempts to enumerate running processes. 

The use of PowerShell increases the level of suspicion because it is frequently abused in post-exploitation activities. The presence of such behavior warrants further investigation to determine whether it was legitimate administrative activity or potentially malicious.

---

## IOC Validation

### VirusTotal Analysis
The source IP address **192.168.1.3** was searched in VirusTotal. No meaningful results or malicious reputation were found. This is expected because the IP belongs to a private internal network range, which is not indexed in public threat intelligence databases.

### AlienVault OTX Analysis
The same IP address was analyzed in AlienVault OTX. No pulses or threat intelligence matches were found. As a private IP address, it is not publicly observable and therefore does not appear in global threat intelligence feeds.

---

## Summary

Threat intelligence analysis of the source IP 192.168.1.3 using VirusTotal and AlienVault OTX returned no malicious reputation. The IP is a private internal address, limiting external intelligence correlation. However, the PowerShell activity indicates suspicious reconnaissance behavior and should be further investigated for potential misuse or unauthorized execution.

---

## Conclusion

Although external threat intelligence sources did not provide evidence of malicious activity, the behavior detected in Wazuh is suspicious. PowerShell-based process discovery is commonly associated with attacker reconnaissance. This highlights the importance of behavioral analysis when IOC reputation is unavailable.

---

## Key Insight

Threat intelligence platforms are more effective for public indicators such as external IPs, domains, and file hashes. In contrast, private IPs require contextual and behavioral analysis within the monitored environment.

---

## MITRE ATT&CK Mapping 

- **Technique:** T1057 (Process Discovery)
- **Tactic:** Discovery

---