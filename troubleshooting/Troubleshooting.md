# Troubleshooting & Bug Hunt

This section covers the real-world issues encountered during the lab deployment and the logic behind their resolution.

### 1. L3 Connectivity for SIC via ISP (Routing & NAT)
* **The Issue:** The Security Management Server (SMS) sits at `192.168.2.1` behind the HQ gateway. The Branch gateway is external (`10.0.3.0/24`). The ISP routers in the lab only knew about the public `10.0.0.0/24` pool and had no routes to internal `192.168.x.x` subnets.
* **The Mistake:** I initially tried to route SMS traffic using Hide NAT on the HQ gateway. Secure Internal Communication (SIC) established at first, but this created a routing black hole that ruined future policy installations.

### 2. Policy Install Failure on TCP 18191 (Management IP vs NAT)
* **The Issue:** After initial SIC establishment, the Branch gateway downloads the object database from SmartConsole. It sees the real SMS object with its private IP (`192.168.2.1`).
* **The Consequence:** When pushing a policy (Install Policy), the Branch gateway tried to connect directly back to `192.168.2.1:18191` instead of the NAT IP. Since there’s no route through the ISP, the connection timed out, causing SIC to drop entirely.

### 3. Automatic NAT / Proxy ARP Conflict with IPsec Phase 2
* **The Issue:** Attempting to fix the routing mess using Automatic NAT and Proxy ARP completely broke the IPsec logic.
* **The Consequence:** IPsec Phase 1 and Phase 2 would establish successfully, but the Branch gateway would immediately tear down the tunnel. In a Route-Based VPN (VTI), the traffic selectors must be strictly `0.0.0.0/0 <-> 0.0.0.0/0`. The Proxy ARP and Auto-NAT confused the Check Point `vpnk` engine, creating false endpoints.

### 4. `fw unloadlocal` Loop and Cache Desync (`fwobj-get-myown` failed)
* **The Issue:** Because policy installations kept hanging, I had to reset the local policy via `fw unloadlocal` hundreds of times.
* **The Consequence:** The local object database and IKE/IPsec state cache on `CP-GW-BR1` got completely corrupted. The gateway threw a critical `VPN-IKE-fwobj-get-myown failed` error, meaning it couldn't even read its own parameters from the local DB.
* **The Fix:** Total wipe. I deleted the `CP-GW-BR1` node in EVE-NG and rebuilt the configuration from scratch.

### 5. OSPF Multicast (224.0.0.5) Blocked by Security Policy
* **The Issue:** The final hurdle. In the Access Control policy, the Destination for the OSPF rule was explicitly set to the neighbor's VTI host IP.
* **The Consequence:** OSPF Hello packets use the multicast address `224.0.0.5`. Check Point’s firewall engine naturally dropped these packets at the Cleanup Rule because they didn't match the specific host destination.
* **The Fix:** Changed the Destination to `Any` in the OSPF/ICMP rule. OSPF instantly transitioned to FULL state, and the routing tables populated properly.
