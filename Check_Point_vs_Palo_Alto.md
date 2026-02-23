```java

🖥️ System Resource Monitoring
| Task          | Check Point | Palo Alto                       | Purpose                 | Production Use Case              | Risk |
| ------------- | ----------- | ------------------------------- | ----------------------- | -------------------------------- | ---- |
| CPU Usage     | `top`       | `show running resource-monitor` | Check CPU utilization   | High CPU, traffic drop, slowness | Safe |
| Memory Usage  | `free -m`   | `show system resources`         | Check RAM usage         | Memory leak suspicion            | Safe |
| Disk Usage    | `df -h`     | `show system disk-space`        | Check storage space     | Log partition full               | Safe |
| Process Check | `ps aux`    | `show system software status`   | Check running processes | Daemon crash suspicion           | Safe |

🌐 Interface & Network
| Task             | Check Point           | Palo Alto            | Purpose            | Production Use Case              | Risk |
| ---------------- | --------------------- | -------------------- | ------------------ | -------------------------------- | ---- |
| Interface Status | `show interfaces all` | `show interface all` | Check link state   | Link down issue                  | Safe |
| Routing Table    | `show route`          | `show routing route` | Verify route       | Traffic not reaching destination | Safe |
| ARP Table        | `arp -an`             | `show arp all`       | Verify MAC mapping | Same subnet issue                | Safe |

📜 Policy & Rule Verification
| Task                | Check Point                | Palo Alto                                                     | Purpose                | Production Use Case        | Risk |
| ------------------- | -------------------------- | ------------------------------------------------------------- | ---------------------- | -------------------------- | ---- |
| Policy Installed?   | `fw stat`                  | `show jobs all`                                               | Check policy active    | Rule added but not working | Safe |
| Show Security Rules | SmartConsole               | `show running security-policy`                                | Verify rule exists     | Confirm rule added         | Safe |
| Rule Hit Count      | Logs                       | `show rule-hit-count vsys vsys1 rule-base security rules all` | Check if rule matching | Traffic not hitting rule   | Safe |
| Active Connections  | `fw tab -t connections -s` | `show session all`                                            | Check active sessions  | Debug stuck sessions       | Safe |

🔄 Policy Changes
| Task               | Check Point                | Palo Alto           | Purpose                    | Production Use Case  | Risk    |
| ------------------ | -------------------------- | ------------------- | -------------------------- | -------------------- | ------- |
| Install Policy     | Install via GUI            | `commit`            | Activate rule changes      | After rule addition  | Medium  |
| Remove Policy      | `fw unloadlocal`           | N/A                 | Remove policy from gateway | Emergency rollback   | High ⚠️ |
| Clear All Sessions | `fw tab -t connections -x` | `clear session all` | Reset sessions             | NAT or routing issue | High ⚠️ |

🔎 Logs & Troubleshooting
| Task              | Check Point        | Palo Alto                           | Purpose                                                | Production Use Case   | Risk               |      |
| ----------------- | ------------------ | ----------------------------------- | ------------------------------------------------------ | --------------------- | ------------------ | ---- |
| Live Logs         | `fw log -f`        | `show log traffic`                  | Real-time traffic check                                | Rule troubleshooting  | Safe               |      |
| Filter Logs       | `fw log -f         | grep IP`                            | `show log traffic query "( addr.src in 10.10.10.10 )"` | Debug specific IP     | Traffic drop issue | Safe |
| Packet Capture    | `fw monitor`       | `debug dataplane packet-diag`       | Deep packet inspection                                 | Complex traffic issue | Medium             |      |
| OS Packet Capture | `tcpdump -ni eth0` | `tcpdump filter "host 10.10.10.10"` | Raw packet capture                                     | Routing issue         | Medium             |      |

🔐 VPN Troubleshooting
| Task             | Check Point      | Palo Alto                         | Purpose             | Production Use Case | Risk   |
| ---------------- | ---------------- | --------------------------------- | ------------------- | ------------------- | ------ |
| Check VPN Status | `cpstat vpn`     | `show vpn ike-sa`                 | Verify tunnel       | VPN down issue      | Safe   |
| IPsec Status     | `vpn tu`         | `show vpn ipsec-sa`               | Check Phase 2       | Tunnel unstable     | Safe   |
| Restart VPN      | `vpn tu → reset` | `clear vpn ike-sa gateway <name>` | Re-establish tunnel | VPN stuck           | Medium |

⚡ Performance & Acceleration
| Task                 | Check Point          | Palo Alto                  | Purpose            | Production Use Case | Risk    |
| -------------------- | -------------------- | -------------------------- | ------------------ | ------------------- | ------- |
| SecureXL Status      | `fwaccel stat`       | Hardware offload automatic | Check acceleration | Performance issue   | Safe    |
| Disable Acceleration | `fwaccel off`        | N/A                        | Disable fast path  | Debug traffic       | High ⚠️ |
| CoreXL Status        | `fw ctl multik stat` | N/A                        | Check core usage   | CPU imbalance       | Safe    |

🔁 Process Restart (Very Sensitive)

| Task                      | Check Point          | Palo Alto                                       | Purpose          | Production Use Case | Risk        |
| ------------------------- | -------------------- | ----------------------------------------------- | ---------------- | ------------------- | ----------- |
| Restart Firewall Services | `cpstop` / `cpstart` | `debug software restart process <process-name>` | Restart services | Daemon crash        | High ⚠️     |
| Reboot Device             | `reboot`             | `request restart system`                        | Full restart     | Critical failure    | Critical 🚨 |

🔵 Check Point Checklist
1. Rule created in SmartConsole?
2. Policy installed? → fw stat
3. Rule above cleanup rule?
4. Correct source/destination?
5. NAT configured?
6. Logs show rule match?
7. Session created?

🔴 Palo Alto Checklist
1. Rule created?
2. Commit successful?
3. Rule above deny rule?
4. Correct zone?
5. Application correct?
6. Service correct?
7. Hit count increasing?
8. Session visible?
8. VPN domain correct?




```
