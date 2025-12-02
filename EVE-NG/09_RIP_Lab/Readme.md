# 🔄 RIP Routing Lab

> Complete hands-on lab to configure RIPv2 (Routing Information Protocol) for dynamic routing and automatic network discovery across multiple routers.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [Router IP Configuration](#router-ip-configuration)
6. [RIP Protocol Configuration](#rip-protocol-configuration)
7. [RIP Convergence & Optimization](#rip-convergence--optimization)
8. [PC Configuration](#pc-configuration)
9. [Testing Connectivity](#testing-connectivity)
10. [Verification](#verification)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master dynamic routing using RIPv2 protocol for automatic route discovery.

### By the end of this lab, you will:

- ✅ Understand distance-vector routing protocol fundamentals
- ✅ Configure RIPv2 on multiple routers
- ✅ Verify dynamic route learning and routing tables
- ✅ Understand RIP metrics and hop counting
- ✅ Implement RIP loop prevention mechanisms (split horizon, poison reverse)
- ✅ Configure RIP timers for convergence optimization
- ✅ Troubleshoot RIP routing issues
- ✅ Compare static vs dynamic routing approaches

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **Static Routing** | Understand limitations RIP solves | 08_Basic_Routing Lab |
| **IP Addressing** | Assign IPs to router interfaces | 04_Basic Switch Lab |
| **Router CLI** | Navigate router configuration | 03_Switch Config Lab |
| **Ping Testing** | Verify connectivity | 04_Basic Switch Lab |
| **Distance-Vector Concepts** | Understand RIP algorithm | Basic networking knowledge |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco router images available (IOSv)
- ✅ Virtual PC (VPCS) images available
- ✅ Access to EVE-NG web interface
- ✅ Completed 08_Basic_Routing lab (recommended)

---

## 📊 Lab Topology

> **Purpose:** Visualize the multi-router RIP network topology.

![RIP Lab Topology](imgs/diagram.png)

### Topology Details

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | 192.168.1.1 | 255.255.255.0 | Link to R2 |
| **R1** | Gi0/1 | 10.0.0.1 | 255.255.255.0 | Access network |
| **R2** | Gi0/0 | 192.168.1.2 | 255.255.255.0 | Link to R1 |
| **R2** | Gi0/1 | 172.1.1.1 | 255.255.255.0 | Link to R3 |
| **R3** | Gi0/0 | 172.1.1.2 | 255.255.255.0 | Link to R2 |
| **R3** | Gi0/1 | 20.0.0.1 | 255.255.255.0 | Access network |
| **PC1** | eth0 | 10.0.0.10 | 255.255.255.0 | Access device |
| **PC2** | eth0 | 20.0.0.10 | 255.255.255.0 | Access device |

### Network Overview

```
PC1 (10.0.0.10)
    |
    | Gi0/1
    |
[R1: 10.0.0.1] ───────── [R2] ───────── [R3: 20.0.0.1]
    |                      |                 |
  192.168.1.1          172.1.1.1        172.1.1.2
                                            |
                                           Gi0/1
                                            |
                                       PC2 (20.0.0.10)

Backbone Links (RIP Enabled):
- R1 ↔ R2: 192.168.1.0/24
- R2 ↔ R3: 172.1.1.0/24
```

---

## 🔧 Creating the Lab

> **Purpose:** Set up the three-router RIP network topology in EVE-NG.

### Step 1: Create a New Lab

**What:** Create the lab project for RIP dynamic routing.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Enter lab details:
   - **Lab Name**: `RIP_Lab`
   - **Lab Description**: `RIPv2 Dynamic Routing Configuration`
   - **Lab Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Router Nodes

**What:** Add three Cisco routers for RIP topology.

**How to:**
1. Click **Add Node**
2. Select **Cisco** → **IOSv** (router)
3. Configure:
   - **Node Name**: `R1`
   - **Quantity**: `1`
4. Click **Add**
5. Repeat for R2 and R3:
   - **Node Name**: `R2`
   - **Node Name**: `R3`

> **ℹ️ Note:** All three routers will run RIP and exchange route information.

---

### Step 3: Add Virtual PC Nodes

**What:** Add two virtual PCs in different networks.

**How to:**
1. Click **Add Node**
2. Select **VPCS** (Virtual PC Simulator)
3. Configure:
   - **Node Name**: `PC1`
   - **Quantity**: `2` (creates PC1 and PC2)
4. Click **Add**

---

### Step 4: Connect Devices

**What:** Create all necessary links between devices.

**How to:**
1. **Connect PC1 to R1:**
   - PC1 → R1 Gi0/1 (access network)

2. **Connect PC2 to R3:**
   - PC2 → R3 Gi0/1 (access network)

3. **Connect R1 to R2:**
   - R1 Gi0/0 ↔ R2 Gi0/0 (backbone link)

4. **Connect R2 to R3:**
   - R2 Gi0/1 ↔ R3 Gi0/0 (backbone link)

> **⚠️ Important:** Ensure all backbone links are properly connected for RIP to exchange routes.

---

### Step 5: Start the Lab

**What:** Power on all devices.

**How to:**
1. Right-click on lab name
2. Select **Start Lab**
3. Wait for routers to boot (3-5 minutes)
4. Verify all devices show green status

---

## ⚙️ Router IP Configuration

> **Purpose:** Configure IP addresses on all router interfaces for the RIP network.

### Step 6: Access R1 Console

**What:** Connect to the first router.

**How to:**
1. Right-click on **R1**
2. Select **Console**
3. Press **Enter** to see the prompt

---

### Step 7: Configure R1 Interfaces

**What:** Assign IP addresses to R1's backbone and access interfaces.

**Commands:**
```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 10.0.0.1 255.255.255.0
no shutdown
exit
end
```

**Expected Output:**
```
Router(config)#interface gigabitEthernet 0/0
Router(config-if)#ip address 192.168.1.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#interface gigabitEthernet 0/1
Router(config-if)#ip address 10.0.0.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#end
```

---

### Step 8: Configure R2 Interfaces

**What:** Assign IP addresses to R2's backbone interfaces (between R1 and R3).

**Commands:**
```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.1.2 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 172.1.1.1 255.255.255.0
no shutdown
exit
end
```

> **ℹ️ Note:** R2 acts as a central router connecting R1 and R3.

---

### Step 9: Configure R3 Interfaces

**What:** Assign IP addresses to R3's backbone and access interfaces.

**Commands:**
```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 172.1.1.2 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 20.0.0.1 255.255.255.0
no shutdown
exit
end
```

> **✅ Checkpoint:** All router interfaces are now configured with IP addresses.

---

## 🔄 RIP Protocol Configuration

> **Purpose:** Configure RIPv2 on all routers to enable dynamic routing.

### Step 10: Enable RIP on R1

**What:** Start RIP process and advertise connected networks.

**Commands (on R1):**
```bash
configure terminal
router rip
version 2
network 192.168.1.0
network 10.0.0.0
no auto-summary
exit
end
```

**Command Breakdown:**

| Command | Purpose |
|---------|---------|
| **router rip** | Enter RIP configuration mode |
| **version 2** | Use RIPv2 (sends CIDR notation) |
| **network 192.168.1.0** | Advertise networks connected to R1 |
| **network 10.0.0.0** | Advertise PC1's access network |
| **no auto-summary** | Disable automatic route summarization |

> **ℹ️ Explanation:** RIPv2 will advertise these networks every 30 seconds to neighbors.

---

### Step 11: Enable RIP on R2

**What:** Configure R2 as central router in RIP topology.

**Commands (on R2):**
```bash
configure terminal
router rip
version 2
network 192.168.1.0
network 172.1.1.0
no auto-summary
exit
end
```

> **ℹ️ Note:** R2 connects to both R1 and R3, so it will forward routes between them.

---

### Step 12: Enable RIP on R3

**What:** Configure R3 with RIP for route advertisement.

**Commands (on R3):**
```bash
configure terminal
router rip
version 2
network 172.1.1.0
network 20.0.0.0
no auto-summary
exit
end
```

> **✅ Checkpoint:** RIPv2 is now running on all three routers. Wait 2-3 minutes for convergence.

---

## 🔒 RIP Convergence & Optimization

> **Purpose:** Implement RIP loop prevention and optimize convergence behavior.

### Step 13: Enable Split Horizon on All Routers

**What:** Prevent routing loops by not sending routes back to source.

**Commands (on each router):**
```bash
configure terminal
router rip
no validate-update-source
exit
end
```

**How Split Horizon Works:**

| Scenario | Normal | With Split Horizon |
|----------|--------|-------------------|
| R1 learns 20.0.0.0 from R2 | May send back to R2 | Does NOT send back to R2 |
| Routing Loop Risk | 🔴 High | ✅ Prevented |

> **ℹ️ Note:** Split horizon is usually enabled by default on Cisco routers.

---

### Step 14: Configure RIP Timers

**What:** Optimize RIP convergence time and failure detection.

**Commands (on each router):**
```bash
configure terminal
router rip
timers basic 30 180 120 240
exit
end
```

**Timer Breakdown:**

| Timer | Default | Set Value | Purpose |
|-------|---------|-----------|---------|
| **Update** | 30 sec | 30 sec | How often to send updates |
| **Invalid** | 180 sec | 180 sec | Mark route invalid after silence |
| **Holddown** | 180 sec | 120 sec | Wait before accepting newer info |
| **Flush** | 240 sec | 240 sec | Remove route from table |

> **ℹ️ Explanation:** These timers control how fast RIP detects failed routes.

---

### Step 15: Verify RIP Configuration

**What:** Confirm RIP is configured correctly on all routers.

**Commands:**
```bash
show ip protocols
show run | include rip
```

**Expected Output:**
```
Routing Protocol is "rip"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Default redistribution metric is 1
  Sending updates every 30 seconds, next due in 2 seconds
  Invalid after 180 seconds, hold down 120, flushed after 240
  Automatic network summarization is in effect
  Maximum path 4
  Routing for Networks:
    192.168.1.0
    10.0.0.0
  Passive Interface(s):
```

> **✅ Checkpoint:** RIP configuration verified on all routers.

---

## 🌐 PC Configuration

> **Purpose:** Assign IP addresses to PCs in their respective networks.

### Step 16: Configure PC1

**What:** Assign IP address to first PC with R1 as gateway.

**How to:**
1. Right-click on **PC1**
2. Select **Console**

**Commands:**
```bash
ip 10.0.0.10 255.255.255.0 10.0.0.1
```

**Verification:**
```bash
show ip
```

---

### Step 17: Configure PC2

**What:** Assign IP address to second PC with R3 as gateway.

**How to:**
1. Right-click on **PC2**
2. Select **Console**

**Commands:**
```bash
ip 20.0.0.10 255.255.255.0 20.0.0.1
```

> **✅ Checkpoint:** Both PCs are configured with correct gateways.

---

## 🔍 Testing Connectivity

> **Purpose:** Verify RIP routing is working correctly across all networks.

### Step 18: Test PC1 to PC2 (Through Three Routers)

**What:** Ping from PC1 across the RIP network to PC2.

**Commands (from PC1):**
```bash
ping 20.0.0.10
```

**Expected Output:**
```
PC1>ping 20.0.0.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 20.0.0.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), roundtrip min/avg/max = 5/7/12 ms
```

![RIP Connectivity Test](imgs/ping.png)

> **✅ Success:** Packets traverse: PC1 → R1 → R2 → R3 → PC2

---

### Step 19: Test PC2 to PC1 (Reverse Direction)

**What:** Verify bidirectional routing through RIP network.

**Commands (from PC2):**
```bash
ping 10.0.0.10
```

**Expected Output:**
```
PC2>ping 10.0.0.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 10.0.0.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), roundtrip min/avg/max = 5/7/12 ms
```

> **✅ Confirmed:** Bidirectional routing working through RIP network!

---

### Step 20: Test with Traceroute

**What:** Visualize the path packets take through RIP network.

**Commands (from PC1):**
```bash
trace 20.0.0.10
```

**Expected Output:**
```
PC1>trace 20.0.0.10
trace to 20.0.0.10, 8 hops max, press Ctrl+C to stop
 1   10.0.0.1        2.345 ms  2.123 ms  2.456 ms
 2   192.168.1.2     4.567 ms  4.234 ms  4.891 ms
 3   172.1.1.2       6.789 ms  6.345 ms  7.123 ms
 4   20.0.0.10       8.234 ms  8.567 ms  8.891 ms
```

| Hop | IP | Device | Description |
|-----|----|----|-----------|
| **1** | 10.0.0.1 | R1 | Gateway |
| **2** | 192.168.1.2 | R2 | Backbone route |
| **3** | 172.1.1.2 | R3 | Final backbone link |
| **4** | 20.0.0.10 | PC2 | Destination |

> **ℹ️ Note:** Path shows dynamic routing through RIP network!

---

## ✔️ Verification

> **Purpose:** Confirm RIP routing tables are correct.

### Step 21: Check R1 Routing Table

**What:** Verify R1 learned routes via RIP.

**Commands:**
```bash
show ip route
show ip route rip
```

**Expected Output:**
```
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF

     172.1.1.0/24 [120/2] via 192.168.1.2, 00:00:15, GigabitEthernet0/0
C    10.0.0.0/24 is directly connected, GigabitEthernet0/1
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
     20.0.0.0/24 [120/3] via 192.168.1.2, 00:00:15, GigabitEthernet0/0
R    (all RIP routes shown above)
```

| Route | Metric | Next Hop | Learned Via |
|-------|--------|----------|-------------|
| **10.0.0.0/24** | - | Connected | Direct |
| **192.168.1.0/24** | - | Connected | Direct |
| **172.1.1.0/24** | 2 hops | 192.168.1.2 (R2) | RIP |
| **20.0.0.0/24** | 3 hops | 192.168.1.2 (R2) | RIP |

> **ℹ️ Note:** RIP metric is hop count (1 per router).

---

### Step 22: Check R2 Routing Table

**What:** Verify R2's central position in RIP topology.

**Commands:**
```bash
show ip route rip
```

**Expected Output:**
```
     10.0.0.0/24 [120/1] via 192.168.1.1, 00:00:12, GigabitEthernet0/0
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
C    172.1.1.0/24 is directly connected, GigabitEthernet0/1
     20.0.0.0/24 [120/1] via 172.1.1.2, 00:00:10, GigabitEthernet0/1
```

> **ℹ️ Note:** R2 sees both R1's (10.0.0.0) and R3's (20.0.0.0) networks directly.

---

### Step 23: Check R3 Routing Table

**What:** Verify R3 learned remote networks via RIP.

**Commands:**
```bash
show ip route rip
```

**Expected Output:**
```
     10.0.0.0/24 [120/3] via 172.1.1.1, 00:00:14, GigabitEthernet0/0
     192.168.1.0/24 [120/2] via 172.1.1.1, 00:00:14, GigabitEthernet0/0
C    172.1.1.0/24 is directly connected, GigabitEthernet0/0
C    20.0.0.0/24 is directly connected, GigabitEthernet0/1
```

> **✅ Checkpoint:** All routers have complete RIP routing tables!

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **No RIP routes in table** | RIP not enabled or not advertised | Check `router rip` config; verify `network` commands |
| **Routes showing as static, not RIP** | RIP process didn't start | Verify RIP version 2 set; check router memory |
| **Ping fails between PCs** | Routes not converged yet | Wait 2-3 minutes; check route learning with `debug ip rip` |
| **Routing loops detected** | Split horizon not working | Enable `no validate-update-source`; check link integrity |
| **Only seeing some networks** | Missing network statements | Add all connected networks to `network` commands |
| **RIP updates not being sent** | Interface passive or disabled | Check interface status with `show ip interface brief` |
| **High convergence time** | Timers not optimized | Reduce invalid/holddown timers for faster detection |

### Debugging Commands

```bash
# View RIP process information
show ip protocols

# Debug RIP updates in real-time
debug ip rip

# Check current configuration
show running-config | include router rip -A 10

# Verify interface status
show ip interface brief

# Check route source and metric
show ip route rip

# View RIP neighbors
show ip rip database

# Turn off debugging
undebug all
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured RIPv2 on three routers
- ✅ Automatically learned routes across the network
- ✅ Implemented loop prevention mechanisms
- ✅ Configured RIP timers for convergence optimization
- ✅ Tested multi-hop connectivity through RIP network
- ✅ Verified routing tables and route metrics
- ✅ Traced packet paths through RIP topology

### Key Concepts Learned

| Concept | Description | Key Takeaway |
|---------|-------------|--------------|
| **Distance-Vector Routing** | Routes learned from neighbors' perspectives | Simple but less scalable |
| **Metric (Hop Count)** | Number of routers to reach destination | Lower = better path |
| **Route Advertisement** | Periodic updates every 30 seconds | Overhead but resilient |
| **Split Horizon** | Don't send routes back to source | Prevents routing loops |
| **Convergence Time** | Time for network to learn new topology | Affected by timers |
| **Administrative Distance** | RIP has AD of 120 (least trusted) | Low AD = higher priority |
| **RIPv2 vs RIPv1** | RIPv2 uses subnet masks (CIDR) | v2 is standard today |

### RIP Configuration Reference

```bash
# Enable RIP version 2
router rip
version 2

# Advertise connected networks
network 10.0.0.0
network 192.168.1.0

# Disable automatic summarization
no auto-summary

# Configure timers
timers basic 30 180 120 240

# Enable split horizon
no validate-update-source
```

### What's Next?

**Choose your next learning path:**

1. **🔀 RIP Enhancements:**
   - Configure passive interfaces to stop sending updates
   - Implement RIP authentication for security
   - Configure route filters and access lists

2. **🏆 OSPF (Open Shortest Path First):**
   - Configure OSPF for faster convergence
   - Understand link-state routing
   - Learn about OSPF areas and network hierarchies

3. **🔄 EIGRP (Enhanced Interior Gateway Routing Protocol):**
   - Configure EIGRP for better scalability
   - Understand DUAL algorithm
   - Learn about feasible successors

4. **🌐 BGP (Border Gateway Protocol):**
   - Configure external routing between AS
   - Understand path attributes
   - Learn inter-autonomous system routing

---

## 📚 Useful Resources

- [Cisco RIPv2 Configuration Guide](https://www.cisco.com/c/en/us/td/docs/routers/access/800/software/configuration/guide/b_800_cg/b_800_cg_chapter_011.pdf)
- [RIP Protocol Overview](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)
- [RIP Timers and Convergence](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/8651-3.html)
- [RIP Loop Prevention (Split Horizon)](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13696-43.html)
- [RFC 2453 - RIPv2 Specification](https://tools.ietf.org/html/rfc2453)
- [EVE-NG Documentation](https://www.eve-ng.net/index.php/documentation/)

---

## 💡 Best Practices & Tips

### ✅ RIP Best Practices

- **Use RIPv2:** Always use version 2 (v1 is obsolete)
- **Disable Auto-Summary:** Use `no auto-summary` for CIDR support
- **Set Passive Interfaces:** Stop sending updates on access links
- **Verify Convergence:** Wait 2-3 minutes before troubleshooting
- **Monitor Updates:** Use `debug ip rip` to verify route propagation
- **Document Timers:** Record any non-default timer values
- **Plan for Growth:** RIP has 15-hop maximum limit

### 🔐 RIP Security Considerations

- **Enable RIP Authentication:** Add MD5 authentication on backbone links
- **Use Passive Interfaces:** Prevent rogue RIP updates on edge networks
- **Implement Route Filters:** Restrict which routes are advertised
- **Monitor for Loops:** Use `debug ip rip` to detect loop conditions
- **Change Default Timers:** Slower timers reduce security attack surface
- **Segregate RIP Domains:** Use VLANs to isolate RIP traffic

### ⚡ RIP Performance Optimization

- **Reduce Update Frequency:** Extend timers on stable networks
- **Use Passive Interfaces:** Stop unnecessary update broadcasts
- **Implement Summarization:** Reduce routing table size
- **Limit Network Statements:** Only advertise necessary networks
- **Monitor Bandwidth:** RIP uses ~25KB per update per interface
- **Plan for Scalability:** RIP not suitable for large networks (15-hop limit)

### 🔧 Troubleshooting Tips

- **Check Timers First:** Convergence is timer-dependent
- **Verify Network Statements:** All interfaces must be included
- **Look for One-Way Links:** Asymmetric connectivity breaks RIP
- **Check for Loop Conditions:** Split horizon may not prevent all loops
- **Enable Debugging:** Use `debug ip rip` for real-time visibility
- **Compare Multiple Routers:** Check routing tables on all neighbors

---

✅ **RIP dynamic routing lab completed successfully!** 🎓

**Ready to explore faster routing protocols! 🚀**
