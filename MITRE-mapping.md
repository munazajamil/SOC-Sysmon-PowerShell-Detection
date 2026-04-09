# 🗺️ MITRE ATT&CK Mapping — Project 2

**Project:** Fileless Attack Detection with Sysmon + Wazuh  
**Analyst:** Munnaza Jamil  
**Date:** April 2026  
**Platform:** Windows 11 + Wazuh SIEM  

---

## 🔍 Tactics Observed in This Lab

| Tactic               | Description                                      | Source        |
|---------------------|--------------------------------------------------|--------------|
| Defense Evasion     | Encoded PowerShell to bypass detection           | Attack 1     |
| Discovery           | System info and process enumeration              | Attack 1 & 3 |
| Initial Access      | Simulated entry via PowerShell abuse             | Attack 2     |
| Persistence         | Registry/startup behaviors flagged               | Wazuh Logs   |
| Privilege Escalation| LSASS access attempt                             | Attack 3     |

---

## 🧠 Technique-Level Mapping

### 🔹 T1059.001 — PowerShell

| Field            | Detail |
|------------------|--------|
| Tactic           | Execution, Defense Evasion |
| Attack Simulated | Base64 encoded command via `-EncodedCommand` |
| Wazuh Rule       | 91816 |
| Event ID         | 4104 |
| Log Channel      | Microsoft-Windows-PowerShell/Operational |
| Detection        | ✅ Detected |
| Evidence         | scriptBlockText in Wazuh alert |

**📌 What happened:**  
Attacker encoded `whoami; hostname; ipconfig` in Base64.  
PowerShell decoded and executed it.  
Script Block Logging captured the decoded command → full visibility.

---

### 🔹 T1105 — Ingress Tool Transfer

| Field            | Detail |
|------------------|--------|
| Tactic           | Command and Control |
| Attack Simulated | `IEX (New-Object Net.WebClient).DownloadString()` |
| Wazuh Rule       | 62123 |
| Threat Name      | Trojan:PowerShell/Powersploit.C |
| Detection Engine | Windows Defender (AMSI) |
| Severity         | Severe |
| Execution Status | Blocked |
| Detection        | ✅ Blocked + Logged |

**📌 What happened:**  
Fileless script download attempt.  
AMSI intercepted before execution and flagged as trojan.  
Wazuh logged high severity alert.

---

### 🔹 T1003.001 — LSASS Credential Dumping

| Field       | Detail |
|------------|--------|
| Tactic     | Credential Access |
| Attack     | LSASS handle access (Mimikatz simulation) |
| Wazuh Rule | 91815 |
| Event ID   | 4104 |
| LSASS PID  | 1056 |
| Detection  | ✅ Detected |

**📌 What happened:**  
Attacker attempted to access LSASS memory.  
Script Block Logging captured commands → mapped to credential dumping behavior.

---

### 🔹 T1057 — Process Discovery

| Field            | Detail |
|------------------|--------|
| Tactic           | Discovery |
| Attack Simulated | `Get-Process lsass` |
| Wazuh Rule       | 91815 |
| Detection        | ✅ Detected |

**📌 What happened:**  
PowerShell enumerated processes.  
Wazuh identified pattern as process discovery.

---

### 🔹 T1082 — System Information Discovery

| Field            | Detail |
|------------------|--------|
| Tactic           | Discovery |
| Attack Simulated | `whoami`, `hostname`, `ipconfig` |
| Wazuh Rule       | 91816 |
| Detection        | ✅ Detected |

**📌 What happened:**  
Attacker gathered system info.  
Wazuh flagged as reconnaissance activity.

---

## 📊 MITRE ATT&CK Coverage Summary
