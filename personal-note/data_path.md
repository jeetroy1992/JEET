```java
In SAP HEC there are 3 different responsibilities:
1️⃣ Move packets (routing)
2️⃣ Control / distribute connections (load balancing)
3️⃣ Act as proxy (application level handling)
Different devices do different jobs.
⸻
🔵 1. CORE – Only Routing (NOT proxy)
Core router job:
• L3 routing
• VRF separation
• MPLS transport
• Default gateway (x.x.x.1)
It forwards based on:
Destination IP
It does NOT:
• inspect HTTP
• cache
• load balance
• understand SAP
ASCII:
Packet → CORE → Next hop
Core = Highway
⸻
🟢 2. F5 Load Balancer – Connection Distributor + SNAT
Device from:
👉 F5 Networks
What F5 does:
✔ Hosts VIP (virtual IP)
✔ Distributes traffic to multiple servers
✔ Health check
✔ SSL offload
✔ SNAT (unique public IP per customer)
It balances connections.
Example:
User → VIP → F5 → App1
                → App2
It does NOT understand SAP system logic.
It just balances TCP/HTTP sessions.
⸻
🔴 3. SAP Web Dispatcher – Reverse Proxy (SAP Aware)
Software:
👉 SAP Web Dispatcher
Type of proxy:
👉 Reverse Proxy
Used for:
User coming FROM outside to SAP.
Job:
✔ SAP load balancing
✔ Reads SAP Message Server
✔ Session stickiness
✔ Routes to correct SAP instance
ASCII:
User
 |
 v
Web Dispatcher
 |----> SAP App1
 |----> SAP App2
It understands:
• SAP SID
• SAP logon groups
• SAP instance health
This is SAP-specific L7 routing.
⸻
🟡 4. Squid Proxy – Forward Proxy (Internet Control)
Software:
👉 Squid
Type of proxy:
👉 Forward Proxy
Used for:
When SAP server goes OUT to internet.
Job:
✔ Allow / deny websites
✔ URL filtering
✔ Caching
✔ Logging
ASCII:
SAP Server
    |
    v
Squid Proxy
    |
    v
Internet
It does NOT:
• Load balance SAP
• Know SAP sessions
⸻
🎯 BIG DIFFERENCE (Very Clear)
Component Proxy Type Used When Main Purpose
CORE ❌ None Always Routing
F5 LB ❌ (Not pure proxy) Inbound + Outbound Distribute traffic + SNAT
Web Dispatcher ✅ Reverse Proxy User → SAP SAP intelligent routing
Squid ✅ Forward Proxy SAP → Internet Internet filtering

⸻
🚀 Complete Picture (Both Directions)
                INBOUND (User Login)
User
 |
 v
F5 Load Balancer (VIP)
 |
 v
Web Dispatcher (Reverse Proxy)
 |
 v
SAP Application Servers

                OUTBOUND (Internet Access)
SAP Server
  |
  v
CORE (Gateway)
  |
  v
Squid Proxy (Forward Proxy)
  |
  v
F5 (SNAT)
  |
  v
Internet

⸻
🧠 Memory Trick (Simple)
• Core = Road
• F5 = Traffic distributor + IP changer
• Web Dispatcher = SAP traffic manager
• Squid = Internet security guard
⸻
🔥 Very Important Final Clarification
• Web Dispatcher and Squid are BOTH proxies
• But different type of proxy
• F5 is mainly Load Balancer + NAT device
• Core is only router
They do NOT compete.
They work at different layers.
```
