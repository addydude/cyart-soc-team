# Task 2: Threat Intelligence Integration

## Objective
Import threat intelligence, enrich alerts, and perform intelligence-driven hunting.

## Status
Not Completed.

## Reason for Non-Completion
AlienVault OTX API key generation was not possible because the OTX site/service was down during the lab period. Due to this dependency, feed import and enrichment validation could not be completed.

## Planned Steps (When Service Is Available)

1. Import OTX feed into Wazuh for IOC matching.
2. Test IOC match using mock IP `192.168.1.100`.
3. Enrich matching alert with reputation details.
4. Run threat hunting query for T1078 behavior and summarize findings.

## Expected Output Format

| Alert ID | IP | Reputation | Notes |
|----------|----|------------|-------|
| 003 | 192.168.1.100 | Malicious (OTX) | Linked to C2 server |
