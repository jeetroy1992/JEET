## Summary
This document provides the complete reference architecture and a concrete runbook for SAP(IaaS) Cloud Peering service connectivity

# Cloud Peering
The cloud pairing is a simple plug & play service which is equivalent to the the direct connect from AWS or maybe express route from Azure or maybe the Google Cloud interconnect from GCP. Example: interconnection ecosystem providers like Equinix and MegaPort.

SAP has cloud peering connection which doesn’t require any physical link of the cable or devices. If customer has any internet connectivity from any Internet Service Provider i.e Equinix, Megaport, Verizon, etc… SAP would be able to connect virtually and provide cloud peering connection.

The difference between MPLS and cloud peering is that both provide private or dedicated connectivity solutions for customers. However, the key difference is that with MPLS, the customer can bring their own device and connect the circuit end‑to‑end directly with the service provider, while cloud peering does not involve customer‑managed end‑to‑end circuits in the same way.

Goal:
On-prem network  ⇄  SAP HEC application subnet

# Architecture Model

There is a 3‑Domain Model:

### (1) Data Center Fabric (EVPN/VXLAN)
- Hosts workloads (HEC/S4/HANA)  
- Uses Anycast Gateway (10.x.x.1)  
- EVPN Type‑5 advertises SAP subnet prefixes  
- Fast east‑west movement inside DC  

### (2) WAN/VPN Edge (CWAN)
- Terminates customer Cloud Peering  
- VRF CUSTOMER_0XXX  
- Converts EVPN Type‑5 → VPNv4  
- Attaches PRIO communities  
- Adds Local‑Pref or AS‑Path prepend  

### (3) SAP ONE Backbone (MPLS Transport)
- Pure transport layer (PE → P → PE)  
- No routing policy  
- No VRF decisions in P nodes  
- Moves VRF traffic using labels 

```mermaid
%% Mermaid table-like grid
flowchart TB
    classDef header fill:#e0e0e0,stroke:#000,font-weight:bold;

    SL1["1"]:::header --> D1["DC (EVPN)"] --> R1["Compute + Tenant Isolation + Anycast GW"]
    SL2["2"]:::header --> D2["cwan-01b"] --> R2["All Routing Logic + VRF Policies"]
    SL3["3"]:::header --> D3["MPLS Core (SOB)"] --> R3["High‑speed Label Transport Only"]

```

# Architecture Overview
```mermaid

flowchart LR

%% ===========================
%% Global Styling for Big Diagram
%% ===========================
classDef big stroke-width:4px, font-size:22px, padding:25px;

classDef customer fill:#4DA3FF,stroke:#005B99,color:#fff,stroke-width:4px,font-size:22px,padding:25px;
classDef telco fill:#A6A6A6,stroke:#4D4D4D,color:#fff,stroke-width:4px,font-size:22px,padding:25px;
classDef interco fill:#B3E6FF,stroke:#0099CC,color:#000,stroke-width:4px,font-size:22px,padding:25px;
classDef qinq fill:#FFD480,stroke:#CC8E00,color:#000,stroke-width:4px,font-size:22px,padding:25px;
classDef sap fill:#F28585,stroke:#993333,color:#000,stroke-width:4px,font-size:22px,padding:25px;
classDef cwan fill:#D5B3FF,stroke:#6A4D99,color:#000,stroke-width:4px,font-size:22px,padding:25px;
classDef core fill:#8FD9A8,stroke:#3F8F5E,color:#000,stroke-width:4px,font-size:22px,padding:25px;

linkStyle default stroke-width:4px;

%% ================================================================
%% VLAN 100 → 401   AND   VLAN 200 → 501  (Horizontal view)
%% ================================================================

%% LEFT: CUSTOMER
H1[CUSTOMER HOST
C‑VLAN 100]:::customer
H2[CUSTOMER HOST
C‑VLAN 200]:::customer

H1 --> CE1[CE1
Customer Edge]:::customer
H2 --> CE2[CE2
Customer Edge]:::customer

%% TELCO
CE1 --> DT1[Deutsche Telekom_PE1
L2/L3 Handoff]:::telco
CE2 --> DT2[Deutsche Telekom_PE2
L2/L3 Handoff]:::telco

%% INTERCONNECT Dual-Homed
DT1 --> EQ1[Equinix_PE1
Fabric Port‑1]:::interco
DT2 --> EQ2[Equinix_PE2
Fabric Port‑2]:::interco

%% CROSS-CONNECT — Dual active paths


%% QINQ Mapping
EQ1 --> Q1["Bundled QinQ Service
C‑VLANs 100/200
→
S‑TAGs 401/501"]:::qinq
EQ2 --> Q1

%% SAP ONE BACKBONE
Q1 --> SOB["SAP ONE BACKBONE
Ingress/Egress PEs
PE1a/P1a/PE1b
PE2a/P1b/PE2b

**MPLS VPN Label → CUSTOMER_0XXX**"]:::sap

%% CWAN SWITCHES
SOB --> SW1["CWAN_SW‑1"]:::cwan
SOB --> SW2["CWAN_SW‑2"]:::cwan

%% CWAN ROUTERS
SW1 --> RT1["CWAN_RT‑1
VRF CUSTOMER_0XXX
EVPN→VPNv4"]:::cwan
SW2 --> RT2["CWAN_RT‑2
VRF CUSTOMER_0XXX
EVPN→VPNv4"]:::cwan

%% BORDER ROUTERS
RT1 --> BR1[BRDR‑01a]:::sap
RT2 --> BR2[BRDR‑01b]:::sap

%% SAP FABRIC Common
BR1 --> SP[SAP SPINES]:::core
BR2 --> SP

SP --> CR[DC CORE]:::core
CR --> FAB[FABRIC VXLAN‑EVPN
Type‑5 Routes]:::core

FAB --> VIP["LB‑VIP"]:::sap
VIP --> APP["APP‑VM"]:::core

```
Before any config, SAP must know:
# Required WAN Questionnaire in Service Request

