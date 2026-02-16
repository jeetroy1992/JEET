
<!-- =========================
     IKEv2 / IPsec Reference
     ========================= -->

<details>
<summary>🟦 Notes</summary>

<pre><code>HDR   = Internet Key Exchange (IKE) Header = Who I am / Message envelope
SK    = Encrypted and Integrity-Protected Payload inside HDR
IDi   = Identification – Initiator
AUTH  = Authentication Payload = Proof I know PSK
SAi2  = Security Association – Initiator (CHILD SA / Phase-2) = Data encryption rules
TSi   = Traffic Selector – Initiator = Which subnets to encrypt
TSr   = Traffic Selector – Responder = Which subnets to encrypt

IDi + AUTH → Trust built
Identity + successful authentication = verified peer.
Once trust is validated, encrypted negotiation can continue.

Trust → Data tunnel allowed
Only after mutual authentication will IPsec SAs be created.
This prevents unauthorized or spoofed tunnel creation.

Internet        → carries IPsec
The public internet transports IKE/ESP packets.
Only outer headers are visible; inner data stays encrypted.

Tunnel          → carries routing
The IPsec tunnel acts like a secure virtual link between sites.
Dynamic routing protocols travel inside it.

BGP             → carries prefixes
BGP exchanges routes to remote networks through the IPsec tunnel.
This decides how traffic flows between the two sites.

ESP             → protects data
ESP = Encapsulating Security Payload, Protocol = 50
ESP encrypts, authenticates, and secures actual payload traffic.
Everything inside ESP is unreadable to intermediaries.

Policy-based VPN = ACL selects traffic
Traffic is matched against ACL rules to determine what enters the tunnel.
More static and suited for simple point-to-point connections.

Route-based VPN  = Routing selects traffic
A virtual tunnel interface is used, and routing decides traffic flow.
Supports BGP, multiple networks, and flexible topologies.

SPI = Security Parameter Index
A 32‑bit label used to identify the correct IPsec SA.
Every ESP packet includes an SPI for fast lookup.
Different SPIs exist for each direction.

BGP OVER IPsec 
BGP runs inside the tunnel, securing TCP 179.
Provides dynamic route exchange between sites.
Ideal for multi‑network or redundant designs.

Time-based rekey   = After lifetime expiry
Triggers after configured SA lifetime expiring (e.g., 3600s).
Ensures keys are refreshed periodically for better security.

Volume-based rekey = After data threshold
Triggered after a data threshold (MB/GB) is crossed.
Prevents excessive data exposure under a single key.

NAT present → ESP in UDP 4500
No NAT     → ESP directly

Tunnel Mode:    Encrypts entire original IP packet (header + payload) and adds a new IP header — used for site-to-site VPNs.
Transport Mode: Encrypts only the payload of the IP packet, keeping the original IP header — mainly used for host-to-host VPNs.

DH (Diffie-Hellman): A key-exchange method that lets two devices create the same secret key over an untrusted network without sending the key itself.
PFS (Perfect Forward Secrecy): Forces fresh DH key generation for every Phase-2 so old keys can’t decrypt new or past traffic even if one key is compromised.
AES (Advanced Encryption Standard): The symmetric cipher that encrypts VPN data payloads (e.g., AES-256 = stronger encryption).
SHA (Secure Hash Algorithm): Provides integrity and authentication by hashing packets so any tampering is detected.

IKEv2 Phase-1 = Trust + control channel, 4 messages, DH + AES + SHA + PSK
IKEv2 Phase-2 = Data encryption, 2 messages, PFS optional, ESP transform set
</code></pre>
</details>

<!-- ========================= -->

<details>
<summary>🟦 PHASE‑1</summary>

<pre><code>## 🔐 PHASE‑1 (IKE_SA – Internet Key Exchange Security Association)

Purpose:
Create a secure and trusted control channel between SAP and CUSTOMER_A VPN devices.

What happens:
- Devices exchange keys (DH)
- Authenticate each other using PSK → this is where TRUST is built
- Agree how future negotiations will be protected
</code></pre>

<b>Messages &amp; Trust</b>
<pre><code>IKE_SA_INIT  : Request + Response (2 messages)
IKE_AUTH     : Request + Response (2 messages)
---------------------------------------------
Total        : 4 messages
</code></pre>

<hr/>

<b>PHASE‑1 (IKE_SA) — REQUEST / RESPONSE</b>
<pre><code>SAP ASR 1002-HX (Initiator)          CUSTOMER_A (Responder)
198.98.99.9                          203.62.63.64
</code></pre>

