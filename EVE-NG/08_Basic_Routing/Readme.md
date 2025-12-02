# 🛣️ Basic Routing Lab

> Complete hands-on lab to configure static routes and default routes for inter-network communication using Cisco routers.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [Router IP Configuration](#router-ip-configuration)
6. [Static Route Configuration](#static-route-configuration)
7. [Default Route Configuration](#default-route-configuration)
8. [PC Configuration](#pc-configuration)
9. [Testing Connectivity](#testing-connectivity)
10. [Verification](#verification)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master static routing and default routes for basic inter-network communication.

### By the end of this lab, you will:

- ✅ Configure IP addresses on router interfaces
- ✅ Create static routes between networks
- ✅ Configure default routes for unknown destinations
- ✅ Understand the difference between static and dynamic routing
- ✅ Verify routing tables and route selection
- ✅ Test end-to-end connectivity across multiple networks
- ✅ Troubleshoot routing problems using show commands

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **IP Addressing** | Assign IPs to router interfaces | 04_Basic Switch Lab |
| **Subnetting** | Understand network masks | 04_Basic Switch Lab |
| **Router CLI** | Navigate router configuration | 03_Switch Config Lab |
| **Ping Testing** | Verify connectivity | 04_Basic Switch Lab |
| **Layer 3 Concepts** | Understand routing fundamentals | Basic networking knowledge |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco router images available (IOSv)
- ✅ Virtual PC (VPCS) images available
- ✅ Access to EVE-NG web interface
- ✅ Completed 04_Basic Switch lab (recommended)

---

## 📊 Lab Topology

> **Purpose:** Visualize the multi-network routing topology.

![Basic Routing Lab Diagram](imgs/diagram.png)

### Topology Details

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | 192.168.1.1 | 255.255.255.0 | Link to R2 |
| **R1** | Gi0/1 | 10.0.0.1 | 255.255.255.0 | Access network |
| **R2** | Gi0/0 | 192.168.1.2 | 255.255.255.0 | Link to R1 |
| **R2** | Gi0/1 | 20.0.0.1 | 255.255.255.0 | Access network |
| **PC1** | eth0 | 10.0.0.10 | 255.255.255.0 | Access device |
| **PC2** | eth0 | 20.0.0.10 | 255.255.255.0 | Access device |

### Network Overview

```
PC1 (10.0.0.10)
    |
    | Gi0/1
    |
[R1: 10.0.0.1]
    |
    | Gi0/0 ─── Gi0/0 (192.168.1.x link)
    |
[R2: 20.0.0.1]
    |
    | Gi0/1
    |
PC2 (20.0.0.10)
```

---

## 🔧 Creating the Lab

> **Purpose:** Set up the multi-network routing topology in EVE-NG.

### Step 1: Create a New Lab

**What:** Create the lab project for basic routing.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Enter lab details:
   - **Lab Name**: `Basic_Routing_Lab`
   - **Lab Description**: `Static and Default Route Configuration`
   - **Lab Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Router Nodes

**What:** Add two Cisco routers for inter-network routing.

**How to:**
1. Click **Add Node**
2. Select **Cisco** → **IOSv** (router, not switch)
3. Configure:
   - **Node Name**: `R1`
   - **Quantity**: `1`
4. Click **Add**
5. Repeat for R2:
   - **Node Name**: `R2`
   - **Quantity**: `1`

> **ℹ️ Note:** Ensure you select router images, not switch images.

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

### Step 4: Connect PCs to Routers

**What:** Connect end devices to router access interfaces.

**How to:**
1. Click **Add Link** or drag cables
2. Create these connections:
   - PC1 → R1 Gi0/1 (access network 10.0.0.0/24)
   - PC2 → R2 Gi0/1 (access network 20.0.0.0/24)

---

### Step 5: Connect Routers Together

**What:** Create link between routers for inter-network communication.

**How to:**
1. Click **Add Link**
2. Connect: R1 Gi0/0 ↔ R2 Gi0/0 (backbone link 192.168.1.0/24)

> **⚠️ Important:** Both routers connect through their Gi0/0 interfaces on the backbone network.

---

### Step 6: Start the Lab

**What:** Power on all devices.

**How to:**
1. Right-click on lab name
2. Select **Start Lab**
3. Wait for routers to boot (3-5 minutes)
4. Verify all devices show green status

---

## ⚙️ Router IP Configuration

> **Purpose:** Configure IP addresses on all router interfaces.

### Step 7: Access R1 Console

**What:** Connect to the first router.

**How to:**
1. Right-click on **R1**
2. Select **Console**
3. Press **Enter** to see the prompt

---

### Step 8: Configure R1 Backbone Interface (Gi0/0)

**What:** Assign IP address to link toward R2.

**Commands:**
```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
```

**Expected Output:**
```
Router(config)#interface gigabitEthernet 0/0
Router(config-if)#ip address 192.168.1.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#
```

| Interface | Purpose | IP Address |
|-----------|---------|-----------|
| **Gi0/0** | Backbone link to R2 | 192.168.1.1 |

---

### Step 9: Configure R1 Access Interface (Gi0/1)

**What:** Assign IP address to access network (PC1 gateway).

**Commands:**
```bash
interface gigabitEthernet 0/1
ip address 10.0.0.1 255.255.255.0
no shutdown
exit
```

**Expected Output:**
```
Router(config)#interface gigabitEthernet 0/1
Router(config-if)#ip address 10.0.0.1 255.255.255.0
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#
```

> **ℹ️ Note:** This IP serves as the gateway for PC1 network.

---

### Step 10: Configure R2 Backbone Interface (Gi0/0)

**What:** Assign IP address on R2's backbone interface.

**How to:**
1. Right-click on **R2** and select **Console**

**Commands:**
```bash
enable
configure terminal
interface gigabitEthernet 0/0
ip address 192.168.1.2 255.255.255.0
no shutdown
exit
```

> **ℹ️ Note:** R2's backbone IP is 192.168.1.2 (same subnet as R1's 192.168.1.1).

---

### Step 11: Configure R2 Access Interface (Gi0/1)

**What:** Assign IP address to PC2's access network.

**Commands:**
```bash
interface gigabitEthernet 0/1
ip address 20.0.0.1 255.255.255.0
no shutdown
exit
```

> **✅ Checkpoint:** All router interfaces are now configured with IP addresses.

---

## 🛣️ Static Route Configuration

> **Purpose:** Create static routes to enable inter-network communication.

### Step 12: Configure Static Route on R1 (to reach PC2 network)

**What:** Tell R1 how to reach the 20.0.0.0/24 network.

**Commands (on R1):**
```bash
ip route 20.0.0.0 255.255.255.0 192.168.1.2
```

**Command Breakdown:**

| Component | Value | Purpose |
|-----------|-------|---------|
| **Destination Network** | 20.0.0.0 | Target network PC2 is on |
| **Subnet Mask** | 255.255.255.0 | /24 network |
| **Next Hop** | 192.168.1.2 | R2's IP address to reach target |

> **ℹ️ Explanation:** "To reach 20.0.0.0/24, send traffic to 192.168.1.2 (R2)."

---

### Step 13: Configure Static Route on R2 (to reach PC1 network)

**What:** Tell R2 how to reach the 10.0.0.0/24 network.

**Commands (on R2):**
```bash
ip route 10.0.0.0 255.255.255.0 192.168.1.1
```

> **ℹ️ Explanation:** "To reach 10.0.0.0/24, send traffic to 192.168.1.1 (R1)."

---

## 🌐 Default Route Configuration

> **Purpose:** Configure default routes for unknown destinations.

### Step 14: Configure Default Route on R1

**What:** Set default gateway for R1 (route for unknown destinations).

**Commands (on R1):**
```bash
ip route 0.0.0.0 0.0.0.0 192.168.1.2
```

**Command Breakdown:**

| Component | Value | Purpose |
|-----------|-------|---------|
| **Destination** | 0.0.0.0 | All destinations (wildcard) |
| **Mask** | 0.0.0.0 | Match everything |
| **Next Hop** | 192.168.1.2 | Send to R2 |

> **ℹ️ Explanation:** "If you don't know where to send traffic, forward to 192.168.1.2 (R2)."

---

### Step 15: Configure Default Route on R2

**What:** Set default gateway for R2 (route for unknown destinations).

**Commands (on R2):**
```bash
ip route 0.0.0.0 0.0.0.0 192.168.1.1
```

> **ℹ️ Note:** Default route provides backup routing if specific routes don't exist.

---

### Step 16: Save Router Configuration

**What:** Persist all routing configuration.

**Commands (on each router):**
```bash
end
write memory
```

> **✅ Checkpoint:** All static and default routes are configured and saved.

---

## 🌐 PC Configuration

> **Purpose:** Assign IP addresses to PCs in their respective networks.

### Step 17: Configure PC1

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

**Expected Output:**
```
PC1> show ip
NAME        : PC1
IP/MASK     : 10.0.0.10/24
GATEWAY     : 10.0.0.1
```

> **ℹ️ Note:** Gateway (10.0.0.1) is R1's access interface.

---

### Step 18: Configure PC2

**What:** Assign IP address to second PC with R2 as gateway.

**How to:**
1. Right-click on **PC2**
2. Select **Console**

**Commands:**
```bash
ip 20.0.0.10 255.255.255.0 20.0.0.1
```

> **ℹ️ Note:** Gateway (20.0.0.1) is R2's access interface.

---

## 🔍 Testing Connectivity

> **Purpose:** Verify routing is working correctly.

### Step 19: Test PC1 to PC2 (Through Routing)

**What:** Ping from PC1 (10.0.0.10) to PC2 (20.0.0.10) across routers.

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
Success rate is 100 percent (5/5), roundtrip min/avg/max = 2/3/5 ms
```

![Ping Result](imgs/ping.png)

> **✅ Success:** Routing is working! Traffic flows: PC1 → R1 → R2 → PC2

---

### Step 20: Test PC2 to PC1 (Reverse Direction)

**What:** Verify bidirectional routing.

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
Success rate is 100 percent (5/5), roundtrip min/avg/max = 2/3/5 ms
```

> **✅ Confirmed:** Bidirectional routing through both routers working!

---

## ✔️ Verification

> **Purpose:** Confirm routing configuration is correct.

### Step 21: Verify Routing Table on R1

**What:** Check that R1 knows how to reach all networks.

**Commands:**
```bash
show ip route
```

**Expected Output:**
```
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E - EGP, ei - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

Gateway of last resort is 192.168.1.2 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 192.168.1.2
C    10.0.0.0/24 is directly connected, GigabitEthernet0/1
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
S    20.0.0.0/24 [1/0] via 192.168.1.2
```

![Router R1 Routes](imgs/r1.png)

| Route | Code | Meaning |
|-------|------|---------|
| **0.0.0.0/0** | S* | Static default route |
| **10.0.0.0/24** | C | Connected (directly attached) |
| **192.168.1.0/24** | C | Connected (directly attached) |
| **20.0.0.0/24** | S | Static route via R2 |

---

### Step 22: Verify Routing Table on R2

**What:** Check that R2 knows how to reach all networks.

**Commands:**
```bash
show ip route
```

**Expected Output:**
```
Gateway of last resort is 192.168.1.1 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 192.168.1.1
C    20.0.0.0/24 is directly connected, GigabitEthernet0/1
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
S    10.0.0.0/24 [1/0] via 192.168.1.1
```

![Router R2 Routes](imgs/r2.png)

> **✅ Checkpoint:** Both routers have complete routing tables with all routes!

---

### Step 23: Test Route with Traceroute

**What:** Visualize the path packets take between networks.

**Commands (from PC1):**
```bash
trace 20.0.0.10
```

**Expected Output:**
```
PC1>trace 20.0.0.10
trace to 20.0.0.10, 8 hops max, press Ctrl+C to stop
 1   10.0.0.1        1.234 ms  1.045 ms  0.987 ms
 2   20.0.0.1        2.456 ms  2.123 ms  2.789 ms
 3   20.0.0.10       3.567 ms  3.234 ms  3.891 ms
```

| Hop | IP | Description |
|-----|----|----|
| **1** | 10.0.0.1 | R1 (gateway) |
| **2** | 20.0.0.1 | R2 (remote gateway) |
| **3** | 20.0.0.10 | PC2 (destination) |

> **ℹ️ Note:** Traceroute shows the path: PC1 → R1 → R2 → PC2

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **Ping fails between networks** | Route not configured or interfaces down | Check `show ip route`; verify interfaces with `show ip interface brief` |
| **One-way connectivity only** | Route missing on return path | Configure return static route on other router |
| **Can't ping router IP directly** | Interface not up or IP misconfigured | Verify with `show ip interface` and `show running-config` |
| **Routing table shows no routes** | No routes configured yet | Add static routes and save configuration |
| **PC can't reach gateway** | PC IP in wrong subnet or wrong gateway | Verify PC IP matches network; check gateway IP |
| **High ping latency** | Router CPU overload or link issues | Check router resource usage; verify physical links |

### Quick Verification Commands

```bash
# Show all routes
show ip route

# Show specific interface status
show ip interface brief

# Show interface details
show interface gigabitEthernet 0/0

# Check running configuration
show running-config

# Test connectivity
ping <destination>

# Trace route to destination
trace <destination>

# Check routing table for specific network
show ip route 20.0.0.0
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured IP addresses on all router interfaces
- ✅ Created static routes for inter-network communication
- ✅ Configured default routes for unknown destinations
- ✅ Set up PC IP addresses with proper gateway configuration
- ✅ Tested end-to-end connectivity between networks
- ✅ Verified routing tables and route selection
- ✅ Traced packet paths between networks

### Key Concepts Learned

| Concept | Description | Key Takeaway |
|---------|-------------|--------------|
| **Static Routing** | Manually configured routes to specific networks | Deterministic but requires manual updates |
| **Default Route** | Route for all unknown destinations (0.0.0.0/0) | Provides fallback routing |
| **Next Hop** | Router IP address to forward traffic toward | Must be directly reachable |
| **Route Precedence** | Specific routes preferred over default routes | More specific = higher priority |
| **Connected Routes** | Networks directly attached to router interfaces | Automatically added by router |
| **Administrative Distance** | Trustworthiness of routing source (1 = static) | Lower AD = higher priority |

### Basic Routing Configuration Reference

```bash
# Configure IP on interface
interface Gi0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

# Add static route
ip route 20.0.0.0 255.255.255.0 192.168.1.2

# Add default route
ip route 0.0.0.0 0.0.0.0 192.168.1.2

# Verify routing
show ip route
show ip interface brief
```

### What's Next?

**Choose your next learning path:**

1. **🔀 Dynamic Routing (RIP):**
   - Configure RIP (Routing Information Protocol)
   - Understand distance-vector routing
   - Learn about routing advertisements

2. **🏆 OSPF (Open Shortest Path First):**
   - Configure OSPF for larger networks
   - Understand link-state routing
   - Learn about areas and cost calculation

3. **🔄 EIGRP (Enhanced Interior Gateway Routing Protocol):**
   - Configure EIGRP on multiple routers
   - Understand hybrid routing
   - Learn about DUAL algorithm

4. **🌐 BGP (Border Gateway Protocol):**
   - Configure external routing
   - Understand AS path and attributes
   - Learn inter-autonomous system routing

---

## 📚 Useful Resources

- [Cisco Static Routing Guide](https://www.cisco.com/c/en/us/td/docs/routers/access/800/software/configuration/guide/b_800_cg/b_800_cg_chapter_010.pdf)
- [IP Routing Fundamentals](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)
- [Route Selection Process](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/8651-3.html)
- [Default Route Configuration](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/200711-BGP-Default-Route-Advertisement.html)
- [EVE-NG Documentation](https://www.eve-ng.net/index.php/documentation/)

---

## 💡 Best Practices & Tips

### ✅ Static Routing Best Practices

- **Use Specific Routes:** Create routes for each network, not just defaults
- **Document All Routes:** Maintain record of all static routes
- **Test After Changes:** Verify connectivity after adding routes
- **Use Consistent Subnets:** Standard /24 networks easier to manage
- **Plan for Growth:** Use hierarchical IP addressing scheme
- **Avoid Routing Loops:** Ensure no conflicting route configurations

### 🔐 Routing Security

- **Disable Default Routes if Possible:** Reduces attack surface
- **Implement Access Control Lists (ACLs):** Restrict route propagation
- **Enable Authentication:** Use MD5 for routing protocol authentication
- **Monitor Routing Changes:** Alert on unexpected route modifications
- **Use Redundant Paths:** Provide failover but with specific metrics

### ⚡ Routing Optimization

- **Use Metric Values:** Set priorities for redundant paths
- **Optimize Path Selection:** Choose links with best performance
- **Balance Load:** Distribute traffic across multiple paths
- **Plan Capacity:** Ensure links can handle expected traffic
- **Monitor Route Usage:** Check which routes carry traffic

---

✅ **Basic routing lab completed successfully!** 🎓

**Ready to explore dynamic routing protocols! 🚀**