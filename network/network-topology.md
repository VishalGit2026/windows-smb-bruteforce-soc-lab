# Network Topology

## SOC Lab Architecture

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
