# Task 4: Alert Triage with Threat Intelligence

## Objective
Triage suspicious alerts and validate IOCs with external threat intelligence sources.

## Status
Not Completed.

## Reason for Non-Completion
AlienVault OTX API key generation was blocked because the OTX platform was unavailable during the lab period. Since OTX was a required intelligence source for this task, IOC cross-validation and enrichment could not be completed as required.

## Planned Triage Workflow (When Service Is Available)

1. Analyze mock Wazuh alert: Suspicious PowerShell Execution.
2. Extract IP/hash from alert payload.
3. Validate IOC with VirusTotal and OTX.
4. Update triage priority and case notes based on reputation.

## Expected Output Format

| Alert ID | Description | Source IP | Priority | Status |
|----------|-------------|-----------|----------|--------|
| 004 | PowerShell Execution | 192.168.1.101 | High | Open |
