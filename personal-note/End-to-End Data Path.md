```java
We assume environment inside
👉 SAP HANA Enterprise Cloud
⸻
🧭 1. End-to-End Data Path — Packet Level
Let’s trace ONE real packet.
Example:
SAP VM → HTTPS call → api.partner.com
SAP VM IP = 10.10.10.100
Core VIP = 172.16.0.1
CGS proxy = 172.16.1.254
F5 SNAT IP = 52.100.5.10
⸻
🟢 STEP 1 — Packet leaves SAP VM
Ethernet frame:
L2:
 SRC MAC = SAP_VM_MAC
 DST MAC = CORE_ROUTER_MAC
L3:
 SRC IP = 10.10.10.100
 DST IP = 8.8.8.8
L4:
 TCP 443
Routing decision:
Default route → 172.16.0.1 (core VIP)

⸻
🟢 STEP 2 — Core router forwarding
Core does pure routing.
Changes:
✔ MAC rewrite
✔ TTL decrement
IP unchanged.
SRC IP = 10.10.10.100
DST IP = 8.8.8.8
NEXT HOP = CGS proxy
Policy routing may exist:
Example:
HTTP/HTTPS → CGS proxy
Non HTTP → F5 directly

⸻
🟢 STEP 3 — CGS proxy decision
CGS runs:
👉 Squid
Now application-layer inspection happens.
Decision logic:
Is destination allowed?
Is URL allowed?
Is customer permitted?
Is auth required?
Possible outcomes:
✔ ALLOW → forward to F5
✔ BLOCK → deny response
✔ CACHE HIT → serve locally
If HTTPS CONNECT tunnel:
CONNECT api.partner.com:443
CGS opens new TCP session toward F5.
⸻
🟢 STEP 4 — F5 SNAT processing
Device from:
👉 F5 Networks
Mode = IP forwarding virtual server.
Processing steps:
1 match virtual server
2 apply iRule (optional)
3 apply SNAT pool
4 forward to internet router
⸻
SNAT translation table (example)
CLIENT           PUBLIC
10.10.10.100  →  52.100.5.10
Port translation:
10.10.10.100:50432
→
52.100.5.10:32001
Connection table stored in TMM memory.
⸻
🟢 STEP 5 — Internet edge router
ACL enforcement.
Typical rule:
ALLOW src 52.100.5.0/24 dst ANY tcp 80,443
DENY everything else
This is last SAP controlled security layer.
⸻
🟢 STEP 6 — Internet server receives
Internet sees:
SRC = 52.100.5.10
DST = 8.8.8.8
Private SAP IP never visible.
⸻
🔵 RETURN TRAFFIC
Reverse order:
Internet → F5 → CGS → Core → SAP VM
F5 reverses SNAT using connection table.
⸻
🔴 2. Inbound SAP Application Access (Deep Path)
User opens:
https://fiori.customer.hec.sap

⸻
Step sequence
User
↓
Internet DNS resolves LB VIP
↓
F5 reverse proxy virtual server
↓
SAP Web Dispatcher
↓
SAP message server / app server

⸻
SSL offload possibilities
Option A — SSL terminated at F5
Option B — SSL pass-through
Option C — SSL terminated at Web Dispatcher
Most secure enterprise design:
SSL terminate at F5
Re-encrypt to Web Dispatcher

⸻
SAP routing logic
👉 SAP Web Dispatcher
Reads SAP message server table.
Example:
SID PRD
App1 10.10.20.10
App2 10.10.20.11
Load balancing algorithm:
• round robin
• weighted
• least load
⸻
🧠 3. Why CGS AND F5 both exist (architectural reason)
Many engineers ask this.
They solve different layers.
Layer Function
CGS L7 policy enforcement
F5 L3/L4 NAT & transport control
CGS = application security
F5 = network transport control
Separation of responsibility.
⸻
⚙️ 4. F5 Virtual Server Design (real config logic)
Conceptual structure:
Virtual Server
  │
  ├── Destination VIP
  ├── SNAT pool
  ├── iRule
  ├── VLAN binding
  └── Profile (TCP, HTTP etc)

⸻
Example SNAT pool
snatpool CUSTOMER_A_SNAT
{
 members:
   52.100.5.10
   52.100.5.11
}
Selection algorithm:
✔ round robin
✔ least connections
⸻
iRule example logic
if destination_port != 80 and != 443
 reject
Used for micro-policy enforcement.
⸻
🔐 5. Security Layering Model (enterprise principle)
SAP follows defense in depth.
Layer 1 — Network routing (core)
Layer 2 — Proxy filtering (CGS)
Layer 3 — NAT isolation (F5)
Layer 4 — Application routing (Web Dispatcher)
Layer 5 — SAP authentication
Multiple inspection points.
⸻
🔁 6. High Availability Behaviour
Every critical component is HA.
⸻
Core routers
Protocol:
VRRP / HSRP style floating VIP
Failure detection:
Hello timers
Failover < 3 sec typical.
⸻
CGS proxy cluster
Modes:
✔ active/standby
✔ load sharing
Session persistence via:
• source IP hash
• proxy state sync
⸻
F5 cluster
Uses:
Device service clustering
State mirroring
Connection table mirrored.
Failover impact:
✔ existing sessions survive
✔ SNAT mapping preserved
⸻
🚨 7. Failure Scenarios (real troubleshooting)
⸻
Case 1 — SAP VM cannot reach internet
Check order:
1 ping core VIP
2 proxy reachability
3 proxy logs
4 F5 SNAT table
5 internet router ACL
Most common cause:
❌ proxy policy block
❌ SNAT pool exhausted
⸻
Case 2 — Internet reachable but slow
Check:
CGS CPU
Proxy cache disk
TCP retransmissions
MTU mismatch

⸻
Case 3 — Inbound SAP login failing
Check:
DNS resolution
F5 virtual server up?
Web dispatcher backend status
SAP message server

⸻
🔬 8. Packet Capture Strategy (expert method)
Capture locations:
SAP VM → tcpdump
Core router → span port
CGS proxy → access log + tcpdump
F5 → tcpdump tmm
Internet edge → firewall logs
Compare source IP each hop.
⸻
🌐 9. Connectivity Options (external)
Customers connect via:
⸻
IPSEC VPN
Encrypted tunnel.
Pros:
✔ quick deployment
Cons:
❌ internet latency
⸻
MPLS private circuit
Service provider WAN.
Pros:
✔ predictable latency
✔ SLA
Cons:
❌ expensive
⸻
Cloud peering
Private interconnect via:
👉 Equinix
Used for hyperscaler connectivity.
⸻
🧠 10. Architecture Design Philosophy (real reason SAP built this)
SAP HEC serves many customers in shared data centers.
Must guarantee:
✔ tenant isolation
✔ regulatory compliance
✔ deterministic routing
✔ audit logging
✔ traffic attribution per customer
That’s why:
• unique SNAT per customer
• proxy enforcement
• layered routing
⸻
🧾 11. Ultimate Mental Model (architect view)
Connectivity brings traffic in
Core decides where to send
Proxy decides if allowed
F5 rewrites identity
Web Dispatcher finds SAP target
SAP processes business logic
That’s full lifecycle.
```