<pre><code>================================================================
(1) IKE_SA_INIT — REQUEST  (UDP 500)
----------------------------------------------------------------
HDR, SAi, KEi, Ni  --------------------------------------------&gt;
        • SAi  = IKE proposal (AES256 / SHA256 / DH-14)
        • KEi  = SAP DH public key
        • Ni   = SAP nonce
</code></pre>

<pre><code>(2) IKE_SA_INIT — RESPONSE (UDP 500)
----------------------------------------------------------------
                                &lt;--------------------------------
                                HDR, SAr, KEr, Nr
                                • SAr = Accepted proposal
                                • KEr = CUSTOMER_A DH public key
                                • Nr  = CUSTOMER_A nonce
</code></pre>

<pre><code>🔐 RESULT AFTER IKE_SA_INIT:
• DH-14 key exchange completed
• Shared secret derived
• Still NO authentication
================================================================
</code></pre>

<pre><code>IKE_AUTH (Message 3 &amp; 4)
SAP ASR 1002-HX (Initiator)          CUSTOMER_A (Responder)
================================================================
</code></pre>

<pre><code>(3) IKE_AUTH — REQUEST  (UDP 500)
----------------------------------------------------------------
HDR, SK {
        IDi,
        AUTH,
        SAi2,
        TSi,
        TSr
} --------------------------------------------------------------&gt;
        • IDi  = SAP identity
        • AUTH = PSK authentication (cisco@9051#)
        • SAi2 = CHILD_SA proposal
        • TSi  = 192.168.10.0/24
        • TSr  = 172.168.20.0/24
</code></pre>

<pre><code>(4) IKE_AUTH — RESPONSE (UDP 500)
----------------------------------------------------------------
                                &lt;--------------------------------
                                HDR, SK {
                                        IDr,
                                        AUTH,
                                        SAr2,
                                        TSi,
                                        TSr
                                }
                                • IDr  = CUSTOMER_A identity
                                • AUTH = PSK verified
                                • SAr2 = CHILD_SA accepted
</code></pre>

<pre><code>================================================================
✅ IKE_SA ESTABLISHED (PHASE-1 COMPLETE)
================================================================
</code></pre>
</details>

<!-- ========================= -->

<details>
<summary>🟦 PHASE‑2</summary>

<pre><code>## 🔒 PHASE‑2 (CHILD_SA – IPsec Security Association)

Purpose:
Create a trusted and encrypted tunnel for actual data traffic.
</code></pre>

<b>Messages &amp; Trust</b>
<pre><code>CREATE_CHILD_SA : Request + Response (2 messages)
-----------------------------------------------
Total           : 2 messages
</code></pre>

<pre><code>SAP ASR 1002-HX (Initiator)          CUSTOMER_A (Responder)
================================================================
</code></pre>

<pre><code>(5) CREATE_CHILD_SA — REQUEST
----------------------------------------------------------------
HDR, SK {
        SA,
        Ni,
        KEi,
        TSi,
        TSr
} --------------------------------------------------------------&gt;
        • SA  = ESP proposal (AES256 / SHA256)
        • Ni  = New nonce
        • KEi = PFS DH-14 key
        • TSi = 192.168.10.0/24
        • TSr = 172.168.20.0/24
</code></pre>

<pre><code>(6) CREATE_CHILD_SA — RESPONSE
----------------------------------------------------------------
                                &lt;--------------------------------
                                HDR, SK {
                                        SA,
                                        Nr,
                                        KEr,
                                        TSi,
                                        TSr
                                }
                                • SA  = ESP accepted
                                • Nr  = Responder nonce
                                • KEr = Responder DH key
</code></pre>

<pre><code>================================================================
✅ IPsec CHILD_SA ESTABLISHED (PHASE-2 COMPLETE)
================================================================
</code></pre>
</details>

<!-- ========================= -->

<details>
<summary>🟦 BIG PICTURE (ASCII)</summary>

<pre><code>Phase-1 (IKE_SA)   → 4 messages → TRUST established
Phase-2 (CHILD_SA) → 2 messages → DATA tunnel created
-----------------------------------------------
TOTAL              → 6 messages
</code></pre>

<b>Wireshark View</b>
<pre><code>1  IKE_SA_INIT      Request
2  IKE_SA_INIT      Response
3  IKE_AUTH         Request
4  IKE_AUTH         Response
5  CREATE_CHILD_SA  Request
6  CREATE_CHILD_SA  Response
</code></pre>

<hr/>

<h4>📦 FULL ASCII (Packet &amp; Path Views)</h4>

<pre><code>--------------------------------------------------------
A) IKEv2 PHASE-1 PACKET (UDP 500) – FULL ON-WIRE FORMAT
--------------------------------------------------------