Before any configuration, SAP must know:

- **Customer ID (CID):** XYZ  
- **Customer Name:** XYZ COMPANY  
- **Target HEC DC:** HEC07  
- **Customer ASN:** 65010  
- **Bandwidth:** 200 Mbps  

## Primary Peering Subnet (/30 or /31)
- **Network:** 10.21.52.12/30  
- **SAP IP:** 10.21.52.13  
- **Customer IP:** 10.21.52.14

## Secondary Peering Subnet (/30 or /31)
- **Network:** 10.21.52.16/30  
- **SAP IP:** 10.21.52.17  
- **Customer IP:** 10.21.52.18

**Optional:** BGP MD5 password

## In High Availability Cloud Peering always lands on two CWAN routers (Primary & Secondary) & CWAN switches (Primary & Secondary), which may be Cisco ASR or Arista WAN devices depending on the DC build. The CWAN vendor is independent of whether the DC core is Cisco legacy (HEC 1.0) or Arista HA‑CORE (HEC 2.0)

## Why stitching is required (or not):
“HEC 2.0 (Arista HA‑CORE) requires route‑target stitching on Cisco CWAN because HA‑CORE advertises EVPN Type‑5 while the WAN uses VPNv4; HEC 1.0 (Cisco DC) speaks VPNv4 end‑to‑end so stitching is not needed.” 

## When to add the ip extcommunity PRIO tags (Cisco CWAN + Arista HA‑CORE):
“When the DC side is Arista HA‑CORE and CWAN is Cisco, tag exported service routes on the Cisco CWAN with PRIO communities using ip extcommunity‑list (e.g., CL‑EVPN‑PRIO1..4) and match them in the EVPN AF outbound route‑map—so HA‑CORE (which may not map Local‑Pref) still prefers PRIO1 via shortest AS‑Path.”

## Examples for RT to ext-community mapping

The following table shows an example of which extended community list route‑target
should be added for a specific customer. In this example:

- **Customer_0011** (RT **10:3011**) has primary way via **CWAN** and backup via **VPN**  
- **Customer_0012** (RT **10:3012**) has primary way via **VPN** and no CWAN connection yet  
- **Customer_0013** (RT **10:3013**) has primary way via **VPN** and secondary via **CWAN**  
  (useful during maintenance)


| **CWAN Router / Extended Community List** | **CL‑EVPN‑PRIO1**              | **CL‑EVPN‑PRIO2**              | **CL‑EVPN‑PRIO3**               | **CL‑EVPN‑PRIO4**               |
|-------------------------------------------|--------------------------------|--------------------------------|----------------------------------|----------------------------------|
| **CWAN 01a router**                       | <span style="color:#c47f00;">10:3011</span> |                                | <span style="color:#3a8f3a;">10:3013</span>  |                                  |
| **CWAN 01b router**                       |                                | <span style="color:#c47f00;">10:3011</span> |                                  | <span style="color:#3a8f3a;">10:3013</span>  |
| **VPN 01a router**                        | <span style="color:#3a8f3a;">10:3013</span> |                                | <span style="color:#c47f00;">10:3011</span><br/><span style="color:#bb3377;">10:3012</span> |                                  |
| **VPN 01b router**                        |                                | <span style="color:#3a8f3a;">10:3013</span> |                                  | <span style="color:#c47f00;">10:3011</span><br/><span style="color:#bb3377;">10:3012</span> |

%% =========================
%%  VLAN Reservation Logic
%% =========================

Each DC has fixed VLAN blocks.
Example:
Primary VLAN pool:   400–499
Secondary VLAN pool: 500–599
Engineer checks free VLANs.
Example allocation:
Primary VLAN:   401
Secondary VLAN: 501

Meaning:
401 → Primary BGP link
501 → Secondary BGP link
________________________________________


# High‑level workflow

```mermaid
sequenceDiagram
    participant SAP as SAP (Port Owner)
    participant CWAN as CWAN Router-Swicthes (SAP Side)
    participant EQ as Equinix Fabric
    participant CUST as Customer

    SAP->>EQ: Create Z-side service token (Primary)
    SAP->>EQ: Create Z-side service token (Secondary)
    SAP->>CUST: Share tokens

    CUST->>EQ: Redeem tokens
    EQ->>CUST: Build L2 virtual circuits

    CUST->>CWAN: Establish eBGP session (Primary/Secondary)
    CWAN->>CUST: Advertise SAP subnet
    CUST->>CWAN: Advertise customer prefixes

```

