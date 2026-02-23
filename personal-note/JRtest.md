# 📝 VPN Call Handling Log – TEMPLATE

## 📅 Date & Time
- **Date:**  
- **Call Time:**  
- **Time Zone:** IST (or customer timezone)

---

## 🎟 Service / Ticket Details
- **Service Request / Ticket ID:**  
- **Customer Name / CID:**  
- **DC (Data Center):**  
- **Engineer Handling:**  

---

## 🧩 Customer Issue Summary
> Short description of what customer reported  
(E.g., "VPN tunnel down", "BGP idle", "Unable to reach SAP system", etc.)

---

## 🔐 VPN Technical Details
- **Type of VPN:** (Policy-based / Route-based / BGP 1EP / BGP 2EP / Multi‑SA etc.)  
- **Customer Device & Version:**  
- **SAP Peer IP:**  
- **Customer Peer IP:**  
- **NAT IP (if applicable):** Yes/No  

---

## 🔍 Tunnel Status
- **Before Call:**  
- **After Call:**  

---

## ⚙ Phase‑1 (IKE) Parameters
### **SAP Side (Before):**
- Encryption:  
- Hash / PRF:  
- DH Group:  
- Lifetime:  

### **Customer Side (Before):**
- Encryption:  
- Hash / PRF:  
- DH Group:  
- Lifetime:  

### **SAP Side (After):**
- Encryption:  
- Hash / PRF:  
- DH Group:  
- Lifetime:  

### **Customer Side (After):**
- Encryption:  
- Hash / PRF:  
- DH Group:  
- Lifetime:  

---

## 🔐 Phase‑2 (IPsec) Parameters
### **SAP Side (Before):**
- Encryption:  
- Hash:  
- PFS Group:  
- Lifetime:  

### **Customer Side (Before):**
- Encryption:  
- Hash:  
- PFS Group:  
- Lifetime:  

### **SAP Side (After):**
- Encryption:  
- Hash:  
- PFS Group:  
- Lifetime:  

### **Customer Side (After):**
- Encryption:  
- Hash:  
- PFS Group:  
- Lifetime:  

---

## 📡 Encryption Domains (Traffic Selectors)
- **SAP Subnets:**  
- **Customer Subnets:**  

---

## 🛠 Actions Performed
### **SAP Side:**
-  

### **Customer Side:**
-  

---

## 🧪 Validation Performed
- Ping tests (SAP → Customer): PASS/FAIL  
- Ping tests (Customer → SAP): PASS/FAIL  
- BGP Status:  
- Route Exchange Verified: YES/NO  
- Packet Captures (if any):  

---

## ❗ Issue Source (SAP / Customer / Third‑party)
-  

---

## 📌 Notes / Important Observations
-  

---

## 🔚 Next Action Plan
-  

---

## ✔ Final Status
**Tunnel State:** UP / DOWN  
**Customer Confirmation:** YES / NO  
