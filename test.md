```flowchart
flowchart TD
   %% ─── CUSTOMER SITE ───
   subgraph CUST["🏢 CUSTOMER SITE (On-Premises)"]
       Browser["👤 User Browser\n192.168.10.50\nHTTPS → Fiori URL"]
       CustFW["🔧 Customer Router / Firewall\nCrypto ACL: 192.168.10.0/24 ↔ 10.67.247.0/24\nIKEv2 Phase-1 + Phase-2 → Tunnel UP"]
   end
   %% ─── INTERNET ───
   subgraph INET["🌐 INTERNET"]
       ESP["🔒 ESP Encrypted Tunnel\nIKEv2 / IPSec S2S"]
   end
   %% ─── HEC EDGE ───
   subgraph EDGE["⚙️ HEC EDGE"]
       ASR["Cisco ASR 1002-HX\nWAN: 203.0.113.10\nIPSec Decrypt HERE\nPlain pkt: src=192.168.10.50 dst=10.67.247.249\nDefault: 0.0.0.0/0 → 10.67.247.249 (F5 VIP)\nAlt:     0.0.0.0/0 → 10.67.247.1  (HA-Core)"]
   end
   %% ─── SPINE FABRIC ───
   subgraph SPINE["🔀 VXLAN-EVPN FABRIC — 4 Spine Switches (ECMP)"]
       SP1["Spine-01"]
       SP2["Spine-02"]
       SP3["Spine-03"]
       SP4["Spine-04"]
   end
   %% ─── HA-CORE ───
   subgraph HACORE["🖥️ HA-CORE CLUSTER — 4-Node Active/Active (VARP)"]
       VARP["VARP VIP: 10.67.247.1\nAll 4 nodes answer ARP simultaneously\nBGP EVPN Full Mesh — 6 sessions\nVXLAN VNI: 3010040 = VRF CUSTOMER_0004"]
       subgraph VRFCUST["VRF CUSTOMER_0004 (PMS) — VLAN 2004"]
           HC_CUST["SVIs: .2 / .3 / .4 / .5\nDefault → F5 VIP .249\nStatic RFC → CGS VIP .254"]
       end
       subgraph VRFINFRA["VRF INFRA"]
           VLAN60["VLAN 60 — 198.18.24.1/21\n▸ OUTBOUND: VM → Infra\n▸ SNAT HERE: VM IP → MGMT IP\n▸ CGS eth0 connects here"]
           VLAN914["VLAN 914 — 198.19.252.34/28\n▸ INBOUND: Infra → VM\n▸ Checkpoint FW uplink ONLY\n▸ ALL inbound infra enters here"]
       end
   end
   %% ─── CHECKPOINT FW ───
   CPFW["🛡️ Checkpoint Firewall\nMandatory inspection\nALL infra→VM traffic\nOnly path into VLAN914"]
   %% ─── F5 ───
   subgraph F5["⚖️ F5 BIG-IP LTM"]
       F5VIP["VIP: 10.67.247.249 (floating)\nActive:  lb-hec01-01 → .250\nStandby: lb-hec01-02 → .251\n① SSL Termination HTTPS→HTTP\n② SNAT → PMS dedicated public IP\n③ Load balance → Web Dispatcher"]
   end
   %% ─── CGS ───
   subgraph CGS["🔗 CGS CLUSTER — 3-Interface Bridge"]
       CGSVIP["VIP: 10.67.247.254\nCGS-A Active:  .253 (hec01v064827)\nCGS-B Standby: .252\neth2 → Customer VLAN 10.67.247.x\neth0 → INFRA VRF  198.18.27.146\neth1 → Storage L2  100.64.122.x"]
   end
   %% ─── SAP APP LAYER ───
   subgraph APP["🟢 SAP APPLICATION LAYER — VLAN 2004 / 10.67.247.0/24"]
       WD["SAP Web Dispatcher\n10.67.247.x\n▸ Receives HTTP from F5\n▸ URL /sap/bc/ui5_ui5/ → FIORI ✓\n▸ URL /sap/bc/gui/     → SAP GUI ✓\n▸ Checks Logon Group\n▸ Load balances → App Servers"]
       AS1["App Server 1\n10.67.247.11"]
       AS2["App Server 2\n10.67.247.12"]
       HANA["SAP HANA DB\n10.67.247.x"]
   end
   %% ─── INFRA SERVERS ───
   LTS["📡 Infra Server (hec-lts)\n147.204.100.17\nSNMP / License / Backup"]
   %% ─── STORAGE ───
   STOR["💾 Storage LAN\n100.64.122.0/24\nL2 ONLY — No Gateway\nNon-routable"]
   %% ─── FLOW CONNECTIONS ───
   Browser -->|"HTTPS request"| CustFW
   CustFW -->|"IKEv2/IPSec ESP tunnel"| ESP
   ESP -->|"Encrypted tunnel"| ASR
   ASR -->|"Plain IP → dst .249"| SP1 & SP2 & SP3 & SP4
   SP1 & SP2 & SP3 & SP4 -->|"ECMP underlay"| VARP
   VARP --> HC_CUST
   HC_CUST -->|"default → .249"| F5VIP
   F5VIP -->|"HTTP + load balance"| WD
   WD -->|"Logon Group"| AS1 & AS2
   AS1 & AS2 -->|"query"| HANA
   %% ─── INFRA OUTBOUND (VM → Infra) ───
   HC_CUST -->|"Infra routes → CGS .254"| CGSVIP
   CGSVIP -->|"eth0 → 198.18.24.1"| VLAN60
   VLAN60 -->|"SNAT + route"| LTS
   %% ─── INFRA INBOUND (Infra → VM) ───
   LTS -->|"infra fabric"| CPFW
   CPFW -->|"inspected packet\nVLAN914 only entry"| VLAN914
   VLAN914 -->|"INFRA VRF route\n→ VLAN60 → CGS eth0"| VLAN60
   VLAN60 -->|"CGS eth0 → eth2\n→ Customer VLAN"| CGSVIP
   %% ─── STORAGE ───
   CGSVIP -->|"eth1 — L2 only"| STOR
   %% ─── RETURN PATH ───
   HANA -->|"response"| AS1
   AS1 -->|"return"| WD
   WD -->|"return"| F5VIP
   F5VIP -->|"SNAT src = .250\nreturn to customer"| VARP
   VARP -->|"route → ASR"| ASR
   ASR -->|"re-encrypt ESP\ndst = 192.168.10.50"| ESP
   ESP -->|"tunnel"| CustFW
   CustFW -->|"deliver"| Browser
   %% ─── STYLES ───
   classDef customer fill:#1a3a5c,stroke:#4a9eff,color:#fff
   classDef internet fill:#2d2d2d,stroke:#888,color:#ccc
   classDef edge fill:#1a3a2a,stroke:#4aff88,color:#fff
   classDef spine fill:#2a1a3a,stroke:#aa66ff,color:#fff
   classDef hacore fill:#1a2a3a,stroke:#4a88ff,color:#fff
   classDef vrf fill:#0d1f2d,stroke:#2266aa,color:#add
   classDef f5 fill:#3a2000,stroke:#ffaa00,color:#fff
   classDef cgs fill:#1a2a1a,stroke:#44aa44,color:#fff
   classDef app fill:#1a1a3a,stroke:#6666ff,color:#fff
   classDef fw fill:#3a0000,stroke:#ff4444,color:#fff
   classDef infra fill:#2a2a1a,stroke:#aaaa44,color:#fff
   classDef storage fill:#1a1a1a,stroke:#666,color:#aaa
   class Browser,CustFW customer
   class ESP internet
   class ASR edge
   class SP1,SP2,SP3,SP4 spine
   class VARP,HC_CUST hacore
   class VLAN60,VLAN914 vrf
   class F5VIP f5
   class CGSVIP cgs
   class WD,AS1,AS2,HANA app
   class CPFW fw
   class LTS infra
   class STOR storage
```
