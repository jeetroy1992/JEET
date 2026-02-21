# Network Physical & Interface Troubleshooting – Quick Guide

**Purpose:** Quickly determine if an interface/device issue is **physical (cable/optic/port)**, **Layer‑2 (STP/MAC)**, or **utilization/capacity**.  
Includes: exact CLI commands, what each shows, how to read outputs, **GOOD vs BAD** indicators, and **clear definitions** of *saturation* and *sustained period*.

---

## 1) Interface Status (UP/DOWN)

**Command**
```bash
show interface ethernet3 status


show interfaces description              # quick overview of port states/names
show interface counters errors           # error-focused snapshot
show interfaces | include line protocol|rate  # status + rates in one view
show l2 protocol counters                # L2 control protocol counters (platform-dependent)
show interfaces flowcontrol              # pause frames (may explain slow apps without drops)
show ip igmp snooping groups vlan <VID>  # multicast receivers (storm debugging)
show mac address-table vlan <VID> | count  # MAC population (loops/anomalies)
show interface vlan <VID>                # SVI counters (L3 view)
show etherchannel summary                # or 'show port-channel summary' for bundles
show spanning-tree detail | include ieee|occurr  # topo change hints
monitor session <id> source interface ethernetX both
monitor session <id> destination interface <tool-port>
no monitor session <id>                  # SPAN for packet captures; remember to remove
show platform hardware port <if> counters  # ASIC-level drops (platform-dependent)

```java
- Interface state → GOOD: connected; BAD: notconnect / err-disabled / sfpAbsent
- Errors (CRC/Input/Output) → GOOD: 0; BAD: >0 (suspect cable/optic/NIC/duplex)
- Discards → GOOD: 0; BAD: >0 (congestion/queues/QoS)
- Utilization → GOOD: <70% sustained; BAD: >90% sustained (saturation)
- MACs on port → GOOD: 1; BAD: >1 or flapping (loop/bridge)
- STP on access → GOOD: edge, forwarding; BAD: non‑edge/frequent state changes
- Transceiver (fiber) → GOOD: power/temp in thresholds; BAD: low Rx/high temp/unsupported
- Copper TDR → GOOD: all pairs Normal; BAD: Open/Short/Mismatch/too long
- CPU → GOOD: <20% avg; BAD: >70% sustained
- Broadcast/Multicast → GOOD: low; BAD: very high ⇒ storm
```
