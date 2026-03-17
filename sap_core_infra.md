
# SAP HEC
SAP HEC (HANA Enterprise Cloud) is SAP's private managed hosting platform. Unlike AWS or Azure (shared public
cloud), SAP HEC gives each customer a completely dedicated and isolated network slice. Every customer gets their
own VRF (Virtual Routing and Forwarding) on the HA-Core router. A VRF is a completely separate routing table inside
one physical device — two customers on the same HA-Core are completely invisible to each other. This is the
foundation of SAP HEC multi-tenancy.

Below is the example of CID: PMS

| Component           | Key IP          | Role / Notes                                      |
|---------------------|-----------------|---------------------------------------------------|
| Customer VM eth2    | 10.67.247.11    | CID-PMS Main interface — all routable traffic            |
| Customer VM eth1    | 100.64.122.15   | CID-PMS Storage only — L2, no routing                    |
| HA-Core VGW         | 10.67.247.1     | VARP shared GW — all 4 HA-Cores respond          |
| HA-Core 01a         | 10.67.247.2     | CID-PMS example — unique SVI IP, VARP VIP .1         |
| HA-Core 01b         | 10.67.247.3     | CID-PMS example — unique SVI IP, VARP VIP .1         |
| HA-Core 01c         | 10.67.247.4     | CID-PMS example — unique SVI IP, VARP VIP .1         |
| HA-Core 01d         | 10.67.247.5     | CID-PMS example — unique SVI IP, VARP VIP .1         |
| CGS VIP             | 10.67.247.254   | CID-PMS CGS Floating IP          |
| CGS-A (ex.Active)   | 10.67.247.253   | CID-PMS Processes all live traffic                       |
| CGS-B (ex.Standby)  | 10.67.247.252   | CID-PMS Hot spare — CGS-A failure takeover               |
| F5 VIP              | 10.67.247.249   | CID-PMS Floating internet LB IP                          |
| F5 SELF IP          | 10.67.247.250   | CID-PMS Self f5 LB IP                          |
| F5 SELF IP          | 10.67.247.251   | CID-PMS Self f5 LB IP                          |
| DNS HOST/ SNAT      | 157.133.120.173 | CID-PMS HEC01-NW-INTERNET
| CGS INFRA iface     | 198.18.27.146   | CID-PMS CGS eth0 — bridge into VRF INFRA                 |                               |
| HA-Core VLAN60      | 198.18.24.1     |  INFRA VRF gateway + SNAT point  |
| HA-Core VLAN914     | 198.19.252.34   | Dedicated Checkpoint FW uplink  |

## The Five Key Components
### Component 1 — Customer VM
The actual SAP workload server. Has TWO network interfaces with completely different purposes:
• eth2 (Customer VLAN 10.67.247.0/24): All routable traffic — internet, on-prem, infra. Default gateway is HA-Core
VGW X.X.X.1 .This is where all application traffic flows.

• eth1 (Storage VLAN 100.64.122.0/24): — packets cannot be routed

### Component 2 — HA-Core (High Availability Core Router/Switch)
The HA-Core is the L3 backbone and the SINGLE routing policy enforcement point in SAP HEC. It is an Arista switch
cluster — four nodes (01a X.X.X.2, 01b X.X.X.3, 01c X.X.X.4, 01d X.X.X.5) connected to 4 Arista Spine(11,12,13,14) switches. all sharing VIP .1 via VARP. 
Every routing decision goes through HA-Core. It runs separate VRF instances per customer (VRF CUSTOMER_0004 for
PMS) plus the shared INFRA VRF for SAP management traffic. 

👉 Think of HA‑Core as: A 4‑lane traffic control center. Always available, always routing

### Component 3 — CGS (Customer Gateway Server)
The CGS is a virtual Intel Xeon Linux server that is uniquely multi-homed across THREE network domains.
CGS runs Active/Standby (CGS-A and CGS-B) with a shared VIP (.254). All routing tables — both in VMs and in
HA-Core — always point to .254, making failover completely transparent and requiring zero reconfiguration.

Customer network (eth2)
SAP Infra network (eth0)
Storage network (eth1)

👉 Think of CGS as: A triple‑door gateway that connects all isolated networks safely

### Component 4 — F5 BIG-IP
The F5 handles ALL internet traffic and is the SINGLE SNAT point. Each customer gets a dedicated SNAT IP pool —
PMS traffic always appears from PMS's own public IP, never shared with other customers. F5 runs Active/Standby with
a floating VIP (.249). Failover is transparent to VMs.

### Component 5 — VPN / CWAN Routers
Connect SAP HEC to the customer's on-premises network. Two redundant paths: VPN Routers using IPSec tunnels
over internet (standard, flexible), and CWAN Routers using dedicated MPLS or cloud peering circuits (guaranteed SLA,
lower latency, higher cost). Both paths are used simultaneously for redundancy.

# 📘 Service & Application Ports Reference

## 🔐 Standard Service Ports

| Service / Protocol | Port | Transport | Meaning |
|--------------------|-------|-----------|---------|
| **SSH** | 22 | TCP | Remote secure shell login (Linux). Used for secure server access. |
| **RDP** | 3389 | TCP | Windows Remote Desktop Protocol for GUI login. |
| **ICMP (Ping)** | N/A | ICMP | No port number; uses ICMP Echo (8) & Reply (0). |
| **FTP** | 21 (control), 20 (data) | TCP | Old file transfer protocol (not encrypted). |
| **SFTP** | 22 | TCP | Secure file transfer (runs over SSH). |
| **SMTP** | 25 | TCP | Mail transfer (Postfix, Sendmail). |
| **SMTP Auth** | 587 | TCP | Secure SMTP submissions with authentication. |
| **SMTPS** | 465 | TCP | SMTP over SSL/TLS. |
| **DNS Query** | 53 | UDP | DNS lookup queries. |
| **DNS Zone Transfer** | 53 | TCP | Secondary DNS servers syncing zone data. |
| **SNMP** | 161 | UDP | Monitoring/polling (Get/Set). |
| **SNMP Trap** | 162 | UDP | Alerts from devices (traps/informs). |

---

## ✔ Custom Application Ports
*(Examples – varies by application)*

| Application | Port | Protocol | Meaning |
|-------------|-------|----------|----------|
| **App Server** | 18081 | TCP | Web/REST testing port. |
| **App Server** | 29000 | TCP | API listener. |
| **App Server** | 31234 | TCP/UDP | Custom application listener. |
