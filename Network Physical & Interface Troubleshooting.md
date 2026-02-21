# Network Physical & Interface Troubleshooting – Quick Guide (v2)
**Prepared for: Jeet Roy**

This pocket guide helps you quickly determine if an interface or device issue is physical (cable/optic/port), Layer‑2 (STP/MAC), or utilization-related.

---

## 1) Interface Status (UP/DOWN)

Command:  
show interface ethernet3 status

Shows: Admin/oper state, speed/duplex, media type, VLAN/trunk.

Good: connected, expected speed/duplex (1G Full), proper VLAN/type  
Bad: notconnect, err-disabled, sfpAbsent, unsupported module

---

## 2) Detailed Interface Health

Command:  
show interface ethernet3

Input errors — should be 0; >0 = cable/optic/NIC issue  
CRC — should be 0; >0 = physical corruption  
Alignment/Symbol — should be 0; >0 = duplex mismatch or noise  
Input discards — >0 = congestion/buffer pressure  
Output errors — >0 = egress/NIC/duplex issue  
Output discards — >0 = queue full/QoS  
Speed/Duplex — must match peer  
Link flaps — frequent = physical instability (check logs)

---

## 3) Interface Utilization (Traffic Load)

Commands:  
show interface ethernet3 | include rate  
show interfaces counters rates

Definitions:  
Utilization: how full the link is  
Saturation: >90% of line rate, sustained  
Sustained: consistently high for many minutes (not 1–2 second spikes)

Interpretation:  
>90% sustained = saturation  
High inbound + low outbound = host pushing data  
High outbound + low inbound = switch forwarding heavily to the host

---

## 4) Quick Health Filter (Errors & Discards)

Command:  
show interface ethernet3 | include error|discard

Use this when you only want to see physical problems and congestion indicators without scrolling.
---

## 5) MAC Address on the Port

Command:  
show mac address-table interface ethernet3

Good: 1 MAC  
Bad: multiple MACs, flapping → loop or unauthorized bridge
Good: 1 MAC (host). Bad: Multiple MACs (loop/bridge), or MAC flapping (instability).

---

## 6) Spanning Tree (STP) Health

Command:  
show spanning-tree vlan

Good: edge, forwarding  
Bad: frequent state changes, non-edge on server ports, unexpected root role change

---

## 7) Transceiver / Optic Health (Fiber Ports)

Command:  
show interface ethernetX transceiver details

Check: temperature, Tx/Rx power  
Low Rx + CRC = dirty/damaged fiber  
Copper ports show N/A (normal)

---

## 8) Copper Cable Test (TDR)

Command:  
show cable-diagnostics tdr interface ethernetX

Good: all pairs Normal  
Bad: open/short/mismatch or >100m

---

## 9) Device CPU / Memory

Commands:  
show processes cpu  
show system resources

Good: CPU <20% average  
Bad: >70% sustained → can cause latency/drops

---

## 10) Interface Counters & Storm Check

Command:  
show interfaces ethernetX counters

Very high broadcast/multicast → storm  
Unknown-unicast flooding → potential ARP/ND issue

---

## 11) Neighbor Discovery (CDP/LLDP)

Commands:  
show lldp neighbors  
show cdp neighbors

Server ports may show none  
If you see a switch on a server port → unauthorized bridging

---

## 12) QoS Policy / Queue Drops

Command:  
show policy-map interface ethernetX

Drops in specific queues = congestion caused by QoS

---

## 13) Logs & Link Flaps

Commands:  
show log | include EthernetX|link  
show interface ethernetX | include change

Frequent link up/down = physical issue (cable/optic/NIC)

---

## 14) Additional Helpful CLI

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

---

## 15) Quick GOOD vs BAD Matrix

| Check | GOOD | BAD |
|------|------|------|
| Interface state | connected | notconnect, err-disabled, sfpAbsent |
| Errors | 0 | >0 |
| Discards | 0 | >0 |
| Utilization | <70% sustained | >90% sustained |
| MACs | 1 | >1 or flapping |
| STP | edge, forwarding | non-edge / transitions |
| Transceiver | normal | low Rx / high temp |
| Cable TDR | normal | open/short/mismatch |
| CPU | <20% avg | >70% sustained |
| Broadcast/Multicast | low | very high |

---

## 16) Worked Example — Et3 at ~99% Inbound, No Errors

Observed:  
Input ≈983 Mbps  
Output ≈0.2 Mbps  
0 errors  
1 MAC  
STP edge  
VLAN 110

Interpretation:  
Physical layer healthy. Single host pushing near line-rate inbound. Not a storm.

Next Steps:  
Check uplink counters  
SPAN the port for capture  
Coordinate with server/application team

---

## Handy Commands (Copy/Paste)

show interface ethernet3  
show interface ethernet3 | include rate|error|discard  
show interfaces ethernet3 counters  
show mac address-table interface ethernet3  
show spanning-tree vlan 110  
show interfaces counters rates  
monitor session 10 source interface ethernet3 both  
monitor session 10 destination interface  
no monitor session 10  

---

## Tips

Always check CRC/errors first  
If clean, check utilization + MAC table  
Single MAC + high unicast = host/application issue  
Use SPAN or sFlow for visibility
