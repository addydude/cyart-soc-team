# Q1 — Threat Hunting Practice Activities

**Tools Used:** Elastic Security · Velociraptor · AlienVault OTX  
**MITRE ATT&CK Technique:** T1078 — Valid Accounts

---

## Task 1 — Hypothesis Development

### Hypothesis Statement

> "Unauthorized privilege escalation is occurring in domain accounts via special privilege assignment (Event ID 4672), indicating T1078 — Valid Accounts abuse."

### Hypothesis Components

| Component | Detail |
|---|---|
| Observable Behaviour | Special privileges assigned to non-admin accounts |
| Data Source | Windows Security Event Log |
| MITRE Technique | T1078 — Valid Accounts |
| Expected Evidence | Event ID 4672 for accounts without admin roles in AD |

### MITRE ATT&CK Mapping

| Technique ID | Name | Tactic |
|---|---|---|
| T1078 | Valid Accounts | Initial Access, Persistence, Privilege Escalation |
| T1059 | Command and Scripting Interpreter | Execution |
| T1548 | Abuse Elevation Control Mechanism | Privilege Escalation |

---

## Task 2 — Elastic Security Query (Event ID 4672)

### KQL Query Used

```
event.code : "4672"
AND winlog.channel : "Security"
AND NOT user.name : ("SYSTEM" OR "LOCAL SERVICE" OR "NETWORK SERVICE")
AND NOT user.name : *$
| sort @timestamp desc
```

### Steps Performed

1. Opened **Elastic Security → Discover**
2. Set time range to last 24 hours
3. Pasted the KQL query above
4. Filtered out known system accounts (SYSTEM, LOCAL SERVICE, NETWORK SERVICE)
5. Reviewed results for unexpected privilege assignments

### Findings — Event ID 4672 Log

| Timestamp | User | Event ID | Host | Privileges | Notes |
|---|---|---|---|---|---|
| 2025-08-18 15:00:00 | testuser | 4672 | WS-CORP-042 | SeDebugPrivilege | Unexpected admin role |
| 2025-08-18 14:47:12 | jsmith | 4672 | WS-CORP-011 | SeTcbPrivilege | Non-admin account |
| 2025-08-18 13:22:55 | svc_backup | 4672 | SRV-FILE-01 | SeSecurityPrivilege | Verify service account |
| 2025-08-18 09:05:40 | adm_deploy | 4672 | DC-PROD-01 | SeDebugPrivilege | Legitimate admin |

**Result:** 3 of 4 events flagged. `testuser` and `jsmith` are high priority — neither holds an admin role in Active Directory.

---

## Task 3 — Threat Intelligence Hunt (AlienVault OTX)

### Steps Performed

1. Opened **otx.alienvault.com**
2. Searched for `T1078` in the search bar
3. Filtered by **Indicator Type → IP**
4. Investigated IP **205.210.31.161**
5. Cross-referenced results with internal Elastic Security auth logs

### OTX Analysis — IP 205.210.31.161

| Field | Value |
|---|---|
| IP | 205.210.31.161 |
| Type | IPv4 |
| Location | Canada |
| ASN | AS396982 (Google) |
| Related Pulses | 50 |
| Related Tags | tpot, honeypot, sensor-tagged, cowrie, suricata |
| Passive DNS | No entries found |
| Network IDS Hits | No entries found |

**Assessment:** This IP is a known honeypot/sensor-monitored address. Assessed as a **false positive** — not confirmed adversary infrastructure. Cross-referencing with internal Elastic logs still performed.

---

## Task 4 — Velociraptor Endpoint Query

### Steps Performed

1. Opened **Velociraptor → Hunt Manager → New Hunt**
2. Targeted suspicious hosts: `WS-CORP-042` and `WS-CORP-011`
3. Ran the following VQL queries

### Query 1 — Enumerate All Running Processes

```sql
SELECT * FROM processes
```

### Query 2 — Filter by Suspicious Users

```sql
SELECT Pid, Name, Exe, CommandLine, Username
FROM processes
WHERE Username IN ('testuser', 'jsmith')
```

### Query 3 — Cross-Reference OTX IP

```sql
SELECT * FROM netstat()
WHERE RemoteAddr = "205.210.31.161"
```

### Query 4 — Check for Malicious Tools

```sql
SELECT * FROM pslist()
WHERE Name =~ "mimikatz|procdump|lsass"
```

### Velociraptor Findings

| Query | Result | Notes |
|---|---|---|
| SELECT * FROM processes | All running processes listed | Baseline check completed |
| Filter by testuser / jsmith | cmd.exe, powershell.exe found | Suspicious processes under testuser |
| netstat() — 205.210.31.161 | No active connection | OTX IP not active on endpoint |
| pslist() malware filter | No match | No credential dumping tools found |

### Critical Finding

| PID | Process | User | Command | Verdict |
|---|---|---|---|---|
| 4812 | cmd.exe | testuser | `net localgroup administrators testuser /add` | Privilege Escalation Confirmed |
| 5124 | powershell.exe | testuser | `-enc [base64 payload]` | Encoded Command — Suspicious |

---
