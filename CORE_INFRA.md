```java
✅ What is “VRF INFRA”? (Very simple meaning)
Think of VRF INFRA as a separate network world inside the switch.

Only Infra / Admin / Management VLANs live inside this VRF
Traffic inside this VRF never mixes with tenant/customer/production traffic
It has its own routing table
It has its own default route, its own BGP, own static routes, own interfaces
```
So:
👉 INFRA VRF = management world for servers, storage, backups, OOB, monitoring, DNS, etc.

✅ What networks are inside VRF INFRA?
From your routing table:
These VLANs belong to the management world

Vlan5 → 198.18.44.0/24 (IPMI / baseboard mgmt)
Vlan6 → 198.18.45.0/24
Vlan10 → IPMI01
Vlan20 → HV‑MGMT01
Vlan30 → IBM‑PWR‑MGMT01
Vlan40 → HANA‑MGMT01
Vlan60 → APP‑MGMT01
Vlan95 → DNS‑01
Vlan110 → STOR‑MGMT01
Vlan120 → BACKUP‑SYNC01
Vlan130 → STOR‑SYNC01
Vlan900 → NW‑MGMT
Vlan914 → Connection to Cisco/Firewall (core link)

These all represent different management networks for servers, storage, HANA, backup, etc.

✅ Default Route in VRF INFRA (Very Important)
You have this route:
0.0.0.0/0 → via 198.19.252.60, Vlan914

Meaning:
👉 To reach anything outside INFRA VRF (like central mgmt, SAP IT, DNS, NTP etc.) → send traffic to firewall via Vlan914.
Vlan914 = your north‑south exit for the management networks.

You can think:
All Mgmt VLANs → INFRA VRF → exit through Vlan914 → Core Firewall → WAN routers → SAP backbone
What are these BGP EVPN routes with 169.254.x.x and 192.168.201.x?
You saw:
B E 169.254.2.0/30 via VTEP 198.19.249.105 VNI 3024990

Explanation:

B E = BGP EVPN learned route
These are transit / routed VNI interfaces used for communication with WAN edge routers
VTEP 198.19.249.105 and 198.19.249.106 = your edge routers
Traffic goes over your VXLAN fabric (spine-leaf)

Very simple meaning:
👉 These management networks need to reach WAN – the path is:
Leaf Core → Spine → Leaf Edge → WAN Router
The EVPN routes are auto‑discovered because VXLAN is used for routing between Leafs.

✅ Static Routes in VRF INFRA
You have:
ip route vrf INFRA 198.19.224.112/28 via 198.19.241.154 Vlan900

This means:
👉 There is some management device/subnet reachable through another device inside Vlan900.
Probably for monitoring / backup / admin tools.

✅ What Prefix‑List PL‑INFRA Does
Prefix list:
100.68.0.0/15  
198.19.227.0/26  
198.19.227.64/26  
147.204.133.184/29  
198.19.241.0/24

This means:
👉 These networks are allowed to be advertised to BGP (to the WAN or central mgmt).
Everything else stays inside the DC.
So only these management subnets are exported upstream.

✅ BGP Neighbors 198.19.249.105 / 106
neighbor 198.19.249.105  → rt-hec02-cwan-02a  
neighbor 198.19.249.106  → rt-hec02-cwan-02b

Very simple:
👉 These two devices are WAN routers.
👉 Your Leaf Core sends the allowed mgmt prefixes to WAN through these routers.
Redundancy:

105 = A‑side WAN router
106 = B‑side WAN router

✅ Arista Leaf Core is doing this:
Holds all management VLANs in VRF INFRA
Sends mgmt traffic to WAN via Vlan914 → Firewall
Uses EVPN BGP to distribute mgmt networks across VXLAN fabric
Advertises only selected prefixes via prefix-list PL‑INFRA
Supports secure mgmt using SNMP, ACLs, Logging, sFlow
Provides full redundancy to WAN routers (105/106)

```
