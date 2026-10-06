# Check Point Gateways Configuration

## CP-GW-HQ

### 1. Network & Routing (Gaia Clish)
```bash
# Interface IP allocation
set interface eth0 ipv4-address 10.0.3.1 subnet-mask 255.255.255.0
set interface eth1 ipv4-address 10.0.2.1 subnet-mask 255.255.255.0
set interface eth2 ipv4-address 192.168.2.254 subnet-mask 255.255.255.0
set interface eth3 ipv4-address 192.168.3.254 subnet-mask 255.255.255.0
set interface eth4 ipv4-address 192.168.4.254 subnet-mask 255.255.255.0
set interface eth5 ipv4-address 192.168.5.254 subnet-mask 255.255.255.0

# Loopback & VTI
set interface loop00 ipv4-address 10.255.255.1 subnet-mask 255.255.255.0
add vpntunnel vpnt1 id 1 local 172.16.1.1 remote 172.16.1.2 peer CP-GW-BR1

# Default Gateway & Static Routes
set static-route default nexthop gateway address 10.0.2.254 priority 1 on
set static-route default nexthop gateway address 10.0.3.254 priority 2 off
