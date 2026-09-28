# Incident Report
## Windows SMB Brute-Force Detection and SOC Response

---

## 1. Incident Identification

**Incident ID:** SOC-WIN11-20260928-001  
**Incident Title:** SMB Authentication Brute-Force Attempt Against Windows 11  
**Incident Type:** Credential Access / Authentication Attack  
**Environment:** Authorized SOC Lab  
**Detection Platform:** Wazuh SIEM  

---

## 2. Environment

| Component | Details |
|---|---|
| Attacker | Kali Linux |
| Attacker IP | 192.168.1.10 |
| Firewall | OPNsense |
| Target | Windows 11 |
| Target IP | 192.168.3.20 |
| Endpoint Agent | Wazuh Agent |
| Agent Name | WIN11-SOC |
| SIEM | Wazuh |
| Wazuh Server | Ubuntu |
| Wazuh Server IP | 192.168.2.20 |
| Target Service | SMB |
| Destination Port | TCP/445 |

---

## 3. Executive Summary

A controlled SMB authentication brute-force simulation was performed from the Kali Linux lab host (192.168.1.10) against the Windows 11 host (192.168.3.20).

The authentication attempts targeted the `win11soc` account over SMB TCP/445.

Windows generated Security Event ID 4625 for the failed network logons. The events showed Logon Type 3 and the source address 192.168.1.10.

Wazuh collected the Windows security events and generated the built-in authentication failure rule 60122.

A custom Wazuh correlation rule, Rule 100100, was configured to detect five failed logons from the same source IP within 60 seconds. The custom rule generated a Level 10 alert mapped to MITRE ATT&CK technique T1110 (Brute Force).

The incident was investigated, contained through an OPNsense firewall block, and recovery was verified by restoring SMB connectivity.

---

## 4. Attack Scenario

The lab simulated repeated SMB authentication failures against the Windows 11 endpoint.

### Attack Flow

Kali Linux
`192.168.1.10`

↓

OPNsense Firewall

↓

SMB / TCP 445

↓

Windows 11
`192.168.3.20`

↓

Security Event ID 4625

↓

Wazuh Agent

↓

Wazuh Manager

↓

Custom Rule 100100

↓

Brute-Force Alert

---

## 5. Indicators

| Indicator | Value |
|---|---|
| Source IP | 192.168.1.10 |
| Source Host | KALI |
| Destination IP | 192.168.3.20 |
| Destination Host | WIN11-SOC |
| Protocol | TCP |
| Destination Port | 445 |
| Service | SMB |
| Target Account | win11soc |
| Windows Event ID | 4625 |
| Logon Type | 3 |
| Wazuh Rule | 60122 |
| Custom Rule | 100100 |
| Alert Level | 10 |
| Frequency | 5 |
| Detection Window | 60 seconds |
| MITRE ATT&CK | T1110 - Brute Force |

---

## 6. Detection

Windows generated Event ID 4625 for failed network authentication attempts.

Important event attributes included:

- Logon Type: 3
- Workstation: KALI
- Source Network Address: 192.168.1.10
- Target Account: win11soc
- Failure Reason: Unknown user name or bad password

Wazuh Rule 60122 identified the failed authentication event.

The custom rule 100100 correlated five failed logons from the same source IP within 60 seconds and generated a Level 10 alert.

---

## 7. Custom Wazuh Detection Rule

**Rule ID:** 100100  
**Level:** 10  
**Frequency:** 5  
**Timeframe:** 60 seconds  
**Parent Rule:** 60122  
**MITRE ATT&CK:** T1110 - Brute Force  

### Detection Logic

```text
Windows Event ID 4625
        ↓
Wazuh Rule 60122
        ↓
5 failed logons
        ↓
Same source IP
        ↓
Within 60 seconds
        ↓
Custom Rule 100100
        ↓
Level 10 Alert