|-------------------- Ethernet --------------------|
| Src MAC: SAP-ASR-MAC                             |
| Dst MAC: ISP-GW-MAC                              |
| Ethertype: 0x0800 (IPv4)                         |
|--------------------------------------------------|
|----------------------- IP -----------------------|
| Version: 4                                       |
| Header Length: 20 bytes                          |
| DSCP: 0                                          |
| Total Length: 412                                |
| Identification: 0x1a2b                           |
| Flags: DF=1 MF=0                                 |
| Fragment Offset: 0                               |
| TTL: 64                                          |
| Protocol: UDP (17)                               |
| Src IP: 198.98.99.9                              |
| Dst IP: 203.62.63.64                             |
|--------------------------------------------------|
|----------------------- UDP ----------------------|
| Src Port: 500                                    |
| Dst Port: 500                                    |
| Length: 392                                      |
| Checksum: 0x9f2a                                 |
|--------------------------------------------------|
|----------------------- IKE ----------------------|
| Exchange: IKE_SA_INIT                            |
| SAi: AES256 / SHA256 / DH-14                     |
| KEi: DH Public Key                               |
| Ni: Initiator Nonce                              |
|--------------------------------------------------|
</code></pre>

<pre><code>--------------------------------------------------------
B) IKEv2 AUTH PACKET (ENCRYPTED PAYLOAD)
--------------------------------------------------------

|----------------------- IKE -----------------------|
| HDR                                              |
|--------------------------------------------------|
| 🔐 SK (Encrypted Payload)                        |
|   - IDi                                          |
|   - AUTH                                         |
|   - SAi2                                         |
|   - TSi                                          |
|   - TSr                                          |
|--------------------------------------------------|
</code></pre>

<pre><code>--------------------------------------------------------
C) ESP DATA PACKET (NO NAT)
--------------------------------------------------------

|-------------------- Ethernet --------------------|
| Src MAC: SAP-ASR-MAC                             |
| Dst MAC: ISP-GW-MAC                              |
|--------------------------------------------------|
|----------------------- IP -----------------------|
| Src IP: 198.98.99.9                              |
| Dst IP: 203.62.63.64                             |
| TTL: 63                                          |
| Protocol: ESP (50)                               |
|--------------------------------------------------|
|----------------------- ESP ----------------------|
| SPI: 0x12345678                                  |
| Sequence Number: 1024                            |
|--------------------------------------------------|
|************* ENCRYPTED **************************|
| Inner IP Src: 192.168.10.10                      |
| Inner IP Dst: 172.168.20.20                      |
| Inner TTL: 64                                    |
| Protocol: TCP                                    |
| Src Port: 52344                                  |
| Dst Port: 443                                    |
| Flags: PSH, ACK                                  |
| Data: Application Payload                        |
| Padding / Pad Length / Next Header               |
| ICV (HMAC-SHA256)                                |
|*************************************************|
</code></pre>

<pre><code>--------------------------------------------------------
D) ESP WITH NAT-T (UDP 4500)
--------------------------------------------------------

|----------------------- IP -----------------------|
| Src IP: 198.98.99.9                              |
| Dst IP: 203.62.63.64                             |
| Protocol: UDP (17)                               |
|--------------------------------------------------|
|----------------------- UDP ----------------------|
| Src Port: 4500                                   |
| Dst Port: 4500                                   |
|--------------------------------------------------|
|----------------------- ESP ----------------------|
| SPI / Seq / Encrypted Payload                    |
|--------------------------------------------------|
</code></pre>

<pre><code>--------------------------------------------------------
E) TTL HOP-BY-HOP VIEW (OUTER IP)
--------------------------------------------------------

SAP Host (TTL=64)
 → SAP ASR (63)
 → ISP (62)
 → Internet (61)
 → CUSTOMER_A Router (60)
</code></pre>

<pre><code>--------------------------------------------------------
F) MTU / MSS NUMERIC IMPACT
--------------------------------------------------------

Ethernet MTU: 1500
IP + ESP Overhead: ~56 bytes
Effective Inner MTU: ~1444
Recommended TCP MSS Clamp: 1360–1400
</code></pre>

<pre><code>--------------------------------------------------------
G) DIRECTIONAL SPI VIEW
--------------------------------------------------------

SAP → CUSTOMER_A:
Outbound SPI: 0x12345678

CUSTOMER_A → SAP:
Outbound SPI: 0x87654321

(Each direction uses a different SA)
</code></pre>

<pre><code>--------------------------------------------------------
H) CONTROL vs DATA PLANE QUICK MAP
--------------------------------------------------------

Control Plane:
UDP 500 / 4500
IKE_SA_INIT
IKE_AUTH

Data Plane:
ESP (Protocol 50)
Encrypted Inner IP + TCP/UDP/ICMP
</code></pre>
</details>
