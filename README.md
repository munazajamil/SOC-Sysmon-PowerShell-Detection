# 🛡️ SOC Project 2: Fileless Attack Detection with Sysmon + Wazuh

![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-blue)
![Sysmon](https://img.shields.io/badge/Telemetry-Sysmon-orange)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## 📌 Project Overview
Deployed a detection lab to identify PowerShell-based fileless attacks 
and Living-off-the-Land (LotL) techniques using Sysmon telemetry forwarded 
to Wazuh SIEM. Wrote custom detection rules mapped to MITRE ATT&CK framework.

## 🏗️ Lab Environment
| Component | Details |
|-----------|---------|
| SIEM | Wazuh 4.x (Docker on WSL2) |
| Endpoint | Windows 11 (Wazuh Agent) |
| Telemetry | Sysmon with SwiftOnSecurity config |
| Attack Tools | Native PowerShell (LotL) |

## ⚔️ Attacks Simulated
| # | Attack | MITRE ID | Result |
|---|--------|----------|--------|
| 1 | Encoded PowerShell Command | T1059.001 | Detected |
| 2 | Download Cradle (IEX) | T1105 | Blocked + Logged |
| 3 | LSASS Memory Access | T1003.001 | Detected |

## 🔧 Custom Wazuh Rules Written
| Rule ID | Description | Level | MITRE |
|---------|-------------|-------|-------|
| 100001 | Encoded PowerShell detected | 12 | T1059.001 |
| 100002 | Download cradle / IEX detected | 13 | T1105 |
| 100003 | LSASS access detected | 15 | T1003.001 |
| 100004 | Reverse shell pattern | 14 | T1059.001 |
| 100005 | Registry persistence | 11 | T1547.001 |

## 📊 Detections in Wazuh Dashboard
![Alerts Dashboard](screenshots/02-alerts-dashboard.png)
![Custom Rule Firing](screenshots/03-custom-rule-firing.png)

## 🗺️ MITRE ATT&CK Coverage
![MITRE Mapping](screenshots/05-mitre-mapping.png)

| Technique | ID | Detection Method |
|-----------|----|-----------------|
| PowerShell | T1059.001 | Sysmon EID 1 + PS Logging |
| Tool Transfer | T1105 | Sysmon EID 1 + Defender |
| Credential Dumping | T1003.001 | Sysmon EID 10 |
| Persistence | T1547.001 | Sysmon EID 13 |

## 🛠️ Tools Used
- Wazuh SIEM, Sysmon, Windows Event Logs
- PowerShell Script Block Logging
- MITRE ATT&CK Navigator

## 👩‍💻 Author
**Munnaza Jamil** — Aspiring SOC Analyst  
[LinkedIn](https://linkedin.com/in/munazajamil) | 
[TryHackMe](https://tryhackme.com/p/munaza.jamil01) | 
[GitHub](https://github.com/munazajamil)
