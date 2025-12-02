# 🌐 BGP Routing Lab

> Complete hands-on lab to configure Border Gateway Protocol (BGP) for inter-autonomous system routing with multiple IGP domains (EIGRP, RIP, OSPF) and route redistribution.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [IGP Configuration](#igp-configuration)
6. [Router IP Configuration](#router-ip-configuration)
7. [BGP Protocol Configuration](#bgp-protocol-configuration)
8. [Route Redistribution](#route-redistribution)
9. [Testing Connectivity](#testing-connectivity)
10. [Verification](#verification)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master external routing using BGP for inter-autonomous system communication with policy-based routing.

### By the end of this lab, you will:

- ✅ Understand BGP external routing protocol fundamentals
- ✅ Configure BGP on multiple routers in different autonomous systems
- ✅ Establish BGP peering relationships between AS domains
- ✅ Understand BGP attributes and path selection
- ✅ Configure route redistribution between IGP and BGP
- ✅ Implement redistribution between EIGRP, RIP, and OSPF domains
- ✅ Verify BGP neighbor relationships and route propagation
- ✅ Test connectivity between different autonomous systems
- ✅ Troubleshoot BGP adjacency and redistribution issues

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **EIGRP Fundamentals** | Understand IGP in AS | 11_EIGRP Lab |
| **RIP Fundamentals** | Understand IGP in AS | 09_RIP_Lab |
| **OSPF Fundamentals** | Understand IGP in AS | 10_OSPF_Lab |
| **IP Addressing** | Assign IPs to router interfaces | 04_Basic Switch Lab |
| **Router CLI** | Navigate router configuration | 03_Switch Config Lab |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco router images available (IOSv)
- ✅ Access to EVE-NG web interface
- ✅ Completed EIGRP, RIP, OSPF labs (recommended)

---

## 📊 Lab Topology

> **Purpose:** Visualize the multi-AS BGP network with three IGP domains.

![BGP Lab Topology](imgs/diagram.png)


### Topology Details

#### AS 65000 (EIGRP Domain)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | 10.10.1.1 | 255.255.255.252 | EIGRP link to R2 |
| **R1** | Gi0/2 | 5.0.17.1 | 255.255.255.252 | BGP link to R7 |
| **R1** | Gi0/3 | 5.0.14.1 | 255.255.255.252 | BGP link to R4 |
| **R2** | Gi0/0 | 10.10.1.2 | 255.255.255.252 | EIGRP link to R1 |
| **R2** | Gi0/1 | 10.10.2.1 | 255.255.255.252 | EIGRP link to R3 |
| **R3** | Gi0/0 | 10.10.2.2 | 255.255.255.252 | EIGRP link to R2 |

#### AS 65001 (RIP Domain)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R4** | Gi0/0 | 192.10.1.1 | 255.255.255.252 | RIP link to R5 |
| **R4** | Gi0/1 | 5.0.14.2 | 255.255.255.252 | BGP link to R1 |
| **R4** | Gi0/2 | 5.0.47.1 | 255.255.255.252 | BGP link to R7 |
| **R5** | Gi0/0 | 192.10.1.2 | 255.255.255.252 | RIP link to R4 |
| **R5** | Gi0/1 | 192.10.2.1 | 255.255.255.252 | RIP link to R6 |
| **R6** | Gi0/0 | 192.10.2.2 | 255.255.255.252 | RIP link to R5 |

#### AS 65002 (OSPF Domain)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R7** | Gi0/0 | 172.1.1.1 | 255.255.255.252 | OSPF link to R8 |
| **R7** | Gi0/1 | 5.0.17.2 | 255.255.255.252 | BGP link to R1 |
| **R7** | Gi0/2 | 5.0.47.2 | 255.255.255.252 | BGP link to R4 |
| **R8** | Gi0/0 | 172.1.1.2 | 255.255.255.252 | OSPF link to R7 |
| **R8** | Gi0/1 | 172.1.2.1 | 255.255.255.252 | OSPF link to R9 |
| **R9** | Gi0/0 | 172.1.2.2 | 255.255.255.252 | OSPF link to R8 |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the nine-router BGP network topology with three IGP domains in EVE-NG.

### Step 1: Create a New Lab

**What:** Create the lab project for BGP external routing.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Enter lab details:
   - **Lab Name**: `BGP_Lab`
   - **Lab Description**: `BGP Inter-AS Routing with Multiple IGP Domains`
   - **Lab Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Router Nodes

**What:** Add nine Cisco routers for BGP topology with three IGP domains.

**How to:**
1. Click **Add Node**
2. Select **Cisco** → **IOSv** (router)
3. Add nine routers:
   - **R1-R9** (one node at a time or adjust quantity)

> **ℹ️ Note:** Nine routers span three autonomous systems with BGP backbone.

---

### Step 3: Connect All Devices

**What:** Create IGP links and BGP backbone connections.

**Connection List:**
- R1 Gi0/0 ↔ R2 Gi0/0 (10.10.1.0/30 - EIGRP)
- R2 Gi0/1 ↔ R3 Gi0/0 (10.10.2.0/30 - EIGRP)
- R4 Gi0/0 ↔ R5 Gi0/0 (192.10.1.0/30 - RIP)
- R5 Gi0/1 ↔ R6 Gi0/0 (192.10.2.0/30 - RIP)
- R7 Gi0/0 ↔ R8 Gi0/0 (172.1.1.0/30 - OSPF)
- R8 Gi0/1 ↔ R9 Gi0/0 (172.1.2.0/30 - OSPF)
- R1 Gi0/3 ↔ R4 Gi0/1 (5.0.14.0/30 - BGP)
- R1 Gi0/2 ↔ R7 Gi0/1 (5.0.17.0/30 - BGP)
- R4 Gi0/2 ↔ R7 Gi0/2 (5.0.47.0/30 - BGP)

---

### Step 4: Start the Lab

**What:** Power on all devices.

**How to:**
1. Right-click on lab name
2. Select **Start Lab**
3. Wait for all routers to boot (5-8 minutes)
4. Verify all devices show green status

---

## ⚙️ IGP Configuration

> **Purpose:** Configure internal routing protocols in each AS domain.

### Step 5: Configure EIGRP (AS 65000)

**Configure on R1:**
```bash
enable
configure terminal
hostname R1
interface gigabitEthernet 0/0
ip address 10.10.1.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 5.0.17.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/3
ip address 5.0.14.1 255.255.255.252
no shutdown
exit

router eigrp 100
network 10.10.1.0 0.0.0.3
no auto-summary
exit
end
```

**Configure on R2 and R3:** (Similar with additional network statements)

> **ℹ️ Note:** EIGRP AS 100 handles R1-R3 connectivity.

---

### Step 6: Configure RIP (AS 65001)

**Configure on R4:**
```bash
enable
configure terminal
hostname R4
interface gigabitEthernet 0/0
ip address 192.10.1.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/1
ip address 5.0.14.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 5.0.47.1 255.255.255.252
no shutdown
exit

router rip
version 2
network 192.10.1.0
no auto-summary
exit
end
```

**Configure on R5 and R6:** (Similar with additional networks)

> **ℹ️ Note:** RIP v2 handles R4-R6 connectivity.

---

### Step 7: Configure OSPF (AS 65002)

**Configure on R7:**
```bash
enable
configure terminal
hostname R7
interface gigabitEthernet 0/0
ip address 172.1.1.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/1
ip address 5.0.17.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 5.0.47.2 255.255.255.252
no shutdown
exit

router ospf 1
network 172.1.1.0 0.0.0.3 area 0
exit
end
```

**Configure on R8 and R9:** (Similar with additional networks)

> **✅ Checkpoint:** All IGP domains are now configured and converged.

---

## ⚙️ Router IP Configuration

> **Purpose:** All router IPs are configured in IGP sections above. This section shows BGP-specific details.

### Step 8: Verify IGP Connectivity

**What:** Ensure all routers within each AS can communicate.

**Commands:**
```bash
show ip route eigrp
show ip route rip
show ip route ospf
```

> **✅ Checkpoint:** Each AS has routes to all its networks via IGP.

---

## 🔄 BGP Protocol Configuration

> **Purpose:** Configure BGP peering between AS domains on backbone routers.

### Step 9: Enable BGP on R1 (AS 65000)

**What:** Configure R1 as BGP speaker for AS 65000.

**Commands:**
```bash
enable
configure terminal
router bgp 65000
neighbor 5.0.14.2 remote-as 65001
neighbor 5.0.17.2 remote-as 65002
exit
end
```

**Command Breakdown:**

| Command | Purpose |
|---------|---------|
| **router bgp 65000** | Enter BGP configuration for AS 65000 |
| **neighbor IP remote-as ASNUM** | Define BGP peer with its AS number |
| **5.0.14.2** | R4's BGP interface (AS 65001) |
| **5.0.17.2** | R7's BGP interface (AS 65002) |

> **ℹ️ Explanation:** R1 becomes BGP speaker connecting two external AS domains.

---

### Step 10: Enable BGP on R4 (AS 65001)

**What:** Configure R4 as BGP speaker for AS 65001.

**Commands:**
```bash
enable
configure terminal
router bgp 65001
neighbor 5.0.14.1 remote-as 65000
neighbor 5.0.47.2 remote-as 65002
exit
end
```

> **ℹ️ Note:** R4 peers with R1 (AS 65000) and R7 (AS 65002).

---

### Step 11: Enable BGP on R7 (AS 65002)

**What:** Configure R7 as BGP speaker for AS 65002.

**Commands:**
```bash
enable
configure terminal
router bgp 65002
neighbor 5.0.17.1 remote-as 65000
neighbor 5.0.47.1 remote-as 65001
exit
end
```

> **✅ Checkpoint:** BGP peering established between all three AS domains!

---

## 🔄 Route Redistribution

> **Purpose:** Inject IGP routes into BGP and vice versa for inter-AS communication.

### Step 12: Configure Redistribution on R1 (EIGRP → BGP)

**What:** Redistribute EIGRP routes into BGP for external advertisement.

**Commands:**
```bash
enable
configure terminal
router bgp 65000
redistribute eigrp 100
exit

router eigrp 100
redistribute bgp 65000 metric 100000 100 255 1 1500
exit
end
```

**Metric Breakdown:**
- **100000**: Bandwidth (in Kbps)
- **100**: Delay (in tens of microseconds)
- **255**: Reliability (0-255)
- **1**: Load (0-255)
- **1500**: MTU (bytes)

> **ℹ️ Note:** Bidirectional redistribution enables route exchange.

---

### Step 13: Configure Redistribution on R4 (RIP → BGP)

**What:** Redistribute RIP routes into BGP for external advertisement.

**Commands:**
```bash
enable
configure terminal
router bgp 65001
redistribute rip
exit

router rip
redistribute bgp 65001
default-information originate
exit
end
```

> **ℹ️ Note:** RIP will learn default route from BGP.

---

### Step 14: Configure Redistribution on R7 (OSPF → BGP)

**What:** Redistribute OSPF routes into BGP for external advertisement.

**Commands:**
```bash
enable
configure terminal
router bgp 65002
redistribute ospf 1
exit

router ospf 1
redistribute bgp 65002 subnets
exit
end
```

> **✅ Checkpoint:** All IGP routes are now advertised via BGP!

---

## 🔍 Testing Connectivity

> **Purpose:** Verify BGP routing is working correctly between AS domains.

### Step 15: Verify BGP Neighbors

**What:** Check BGP peer status and adjacency.

**Commands (on each backbone router):**
```bash
show ip bgp summary
show ip bgp neighbors
```

**Expected Output:**
```
BGP router identifier 5.0.14.1, local AS number 65000
BGP table version is 4, main routing table version 4

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
5.0.14.2        4 65001      15      15        4    0    0 00:10:24       4
5.0.17.2        4 65002      15      15        4    0    0 00:10:20       5
```

| Column | Meaning |
|--------|---------|
| **V** | BGP version (4 = IPv4) |
| **AS** | Remote autonomous system number |
| **State/PfxRcd** | Routes received from neighbor |

> **ℹ️ Note:** All neighbors should show routes received.

---

### Step 16: Test Connectivity R1 to R9

**What:** Ping from EIGRP domain (R1) to OSPF domain (R9).

**Commands (on R1):**
```bash
ping 172.1.2.2
```

**Expected Output:**
```
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 172.1.2.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), roundtrip min/avg/max = 10/12/15 ms
```

> **✅ Success:** Traffic flows through BGP backbone from AS 65000 to AS 65002!

---

### Step 17: Test Connectivity R3 to R6

**What:** Ping from EIGRP domain (R3) to RIP domain (R6).

**Commands (on R3):**
```bash
ping 192.10.2.2
```

> **✅ Success:** BGP redistributes routes between all IGP domains!

---

### Step 18: Test Connectivity R9 to R4

**What:** Verify return path from OSPF domain to RIP domain.

**Commands (on R9):**
```bash
ping 192.10.1.1
```

> **✅ Confirmed:** Bidirectional connectivity between all AS domains!

---

## ✔️ Verification

> **Purpose:** Confirm BGP routing tables and route propagation.

### Step 19: Check BGP Routing Table on R1

**What:** Verify BGP learned routes in routing table.

**Commands:**
```bash
show ip route bgp
show ip bgp
```

**Expected Output:**
```
B    192.10.0.0/24 [20/0] via 5.0.14.2, 00:15:34
B    172.1.0.0/24 [20/0] via 5.0.17.2, 00:15:30

BGP table version is 5, local router ID is 10.10.1.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, x best-external
Origin codes: i - IGP, e - EGP, ? - incomplete

   Network          Next Hop            Metric LocPrf Weight Path
*> 10.10.0.0/24    0.0.0.0                  0         32768 i
*  10.10.0.0/24    5.0.14.2                             0 65001 i
*> 192.10.0.0/24   5.0.14.2                 0             0 65001 i
*> 172.1.0.0/24    5.0.17.2                 0             0 65002 i
```

| Field | Meaning |
|-------|---------|
| **>** | Best path (selected for routing) |
| **Path** | AS numbers traversed to reach prefix |
| **LocPrf** | Local preference (higher = preferred) |
| **Weight** | Cisco proprietary (higher = preferred) |

---

### Step 20: Check BGP Attributes on R4

**What:** Verify BGP attribute values for route selection.

**Commands:**
```bash
show ip bgp 192.10.0.0/24
show ip bgp neighbors 5.0.14.1 advertised-routes
```

**Expected Output:**
```
BGP routing table entry for 192.10.0.0/24, version 3
Paths: (1 available, best #1, table default)
Flag: 0x820
  65001
    5.0.14.1 from 5.0.14.1 (5.0.14.1)
      Origin IGP, metric 0, localpref 100, valid, external, best
      rx pathid: 0, tx pathid: 0x0
```

> **✅ Checkpoint:** All BGP routes are learned and advertised!

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **BGP neighbors not forming** | AS mismatch or TCP 179 blocked | Check `neighbor remote-as`; verify connectivity |
| **No routes appearing in BGP table** | Redistribution not configured | Check `redistribute` commands on all routers |
| **Routes not reaching other AS** | Redistribution working but not reflecting | Verify `show ip route` includes redistributed routes |
| **Suboptimal path selection** | BGP attributes not tuned | Adjust local-preference or AS-path prepending |
| **Neighbors in Idle state** | TCP connection failed | Verify TCP 179 reachability; check IGP routes |
| **Routes not propagating to internal routers** | Internal redistribution missing | Configure `redistribute bgp` in IGP on all routers |

### Debugging Commands

```bash
# View BGP neighbor status
show ip bgp summary

# Check specific neighbor details
show ip bgp neighbors 5.0.14.2

# View all BGP routes
show ip bgp

# Check redistributed routes
show ip route eigrp
show ip route rip
show ip route ospf

# Debug BGP keepalives
debug ip bgp keepalives

# Debug BGP updates
debug ip bgp updates

# Turn off debugging
undebug all
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured EIGRP, RIP, and OSPF in separate autonomous systems
- ✅ Established BGP peering between AS domains
- ✅ Implemented route redistribution in all directions
- ✅ Verified BGP neighbor adjacencies and route exchange
- ✅ Tested connectivity across all AS domains
- ✅ Verified route propagation through BGP backbone
- ✅ Understood BGP attributes and route selection

### Key Concepts Learned

| Concept | Description | Key Takeaway |
|---------|-------------|--------------|
| **Autonomous System (AS)** | Independent network domain with own routing | Each AS has unique number (65000-65535) |
| **BGP Peering** | TCP connection for route exchange | AS peers exchange best paths |
| **Route Redistribution** | Inject IGP routes into BGP/vice versa | Enables inter-AS communication |
| **BGP Attributes** | Weights for path selection (AS-path, local-pref) | Used for traffic engineering |
| **External BGP (eBGP)** | BGP between different AS | Backbone routing protocol |
| **Administrative Distance** | BGP has AD of 20 (eBGP) or 200 (iBGP) | Preferred over IGP (90-120) |
| **Redistribution Filtering** | Control which routes are redistributed | Important for policy implementation |

### BGP Configuration Reference

```bash
# Enable BGP for AS
router bgp 65000

# Define BGP peer
neighbor 5.0.14.2 remote-as 65001

# Redistribute IGP into BGP
redistribute eigrp 100

# Redistribute BGP into IGP
router eigrp 100
redistribute bgp 65000 metric 100000 100 255 1 1500

# Set local preference
router bgp 65000
neighbor 5.0.14.2 route-map PREFER in

# Check BGP status
show ip bgp summary
```

### What's Next?

**Choose your next learning path:**

1. **🔀 BGP Advanced Configuration:**
   - Implement route filtering with prefix lists
   - Configure AS path prepending for traffic engineering
   - Set local preference for path manipulation
   - Implement BGP communities for policy

2. **🔄 BGP Security:**
   - Configure BGP authentication (MD5)
   - Implement route filtering and aggregation
   - Use prefix lists for route control
   - Configure maximum prefix limits

3. **🌐 Multi-Protocol BGP:**
   - Configure BGP for IPv6 (MP-BGP)
   - Support multicast routing with MBGP
   - Implement VPN over BGP (MPLS)

4. **⚡ Advanced Routing:**
   - Implement MPLS and traffic engineering
   - Configure Segment Routing (SR)
   - Deploy SDN-based routing

---

## 📚 Useful Resources

- [Cisco BGP Configuration Guide](https://www.cisco.com/c/en/us/td/docs/routers/access/800/software/configuration/guide/b_800_cg/b_800_cg_chapter_014.pdf)
- [BGP Protocol Overview](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/26634-bgp-over-isdn-backup.html)
- [BGP Path Selection Algorithm](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/13753-25.html)
- [BGP Route Redistribution](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/15986-1.html)
- [RFC 4271 - BGP Protocol Specification](https://tools.ietf.org/html/rfc4271)
- [EVE-NG Documentation](https://www.eve-ng.net/index.php/documentation/)

---

## 💡 Best Practices & Tips

### ✅ BGP Best Practices

- **Use Unique AS Numbers:** Each domain should have unique AS (65000-65535 private range)
- **Configure Router ID:** Set explicit router ID on all BGP routers
- **Monitor BGP Sessions:** Use `show ip bgp summary` regularly
- **Plan Redistribution:** Be careful with bidirectional redistribution (loops)
- **Use Route Filters:** Implement prefix lists to control route propagation
- **Document Peering:** Record all BGP neighbors and AS numbers
- **Test Failover:** Verify backup paths work correctly

### 🔐 BGP Security Considerations

- **Enable BGP Authentication:** Use MD5 to protect route updates
- **Implement Prefix Filters:** Only accept expected routes from neighbors
- **Set Maximum Prefixes:** Limit routes per neighbor to detect misconfiguration
- **Monitor AS Path:** Detect unexpected AS path manipulation
- **Use Route Maps:** Implement policy-based filtering
- **Control Route Redistribution:** Prevent route loops between IGP and BGP

### ⚡ BGP Performance Optimization

- **Summarize Routes:** Aggregate prefixes to reduce table size
- **Use Local Preference:** Direct traffic through preferred exit point
- **Implement Route Filtering:** Reduce memory and CPU usage
- **Monitor Convergence:** BGP convergence typically takes seconds
- **Tune Timers:** Adjust hello/dead timers for failover speed (default 3s/9s)
- **Use MPLS:** Implement traffic engineering for better path control

### 🔧 BGP Troubleshooting Tips

- **Check AS Numbers First:** Mismatched AS prevents neighbor formation
- **Verify TCP Connectivity:** BGP uses TCP port 179
- **Check Redistribution:** Ensure IGP routes reach BGP via `redistribute` command
- **Review Router ID:** Each router needs unique Router ID
- **Monitor Stability:** Look for route flaps indicating instability
- **Enable Debugging:** Use `debug ip bgp` for real-time troubleshooting
- **Check Policies:** Route maps and prefix lists may filter routes unexpectedly

---

✅ **BGP external routing lab completed successfully!** 🎓

**You have now mastered all major routing protocols! 🚀**
