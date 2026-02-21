
***
```markdown


---
## 1) Interface Status (UP/DOWN)

**Command:**
```
show interface ethernet3 status
```
**Shows:** Admin/oper state, speed/duplex, media type, VLAN/trunk.
- **Good:** `connected`, speed/duplex as expected (e.g., full 1G), correct VLAN/Type (1000BASE-T)  
- **Bad:** `notconnect` (unplugged/bad), `err-disabled` (security/STP/link‑flap), `sfpAbsent` / unsupported module
---
## 2) Detailed Interface Health
**Command:**
```
show interface ethernet3
```
**Key fields:**
- **Input errors:** Frames received with problems — expect **0**. >0 → cable/optic/NIC issue  
- **CRC:** Checksum failures — expect **0**. >0 → physical corruption  
- **Alignment/Symbol:** Bit/line errors — expect **0**. >0 → noise or duplex mismatch  
- **Input discards:** Drops due to buffer/QoS — expect **0**. >0 → congestion  
- **Output errors:** Failed transmit — expect **0**  
- **Output discards:** Queue full/QoS → congestion  
- **Speed/Duplex:** Must match peer — half‑duplex = misconfig  
- **Flaps/last clear:** Frequent flaps → unstable cable/NIC (check logs)
---
## 3) Interface Utilization (Traffic Load) + Definitions
**Commands:**
```
show interface ethernet3 | include rate
show interfaces counters rates
```
### Definitions
- **Utilization:** e.g., 983 Mbps on 1G port  
- **Saturation:** >90% of line rate for sustained period  
- **Sustained period:** Not spikes — continuous high load for minutes (e.g., >90% for >10 min)
### Interpret Quickly
- **>90% sustained = saturation** → expect latency/drops  
- **High inbound + low outbound** = host sending into switch  
- **High outbound + low inbound** = switch forwarding heavily to host  
---
## 4) Quick Health Filter (Errors & Discards)

**Command:**
```
show interface ethernet3 | include error|discard
```
---
## 5) MAC Address on the Port
**Command:**
```
show mac address-table interface ethernet3
```
- **Good:** 1 MAC  
- **Bad:** Multiple MACs (loop/bridge), MAC flapping (instability)
---
## 6) Spanning Tree (STP) Health
**Command:**
```
show spanning-tree vlan
```
- **Good:** Edge, forwarding  
- **Bad:** Frequent state changes, non‑edge on server ports, unexpected root change  
---
## 7) Transceiver / Optic Health (Fiber Ports)

**Command:**
```
show interface ethernetX transceiver details
```
- Check **temperature**, **Tx/Rx power**, thresholds  
- Low Rx + CRC → dirty/damaged fiber  
- Copper (1000BASE‑T) = **N/A** (normal)
---
## 8) Copper Cable Test (TDR)

**Command:**
```
show cable-diagnostics tdr interface ethernetX
```
- **Good:** All pairs **Normal**  
- **Bad:** Open/Short/Mismatch or length >100m  
---
## 9) Device CPU/Memory Health
**Commands:**
```
show processes cpu
show system resources
```
- **Good:** CPU <20% avg  
- **Bad:** CPU >70% sustained → can cause lag/drops  
---
## 10) Interface Counters & Storm Check

**Command:**
```
show interfaces ethernetX counters
```
- Very high broadcast/multicast → storm  
- Unknown‑unicast flooding = high unicast with no ARP/ND entries  
---
## 11) Neighbor Discovery (CDP/LLDP)
**Commands:**
```
show lldp neighbors
show cdp neighbors
```
- Server ports often show none  
- Seeing a **switch** where a host is expected → unauthorized bridging  
---
## 12) QoS Policy / Queue Drops
**Command:**
```
show policy-map interface ethernetX
```
- Drops in specific queues = congestion due to QoS  

---

## 13) Logs & Link Flaps

**Commands:**
```
show log | include EthernetX|link
show interface ethernetX | include change
```
Frequent up/down = cable/optic/NIC issues.
---
## 14) Additional Helpful CLI
```
show interfaces description
show interface counters errors
show interfaces | include line protocol|rate
show l2 protocol counters
show interfaces flowcontrol
show ip igmp snooping groups vlan
show mac address-table vlan | count
show interface vlan
show etherchannel summary
show spanning-tree detail | include ieee|occurr
monitor session source interface ethernetX both
show platform hardware port counters
```
---
## 15) Quick GOOD vs BAD Matrix

| Check | GOOD | BAD |
|------|------|------|
| Interface state | connected | notconnect / err-disabled / sfpAbsent |
| Errors (CRC/Input/Output) | 0 | >0 → physical issues |
| Discards | 0 | >0 → congestion/QoS |
| Utilization | <70% sustained | >90% sustained |
| MACs on port | 1 | >1 / flapping |
| STP | edge, forwarding | non-edge / state changes |
| Transceiver | Within thresholds | Low Rx / high temp |
| Copper TDR | Normal | Open/Short/Mismatch |
| CPU | <20% avg | >70% sustained |
| Broadcast/Multicast | low | high → storm |
---
## 16) Worked Example – Et3 at ~99% Inbound, No Errors

**Observed:**  
- Input ~983 Mbps  
- Output ~0.2 Mbps  
- 0 errors; single MAC; STP edge; VLAN 110  

**Interpretation:**  
Physical layer healthy. Host driving near‑line‑rate unicast. Not a storm.

**Next Steps:**  
- Check uplinks (`show interfaces counters rates`)  
- SPAN port to capture  
- Engage server team  

---

## Handy Commands (Copy/Paste)

```

show interface ethernet3
show interface ethernet3 | include rate|error|discard
show interfaces ethernet3 counters
show mac address-table interface ethernet3
show spanning-tree vlan 110
show interfaces counters rates
monitor session 10 source interface ethernet3 both
monitor session 10 destination interface
no monitor session 10

```

---

## Tips

- Always check **errors/CRC first**  
- If clean → check utilization & MAC table  
- If single MAC + high unicast → investigate host/application  
- Use SPAN or sFlow for visibility  
```
***


Just tell me!
