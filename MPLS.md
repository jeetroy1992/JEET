```java
1️⃣ Device → Role Mapping (Very Clear)
🟦 Customer WAN Device (FW / Router)
Role: CE (Customer Edge)
• Owned by customer
• Runs static routes or BGP
• No MPLS, no VRF
• Sees only your CWAN router as next hop
⸻
🟦 FDB (Fiber Distribution Box)
Role: Physical demarcation
• No L2 / L3
• Patch-only
• Customer responsibility ends here
⸻
🟦 CWAN Switch (Primary & Secondary)
Role: L2 Access / Aggregation
• Dedicated access VLAN 2xxx per customer
• No routing
• No VRF
• No MPLS
• Connects customer → CWAN routers
⸻
🟦 CWAN Routers (Primary & Secondary)
Role: CE-facing L3 Gateway (Provider-owned)
• First L3 hop for customer
• Port-Channel toward CWAN switches
• Sub-interface per customer (Po10.2003)
• VRF CUSTOMER_0003 exists here
• Static or eBGP with customer
• NOT MPLS core
👉 Think of this as “L3 demarcation router”
⸻
🟦 MLAG Border Leaf 03a / 03b
Role: L2 / L3 Transport
• Carries VLAN 2003
• Provides redundancy (MLAG)
• No VRF
• No customer routing
• No MPLS labels
⸻
🟦 hec04-core-01a
Role: Ingress PE + LSR
• Terminates VLAN 2003
• Has VRF CUSTOMER_0003
• Learns customer routes
• Assigns VPN label
• Pushes MPLS transport label
• Runs MP-BGP vpnv4 (AFI/SAFI 1/128)
👉 MPLS L3VPN starts here
⸻
🟦 hec04-idc-01a
Role: P Router / Transit LSR
• Pure MPLS core
• Runs OSPF + LDP
• Swaps transport labels only
• No VRF
• No customer routes
⸻
🟦 Po10.920 (BGP Transit)
Role: Inter-domain IP handoff
• MPLS labels already removed
• Plain IP forwarding
• Connects toward next DC block
⸻
🟦 MLAG Border Leaf 01a / 01b
Role: Transport toward DC fabric
• No VRF
• No MPLS
• Hands traffic into EVPN/VXLAN domain
⸻
🟦 rt-hec16-601
Role: Egress PE + Stitch PE
• Has customer VRF
• Converts:
• MPLS L3VPN (vpnv4) → EVPN
• Runs BGP EVPN (AFI/SAFI 25/70)
• Acts as inter-DC stitching point
⸻
🟦 hec16-ha-core-01a
Role: Leaf / Anycast Gateway
• Runs EVPN/VXLAN
• Hosts:
• L2VNI (VLAN 2051)
• L3VNI (VRF routing)
• Default gateway for host
⸻
🟦 Host (10.120.0.2)
Role: End system
• No idea MPLS / EVPN exists
• Just uses default gateway
⸻
2️⃣ COMPLETE END-TO-END SUMMARY (PRINT THIS)
Customer traffic enters the DC through dual customer WAN devices connected via FDB to redundant CWAN switches using a dedicated access VLAN (2xxx). CWAN switches forward traffic at Layer-2 to primary and secondary CWAN routers over a port-channel, where per-customer sub-interfaces (Po10.2xxx) are configured with a VRF and static or BGP routing. Traffic is then carried transparently across MLAG border leafs to the core PE router, where MPLS L3VPN begins: the PE assigns VPN and transport labels using MP-BGP vpnv4 (AFI/SAFI 1/128). Transit IDC routers act as P routers, swapping MPLS labels via OSPF and LDP without any customer awareness. At the inter-DC stitch router, MPLS L3VPN routes are converted into EVPN routes using BGP EVPN (AFI/SAFI 25/70), and traffic enters the VXLAN fabric. Inside the fabric, leaf switches provide Anycast gateway functionality using L2VNI and L3VNI, delivering traffic to the final host. This entire design is a provider-managed MPLS Layer-3 VPN with CWAN access and EVPN inter-DC stitching, not a Direct Connect.
⸻
🔑 Final One-Line Truth (Memorize This)
CWAN routers terminate customer L3, core PE creates MPLS VPN, P routers swap labels, stitch PE converts MPLS to EVPN, and DC leaf delivers traffic using VXLAN.
```
