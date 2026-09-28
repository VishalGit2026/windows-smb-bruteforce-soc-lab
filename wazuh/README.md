# Wazuh Detection Configuration

## Purpose

This directory contains the Wazuh configuration used in the Windows SMB brute-force detection SOC lab.

## Detection Flow

```text
Windows Event ID 4625
        |
        v
Wazuh Rule 60122
        |
        v
5 failed logons
        |
        v
Same source IP
        |
        v
Within 60 seconds
        |
        v
Custom Rule 100100
        |
        v
Level 10 Alert
        |
        v
MITRE ATT&CK T1110
