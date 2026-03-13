# Theoretical Knowledge - Threat Intelligence Integration

## What to Learn

### Core Concepts

1. Threat Intelligence Types
- Understand IOCs such as malicious IPs, domains, hashes, and URLs.
- Understand TTPs and campaign-level intelligence using MITRE ATT&CK.
- Understand feed formats and exchange standards such as STIX and TAXII.

2. Integration in SOC
- Learn how threat feeds enrich SIEM alerts automatically.
- Example: Match suspicious source IPs against known C2 infrastructure.

3. Threat Hunting with Intelligence
- Use threat context to hunt for specific ATT&CK techniques such as T1078 (Valid Accounts misuse).
- Prioritize hunts based on relevance to your environment and sector.

## Key Objectives

- Improve alert fidelity by enriching detections with threat context.
- Build practical understanding of intelligence-driven triage and hunting.
- Reduce response time by escalating high-confidence intelligence matches.

## Practical Notes

- In a production SOC, use feed confidence scoring and expiry windows to reduce stale IOC noise.
- Pair IOC matching with behavior detections to avoid over-reliance on indicator-only rules.

