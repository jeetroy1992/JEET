```java

✅ Core VIP = x.x.x.1
✅ CGS floating IP = x.x.x.254
✅ Load Balancer VIP = x.x.x.254
✅ Unique SNAT per customer
✅ Forward proxy + Reverse proxy
✅ VPN / MPLS / Cloud Peering
Everything explained clearly with ASCII + examples.
⸻
🌍 1. What is SAP HEC (big picture)
👉 SAP HANA Enterprise Cloud = SAP managed private cloud.
Customer SAP systems run inside SAP data center.
Customer connects using:
1️⃣ IPSEC VPN
2️⃣ MPLS private circuit
3️⃣ Cloud peering via Equinix
So customer apps are NOT on internet directly — they live inside SAP controlled network.
⸻
🧱 2. Full Network Layers (top to bottom)
We build architecture layer by layer.
Customer Users / Office
       │
Connectivity (VPN / MPLS / Peering)
       │
SAP HEC Core Router (VIP x.x.x.1)
       │
Customer Gateway Server (CGS) (VIP x.x.x.254)
       │
F5 Load Balancer (VIP x.x.x.254)
       │
SAP Web Dispatcher
       │
SAP Application Servers
Each layer has a specific job.
⸻
🧭 3. Each Component Explained (simple + real meaning)
⸻
🟦 A. Customer Network
Your office, data center, or cloud.
Users connect to SAP via:
• VPN tunnel
• MPLS circuit
• Equinix private peering
Nothing special here.
⸻
🟦 B. SAP HEC Core Router
👉 Default gateway of customer systems inside HEC.
Core VIP = x.x.x.1 (floating HA IP)
Meaning:
• active / standby routers share one IP
• if router fails → VIP moves
Purpose:
✔ routing between networks
✔ entry point from VPN/MPLS
✔ default gateway for SAP VMs
⸻
Example
SAP server IP = 10.10.10.100
Default route:
0.0.0.0 → x.x.x.1
Traffic always goes to core first.
⸻
🟦 C. Customer Gateway Server (CGS)
This is internet control point for customer environment.
CGS floating IP = x.x.x.254
Runs proxy like:
👉 Squid
Purpose:
✔ outbound internet control
✔ web filtering
✔ proxy enforcement
✔ logging
Think of CGS = internet security guard.
⸻
🟦 D. Load Balancer (F5)
Enterprise load balancer from:
👉 F5 Networks
LB VIP = x.x.x.254
Used in TWO different ways:
1️⃣ outbound SNAT gateway
2️⃣ inbound application access
⸻
🟦 E. SAP Application Reverse Proxy
👉 SAP Web Dispatcher
Purpose:
✔ SAP URL routing
✔ SAP server load balancing
✔ SSL termination
✔ hide backend
Used only when user connects to SAP.
⸻
🔁 4. Same VIP x.x.x.254 — Why repeated?
Different network segments.
Example:
Layer VIP
CGS x.x.x.254
Load balancer x.x.x.254
Different VLAN / subnet / zone.
Just HA design pattern.
⸻
🌐 5. Proxy Types (critical concept)
⸻
🔵 Forward Proxy (outbound internet)
Client wants to go OUT.
Proxy sits near client.
Example:
SAP system → Google API
ASCII:
SAP VM → CGS Proxy → Internet
Used in HEC outbound traffic.
⸻
🔴 Reverse Proxy (inbound application)
User wants to access server.
Proxy sits near server.
ASCII:
User → Load Balancer → Web Dispatcher → SAP
Used in HEC inbound access.
⸻
🔄 6. SNAT (Source NAT)
When SAP VM goes internet:
Private IP cannot be seen on internet.
So load balancer replaces source IP.
⸻
Before SNAT
Source: 10.10.10.100
Dest: api.payments.com

⸻
After SNAT
Source: 52.10.5.100 (customer public SNAT IP)
Dest: api.payments.com
Each customer gets unique SNAT IP.
Why?
✔ traffic isolation
✔ security logging
✔ internet routing
⸻
🚦 7. Full Traffic Flows (Most Important)
⸻
🟢 FLOW 1 — SAP system → Internet (OUTBOUND)
Example:
SAP sending email or API call.
Step-by-step:
SAP VM (10.10.10.100)
       │
Default Gateway → Core VIP x.x.x.1
       │
CGS Proxy x.x.x.254
       │
F5 Load Balancer VIP x.x.x.254
       │   (SNAT happens here)
Internet Router
       │
Internet Service
This uses:
✔ forward proxy
✔ SNAT
⸻
🔴 FLOW 2 — User → SAP Application (INBOUND)
Example:
User opens Fiori URL.
User Browser
    │
Internet
    │
F5 Load Balancer VIP x.x.x.254
    │
SAP Web Dispatcher
    │
SAP Application Server
This uses:
✔ reverse proxy
✔ load balancing
⸻
🟡 FLOW 3 — Customer office → SAP private access
Example:
Internal integration.
Customer LAN
    │
VPN/MPLS/Equinix
    │
HEC Core VIP x.x.x.1
    │
SAP internal network
No proxy required.
⸻
🧠 8. Full Logical Architecture (Final ASCII)
CUSTOMER SIDE
Users / Apps
    │
VPN / MPLS / Cloud Peering
    │
---------------- SAP HEC ----------------
Core Router (VIP x.x.x.1)
    │
CGS Proxy (VIP x.x.x.254)  ← outbound internet control
    │
F5 Load Balancer (VIP x.x.x.254)
    │
SAP Web Dispatcher
    │
SAP Application Servers

⸻
🎯 9. Memory Shortcut (exam / interview)
Core = routing
CGS = forward proxy (internet control)
F5  = SNAT + load balancing
Web Dispatcher = SAP routing

⸻
🧪 10. Real Life Example
User logs into SAP Fiori:
Laptop → VPN → Core → F5 → Web Dispatcher → SAP
SAP calls payment API:
SAP → Core → CGS proxy → F5 SNAT → Internet

⸻
✅ 11. Final Concept (most important)
SAP HEC uses layered security:
✔ routing layer (core)
✔ proxy security layer (CGS)
✔ NAT & load balancing layer (F5)
✔ application routing layer (Web Dispatcher)
Multiple protection layers.
```
