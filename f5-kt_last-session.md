```java

***

# ✅ **F5 Load Balancer – ACL, VIP & Outbound/Inbound Flow Notes (Enhanced with Deep Technical Additions)**

### **Prepared for: Jeet Roy (Connectivity & Network Engineer – C)**

***

# **1️⃣ Basics: Traffic Flow Architecture**

### **Internet ↔ INET Router ↔ F5 Load Balancer → Backend Servers**

### **🔍 Deep Technical Explanation (Simple Terms)**

*   INET routers act like **security guards**.  
    They decide: *“Should this packet be allowed to reach F5 or not?”*
*   F5 only receives traffic **after** INET routers allow it via ACL.
*   F5 Load Balancer then:
    *   Terminates TCP/HTTPS sessions
    *   Applies SNAT, SSL profiles, IRules
    *   Sends traffic to backend servers

### **📘 Example**

    Internet User → Public IP (VIP) → INET Router (ACL allow?) → F5 VIP → Backend Server

If ACL is missing → **traffic dies at INET router**, even if VIP is up.

***

# **2️⃣ Understanding Outbound vs Inbound Traffic**

## **A. Outbound Traffic (Customer → Internet)**

### **🔍 Deep Technical Explanation**

Outbound is when **customer workload needs Internet**.  
Examples:

*   SAP system sending logs to cloud
*   SMTP mail relay
*   Checking licenses externally

For SNAT:

*   F5 rewrites private IP → NAT public IP
*   INET router must allow **return traffic** from the Internet.

### **➡️ Important Concept: “Established Packets”**

Outbound ACL uses:

    established

This means:

*   Only **return** packets from Internet (reply) are allowed
*   Prevents any *new* connection from internet to host

### **📘 Example – SMTP Outbound (Port 587)**

    Customer Server (10.x.x.x) → F5 SNAT → NAT IP → Internet (smtp.office365.com)

ACL Needed:

    permit tcp any host <NAT-IP> eq 587 established

***

## **B. Inbound Traffic (Internet → Customer VIP)**

### **🔍 Deep Technical Explanation**

Inbound is **public-facing VIP** where internet users connect:

*   Web access (443)
*   Portal access (non-standard ports)
*   External API access

Inbound **always requires manual ACL** except standard outbound defaults.

### **📘 Example – Public VIP**

    permit tcp any host 157.22.33.44 eq 443

If missing → site will not load for the customer.

***

# **3️⃣ Standard vs Non-standard Ports**

### **🔍 Deep Technical Explanation**

*   **Standard 80/443** → globally accepted safe ports
*   **Non-standard** (22, 587, 30441, 6405 etc.) → may expose service if open to internet
    *   Must restrict by source IP
    *   Or require security approval

### **📘 Example**

**Customer asks:**  
“Open SSH (22) from internet for testing.”

We say:

*   Provide the **source IP**
*   Cannot open to all
*   If they insist → security approval required

ACL:

    permit tcp host <customer-public-IP> host <VIP-IP> eq 22

***

# **4️⃣ Legacy ACL vs CVP Based ACL**

## **A. Legacy ACL (Manual File Edit)**

### **🔍 Deep Technical Explanation**

Legacy = Edit ACL file using a Linux-like editor.  
Risky because:

*   One wrong delete → hundreds of ACL lines can vanish
*   No auto backup unless done manually
*   Only one person can edit at a time (file lock)

### **📘 Example of Editing**

    cd ~
    cd config/confg
    add config acl echo03

Then go to bottom and add entry.

When done:

    :wq
    load acl new echo03

***

## **B. CVP Enabled DCs (GitHub + Pipeline)**

### **🔍 Deep Technical Explanation**

In CVP DCs:

*   ACL files live in **GitHub**
*   You open PR → Approver reviews → CVP deploys to hardware
*   Safer because:
    *   Version control
    *   Rollback available
    *   No file lock problems
    *   Fully audited with timestamps

### **📘 Example Flow**

    GitHub edit → Commit → Approval → Merge → CVP Change Control → Deploy

Deployment time: **30–35 mins**.

***

# **5️⃣ How to Recognize VIP Types**

### **🔍 Extra Technical Explanation**

| VIP Type                  | How F5 Treats It                    | Deep Explanation                                                                  |
| ------------------------- | ----------------------------------- | --------------------------------------------------------------------------------- |
| **Outbound SNAT VIP**     | Created via iApp “Internet Gateway” | Used only for routing Internet-bound traffic; no external clients directly hit it |
| **Inbound Public VIP**    | Exposed on Public IP                | SSL termination, profiles, persistence applied                                    |
| **Internal VPN-only VIP** | Uses `10.x.x.x%RD`                  | Accessible only through internal VPN or MPLS                                      |

### **📘 Example**

    10.22.33.10%2012:8443 → Internal only

***

# **6️⃣ ACL Syntax Quick Guide (with Deep Meaning)**

### **Outbound Example (587)**

    permit tcp any host <NAT-IP> eq 587 established

**Meaning:**

*   Allow internet replies to NAT-IP
*   Cannot initiate connection from Internet

***

### **Inbound Example (443)**

    permit tcp any host <VIP-IP> eq 443

**Meaning:**

*   Allow new connections from Internet
*   Destination is F5 VIP

***

### **Inbound Restricted Example**

    permit tcp host <source-IP> host <VIP-IP> eq 30441

**Used when:**

*   Customer wants access only from specific IP(s)
*   Port is sensitive or non-standard

***

# **7️⃣ Validating ACL on Routers**

### **Deep Technical Meaning**

You search in ALL ACLs:

    ip access-list | grep <IP>

Helps confirm:

*   Is inbound ACL added?
*   Is outbound port allowed?
*   Are multiple entries conflicting?

***

# **8️⃣ Commonly Used Commands**

(No deep changes needed but adding internal meaning)

| Command                 | Explanation                |
| ----------------------- | -------------------------- |
| `add config acl echo02` | Opens ACL file for editing |
| `i`                     | Insert mode (start typing) |
| `:wq`                   | Save & exit                |
| `:q!`                   | Exit without saving        |
| `change log -is -o`     | Shows pending ACL changes  |
| `a <change-id>`         | Approve change             |
| `load acl new echo02`   | Push ACL to routers        |

***


```
