# Technical Documentation

## Project

Windows SMB Brute-Force Detection and SOC Response Lab

## Objective

Build an authorized SOC lab to simulate SMB authentication failures, collect Windows security telemetry, detect repeated authentication failures using Wazuh, investigate the activity, perform firewall containment, and verify recovery.

## Lab Components

| Component | Role |
|---|---|
| Kali Linux | Attack simulation |
| OPNsense | Firewall and network monitoring |
| Windows 11 | Monitored endpoint |
| Sysmon | Endpoint process telemetry |
| Wazuh Agent | Windows event collection |
| Ubuntu | Wazuh Manager and SIEM |
| Wazuh Dashboard | SOC monitoring and investigation |

## IP Addressing

| System | IP Address |
|---|---|
| Kali Linux | 192.168.1.10 |
| OPNsense LAN | 192.168.1.1 |
| OPNsense DMZ | 192.168.2.1 |
| OPNsense OPT2 | 192.168.3.1 |
| Ubuntu Wazuh | 192.168.2.20 |
| Windows 11 | 192.168.3.20 |

## Attack Simulation

The lab generated controlled SMB authentication failures from Kali Linux against the Windows 11 endpoint.

Target service:

`SMB / TCP 445`

Target account:

`win11soc`

## Windows Detection

Windows Security Event ID `4625` records failed authentication attempts.

The observed event contained:

- Logon Type: 3
- Workstation: KALI
- Source IP: 192.168.1.10
- Target Account: win11soc

## Wazuh Detection

Wazuh Rule `60122` detects the Windows authentication failure.

A custom rule `100100` was created to correlate repeated failures.

Detection threshold:

- 5 failed logons
- Same source IP
- Within 60 seconds
- Alert Level: 10
- MITRE ATT&CK: T1110

## Investigation

The source IP was identified as:

`192.168.1.10`

The target endpoint was:

`192.168.3.20`

The activity was associated with SMB authentication failures over TCP/445.

## Containment

OPNsense was used to temporarily block:

```text
Source: 192.168.1.10
Destination: 192.168.3.20
Protocol: TCP
Port: 445
Action: BLOCK
