# Cisco ISP Routers Configuration

## CS-ISP-HQ

### 1. Interface & Network Setup
* **Gi0/0** (WAN2-HQ): `10.0.3.254/24` — NAT Inside
* **Gi0/1** (WAN / pnet1): `192.168.123.200/24` — NAT Outside (Upstream Cloud)
* **Gi0/2** (WAN1-HQ): `10.0.2.254/24` — NAT Inside
* **Gi0/3** (WAN-Kali): `10.0.4.254/24` — NAT Inside

### 2. Configuration Commands
```cisco
! Interface NAT Assignments
interface GigabitEthernet0/0
 ip nat inside
!
interface GigabitEthernet0/1
 ip nat outside
!
interface GigabitEthernet0/2
 ip nat inside
!
interface GigabitEthernet0/3
 ip nat inside
!

! Access Control List for Dynamic NAT (Excludes internal inter-site traffic)
access-list 101 deny   ip 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255
access-list 101 permit ip 10.0.0.0 0.255.255.255 any

! Dynamic Overload NAT (PAT)
ip nat inside source list 101 interface GigabitEthernet0/1 overload

! Static Routing
ip route 0.0.0.0 0.0.0.0 192.168.123.1
ip route 10.0.0.0 255.255.0.0 10.0.2.1
ip route 10.1.0.0 255.255.0.0 192.168.123.201

CS-ISP-BR1
1. Interface & Network Setup

    Gi0/0 (WAN1-BR1): 10.1.2.254/24 — NAT Inside

    Gi0/1 (WAN / pnet1): 192.168.123.201/24 — NAT Outside (Upstream Cloud)

2. Configuration Commands
Cisco CLI

! Interface NAT Assignments
interface GigabitEthernet0/0
 ip nat inside
!
interface GigabitEthernet0/1
 ip nat outside
!

! Access Control List for Dynamic NAT
access-list 101 deny   ip 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255
access-list 101 permit ip 10.0.0.0 0.255.255.255 any

! Dynamic Overload NAT (PAT)
ip nat inside source list 101 interface GigabitEthernet0/1 overload

! Static Routing
ip route 0.0.0.0 0.0.0.0 192.168.123.1
ip route 10.0.0.0 255.255.0.0 192.168.123.200
ip route 10.1.0.0 255.255.0.0 10.1.2.1
