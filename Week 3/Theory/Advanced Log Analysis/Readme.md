# Theoretical Knowledge - Advanced Log Analysis

## What to Learn

### Core Concepts

1. Log Correlation
- Correlate events from firewall, endpoint, and application logs to reveal full attack chains.
- Example: Link repeated failed logins (Event ID 4625) with suspicious outbound traffic from the same source host.

2. Anomaly Detection
- Identify deviations from normal behavior such as unusual login hours, unexpected process execution, and large data transfers.
- Use both statistical baselines and rule-based detections.

3. Log Enrichment
- Add context to logs (GeoIP, user role, asset criticality, threat tags) to improve triage speed and decision quality.

## Key Objectives

- Build skill in multi-source correlation for threat discovery.
- Reduce false positives by combining context with detection logic.
- Produce actionable findings for incident response and escalation.

## Quick Event ID Reference

| Event ID | Description | Why It Matters |
|----------|-------------|----------------|
| 4625 | Failed logon | Brute-force detection |
| 4624 | Successful logon | Correlate with failed attempts |
| 4688 | New process created | Execution visibility |
| 4698 | Scheduled task created | Persistence indicator |
| 7045 | Service installed | Malware/service persistence |
| 1102 | Audit log cleared | Anti-forensics signal |
