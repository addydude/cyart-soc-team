# Task 5: Evidence Preservation and Analysis

## Objective
Collect volatile evidence, preserve artifacts, and maintain evidence integrity with hashing.

## 1. Volatile Data Collection

Velociraptor query executed:
- `SELECT * FROM netstat`

Output was exported to:
- `network_connections.csv`

## 2. Memory Evidence Collection

Memory acquisition initiated with:
- `Artifact.Windows.Memory.Acquisition`

Collection result:
- Acquisition process reached timeout at 600 seconds in remote collection context.
- Partial evidence package was still preserved and hashed for integrity tracking.

## 3. Chain of Custody Entry

| Item | Description | Collected By | Date | Hash Value (SHA256) |
|------|-------------|--------------|------|---------------------|
| Memory Dump | Server-Y Dump (Partial Container) | SOC Analyst | 2026-03-06 | b207d4cbc50d815b5981bc61cc0282a48ac9d11ce255bde280502617218a348b |

## Artifacts

- `network_connections.csv`
- `VELOCIRAPTOR NETSTAT OUTPUT.png`
- `memory_hash.png`
- `memory_hash_result.png`
