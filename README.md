# Enterprise-MultiVendor-IPsec-VTI-Lab

## Overview
This repository documents a comprehensive, multi-vendor network security laboratory built in EVE-NG. The goal isn't just to connect a few routers, but to simulate a realistic enterprise infrastructure with headquarters and remote branches, focusing on secure routing, IPsec VPNs, and firewall administration. 

## Tools & Technologies

[![Check Point](https://img.shields.io/badge/Check_Point-R81.20-red?style=for-the-badge&logo=checkpoint&logoColor=white)](https://www.checkpoint.com/)
[![Cisco](https://img.shields.io/badge/Cisco-IOS-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.cisco.com/)
[![EVE-NG](https://img.shields.io/badge/EVE--NG-Lab_Emulation-orange?style=for-the-badge)](https://www.eve-ng.net/)

[![pfSense](https://img.shields.io/badge/pfSense-Firewall-212529?style=for-the-badge&logo=pfsense&logoColor=white)](https://www.pfsense.org/)
[![Linux](https://img.shields.io/badge/Linux-Server-FCC624?style=for-the-badge&logo=linux&logoColor=black)](https://www.kernel.org/)
[![Active Directory](https://img.shields.io/badge/Active_Directory-Windows_Server-0078D4?style=for-the-badge&logo=windows&logoColor=white)](https://microsoft.com)
## Current Progress & Roadmap
- [x] **Check Point R81.20 (Gateway & Management)**: Full setup, Route-Based VPN (VTI), OSPF over IPsec, NAT, and Security Policies. 
- [ ] **Fortinet / pfSense**: Planned for branch expansions to test multi-vendor IPsec compatibility.
- [ ] **WEB & Mail Servers**: Planned deployment in the DMZ. This involves diving into application-layer security, configuring Apache/Nginx, and setting up SQL databases to simulate real-world vulnerable and secure services.

| VM / Device | Port | Zone | IP Address | Comment |
| :--- | :--- | :--- | :--- | :--- |
| **EVE-NG Cloud** | `pnet1` | WAN | `192.168.123.1/24` | Upstream Lab GW / Internet |
| **CS-ISP-HQ** | `Gi0/1` | WAN | `192.168.123.200/24` | Link to Cloud (pnet1) |
| | `Gi0/2` | WAN1-HQ | `10.0.2.254/24` | Primary WAN for HQ |
| | `Gi0/0` | WAN2-HQ | `10.0.3.254/24` | Secondary WAN for HQ |
| | `Gi0/3` | WAN-Kali | `10.0.4.254/24` | External Testing Zone |
| **CS-ISP-BR1** | `Gi0/1` | WAN | `192.168.123.201/24` | Link to Cloud (pnet1) |
| | `Gi0/0` | WAN1-BR1 | `10.1.2.254/24` | Primary WAN for BR1 |
| **CP-GW-HQ** | `eth1` | WAN1-HQ | `10.0.2.1/24` | GW: `10.0.2.254` (Default) |
| | `eth0` | WAN2-HQ | `10.0.3.1/24` | Backup WAN |
| | `eth2` | MGMT | `192.168.2.254/24` | Security MGMT Zone |
| | `eth3` | SRV | `192.168.3.254/24` | Servers Zone |
| | `eth4` | USR-HQ | `192.168.4.254/24` | Users Zone |
| | `eth5` | DMZ | `192.168.5.254/24` | DMZ Zone |
| | `vpnt1` | - | `172.16.1.1/32` | IPsec VTI Tunnel Interface |
| | `loop00` | - | `10.255.255.1/24` | Loopback Interface |
| **CP-SMS-HQ** | `eth0` | MGMT | `192.168.2.1/24` | Check Point Security Management |
| **Jumphost** | `eth0` | MGMT | `192.168.2.252/24` | Admin Workstation |
| **DC** | `eth0` | SRV | `192.168.3.1/24` | Domain Controller |
| **Linux-HQ_1** | `eth0` | USR-HQ | `192.168.4.1/24` | User PC 1 |
| **Linux-HQ_2** | `eth0` | USR-HQ | `192.168.4.2/24` | User PC 2 |
| **WEB-Server** | `eth0` | DMZ | `192.168.5.1/24` | Web Application Server |
| **Mail-Server** | `eth0` | DMZ | `192.168.5.2/24` | Mail Application Server |
| **Kali-Linux** | `eth0` | WAN-Kali | `10.0.4.1/24` | External Security Auditor |
| **CP-GW-BR1** | `eth0` | WAN1-BR1 | `10.1.2.1/24` | GW: `10.1.2.254` (Default) |
| | `eth1` | USR-BR1 | `192.168.12.254/24` | Branch 1 Users Zone |
| | `vpnt1` | - | `172.16.1.2/32` | IPsec VTI Tunnel Interface |
| | `loop00` | - | `10.255.255.2/24` | Loopback Interface |
| **Linux-BR1** | `eth0` | USR-BR1 | `192.168.12.1/24` | Branch 1 User PC |
| **MK-ISP-BR2** | `eth1` | WAN | `192.168.123.202/24` | Link to Cloud (pnet1) [Future] |
| | `eth2` | WAN-BR2 | `10.2.2.254/24` | WAN for BR2 [Future] |
| **PFS-GW-BR2** | `eth0` | WAN-BR2 | `10.2.2.1/24` | pfSense Gateway [Future] |
| | `eth1` | USR-BR2 | `192.168.120.254/24` | Branch 2 Users Zone [Future] |
| **Linux-BR2** | `eth0` | USR-BR2 | `192.168.120.1/24` | Branch 2 User PC [Future] |

