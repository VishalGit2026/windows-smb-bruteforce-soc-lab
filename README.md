# Windows SMB Brute-Force Detection and SOC Response Lab

## Overview

An authorized hands-on SOC lab demonstrating the detection, investigation, containment, and recovery of SMB authentication brute-force activity.

The lab combines Kali Linux, OPNsense, Windows 11, Sysmon, and Wazuh SIEM to demonstrate an end-to-end SOC workflow.

## Architecture

![SOC Network Topology](network/network-topology.png)

```text
Kali Linux
192.168.1.10
      |
      | SMB / TCP 445
      v
OPNsense Firewall
      |
      v
Windows 11
192.168.3.20
      |
      | Security Events + Sysmon
      v
Wazuh Agent
      |
      v
Ubuntu Wazuh Server
192.168.2.20
      |
      v
Wazuh SOC Dashboard

## Objectives

- Simulate controlled SMB authentication failures.
- Collect Windows Security Event ID 4625.
- Monitor endpoint activity using Sysmon.
- Detect repeated authentication failures with Wazuh.
- Create a custom brute-force correlation rule.
- Investigate the source and target activity.
- Demonstrate firewall-based containment.
- Verify recovery after containment rollback.

## Lab Components

| Component | Role |
|---|---|
| Kali Linux | Attack simulation |
| OPNsense | Firewall and network monitoring |
| Windows 11 | Monitored endpoint |
| Sysmon | Endpoint telemetry |
| Wazuh Agent | Event collection |
| Ubuntu | Wazuh Manager / SIEM |
| Wazuh Dashboard | SOC investigation and monitoring |

## Network

| System | IP Address |
|---|---|
| Kali Linux | 192.168.1.10 |
| OPNsense LAN | 192.168.1.1 |
| OPNsense DMZ | 192.168.2.1 |
| OPNsense OPT2 | 192.168.3.1 |
| Ubuntu Wazuh | 192.168.2.20 |
| Windows 11 | 192.168.3.20 |

## Attack Scenario

A controlled SMB authentication attack was simulated from:

`192.168.1.10`

against:

`192.168.3.20`

using:

`SMB / TCP 445`

The target account used during the lab was:

`win11soc`

## Detection

Windows generated:

`Event ID 4625`

The observed event included:

- Logon Type: 3
- Workstation: KALI
- Source IP: 192.168.1.10
- Target Account: win11soc
- Failure Reason: Unknown user name or bad password

Wazuh Rule `60122` identified the authentication failure.

## Custom Detection Rule

A custom Wazuh rule was created:

| Parameter | Value |
|---|---|
| Rule ID | 100100 |
| Level | 10 |
| Frequency | 5 |
| Timeframe | 60 seconds |
| Parent Rule | 60122 |
| MITRE ATT&CK | T1110 - Brute Force |

The rule detects five failed logons from the same source IP within 60 seconds.

## Investigation

The investigation correlated:

Kali Source IP
      ↓
OPNsense TCP/445 Traffic
      ↓
Windows Event ID 4625
      ↓
Wazuh Rule 60122
      ↓
Custom Rule 100100
