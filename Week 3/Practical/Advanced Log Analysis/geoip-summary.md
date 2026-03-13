# Task 1: Advanced Log Analysis

## Objective
Correlate logs, detect anomalies, and enrich events with GeoIP context using Elastic Security and sample data.

## 1. Log Correlation

Sample correlation output from imported dataset:

| Timestamp | Event ID | Source IP | Destination IP | Notes |
|----------|----------|-----------|----------------|-------|
| 2025-08-18 12:00:00 | 4625 | 192.168.1.100 | 8.8.8.8 | Suspicious DNS request |

Observed pattern:
- Multiple outbound connections were reviewed alongside failed login activity.
- Correlation helped isolate suspicious access and external communication behavior.

## 2. Anomaly Detection Rule

Rule created in Elastic to flag potential exfiltration:
- Condition: `bytes_out >= 1048576`
- Window: `1m`
- Rule intent: detect high-volume outbound transfer events quickly.

## 3. Log Enrichment (GeoIP)

GeoIP ingest pipeline was tested successfully. Enrichment added country and coordinate metadata to IP records, improving triage quality and analyst context.

50-word summary:
GeoIP enrichment transformed raw IP values into actionable context by appending country and location metadata during ingestion. This reduced investigation time by helping quickly distinguish expected versus unusual destinations. Combined with correlation and anomaly rules, enrichment improved detection confidence and reduced manual effort during alert triage in Elastic.

## Artifacts

- `mock data save.csv`
- `Mock data elastic.png`
- `Google sheets.png`
- `Rule Setup.png`
- `geo_ip_plugin.png`