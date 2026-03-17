
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
| CGS INFRA iface     | 198.18.27.146   | CID-PMS CGS eth0 — bridge into VRF INFRA                 |                               |
| HA-Core VLAN60      | 198.18.24.1     |  INFRA VRF gateway + SNAT point  |
| HA-Core VLAN914     | 198.19.252.34   | Dedicated Checkpoint FW uplink  |

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
