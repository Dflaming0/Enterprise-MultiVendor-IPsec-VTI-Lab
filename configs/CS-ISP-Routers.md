CS - (CISCO)

CS-ISP-HQ
  NAT:
    ip nat in s list 101 int Gi0/1 ov
  ACL:
    Access-list 101 deny 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255
    Access-list 101 permit 10.0.0.0 0.255.255.255 any 
  Ip Route:
    ip route 0.0.0.0 0.0.0.0 192.168.123.1
    ip route 10.0.0.0 255.255.0.0 10.0.2.1
    ip route 10.1.0.0 255.255.0.0 192.168.123.201

CS-ISP-BR1
  NAT:
    ip nat in s list 101 int Gi0/1 ov
  ACL:
    Access-list 101 deny   ip 10.0.0.0 0.255.255.255 10.0.0.0 0.255.255.255
    Access-list 101 permit ip 10.0.0.0 0.255.255.255 any
  Ip Route:
    ip route 0.0.0.0 0.0.0.0 192.168.123.1
    ip route 10.0.0.0 255.255.0.0 192.168.123.200
    ip route 10.1.0.0 255.255.0.0 10.1.2.1
