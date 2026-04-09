🗺️ MITRE ATT&CK Mapping — Project 2
Project: Fileless Attack Detection with Powershell + Wazuh  
Analyst: Munnaza Jamil  
Date: April 2026  
Platform: Windows 11 + Wazuh SIEM
---
Tactics Observed in This Lab
Based on actual Wazuh detections, the following MITRE ATT&CK tactics were triggered:
Tactic	Description	Source
Defense Evasion	Encoded PowerShell to bypass basic detection	Attack 1
Discovery	System info and process enumeration	Attack 1 & 3
Initial Access	Simulated entry via PowerShell abuse	Attack 2
Persistence	Registry/startup behaviors flagged	Wazuh background
Privilege Escalation	LSASS access attempt	Attack 3
---
Technique-Level Mapping
T1059.001 — Command and Scripting Interpreter: PowerShell
Field	Detail
Tactic	Execution, Defense Evasion
Attack Simulated	Base64 encoded command via `-EncodedCommand` flag
Wazuh Rule	91816
Event ID	4104 (PowerShell Script Block Log)
Log Channel	Microsoft-Windows-PowerShell/Operational
Detection	✅ Detected
Evidence	scriptBlockText captured in Wazuh alert JSON
What happened: Attacker encodes `whoami; hostname; ipconfig` in Base64 to hide intent. PowerShell decodes and runs it. Script Block Logging captures the decoded command before execution — giving the SOC analyst full visibility even on obfuscated commands.
---
T1105 — Ingress Tool Transfer
Field	Detail
Tactic	Command and Control
Attack Simulated	`IEX (New-Object Net.WebClient).DownloadString()`
Wazuh Rule	62123 (Level 12)
Threat Name	`Trojan:PowerShell/Powersploit.C`
Detection Engine	Windows Defender via AMSI
Severity	Severe
Execution Status	Suspended (Blocked)
Detection	✅ Blocked + Logged
What happened: Attacker tries to download a PowerShell script directly into memory (fileless). AMSI (Antimalware Scan Interface) intercepts the content before it executes and flags it as a known trojan. Wazuh logs the Defender alert as Level 12 — high severity.
---
T1003.001 — OS Credential Dumping: LSASS Memory
Field	Detail
Tactic	Credential Access
Attack Simulated	Opening handle to LSASS process (Mimikatz simulation)
Wazuh Rule	91815
Event ID	4104 (PowerShell Script Block Log)
LSASS PID	1056
Log Channel	Microsoft-Windows-PowerShell/Operational
Detection	✅ Detected
What happened: Attacker queries and opens a handle to LSASS — the Windows process that holds password hashes in memory. This is the first step Mimikatz performs before dumping credentials. Script Block Logging captured `Get-Process lsass` and `GetProcessById`, which Wazuh mapped to T1057.
---
T1057 — Process Discovery
Field	Detail
Tactic	Discovery
Attack Simulated	`Get-Process lsass` during Attack 3
Wazuh Rule	91815
Rule Description	"PowerShell executing process discovery"
Detection	✅ Detected
What happened: As part of the LSASS attack, PowerShell enumerated running processes. Wazuh recognized this pattern as process discovery behavior and auto-tagged it with T1057.
---
T1082 — System Information Discovery
Field	Detail
Tactic	Discovery
Attack Simulated	`whoami`, `hostname`, `ipconfig` via encoded command
Wazuh Rule	91816
Rule Description	"PowerShell script querying system environment variables"
Detection	✅ Detected
What happened: The encoded PowerShell command queried username, hostname, and network configuration — standard attacker reconnaissance after initial access. Wazuh flagged this as system info discovery.
---
MITRE ATT&CK Coverage Summary
```
Tactics Covered:
├── Execution          → T1059.001 (PowerShell)
├── Defense Evasion    → T1059.001 (Encoded commands)
├── Command & Control  → T1105 (Download cradle)
├── Credential Access  → T1003.001 (LSASS access)
└── Discovery          → T1057, T1082 (Process + System info)
```
---
Defensive Layers That Worked
Layer	What It Caught
AMSI (Windows Defender)	Blocked Trojan:PowerShell/Powersploit.C before execution
PowerShell Script Block Logging	Captured all commands including encoded ones (Event ID 4104)
Wazuh Built-in Rules	Auto-detected and MITRE-mapped all three attacks
Sysmon	Provided deep process telemetry as log source
---
Part of the SOC Analyst Home Lab Portfolio by Munnaza Jamil  
GitHub | LinkedIn
