# 📝 Comprehensive Networking Exam Lab

> Practical assessment combining VLAN, Inter-VLAN Routing, and OSPF dynamic routing in a multi-domain network with six PCs across three domains.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [Network Configuration](#network-configuration)
6. [Switch VLAN Configuration](#switch-vlan-configuration)
7. [Router Inter-VLAN Configuration](#router-inter-vlan-configuration)
8. [OSPF Dynamic Routing](#ospf-dynamic-routing)
9. [PC Configuration](#pc-configuration)
10. [Testing Connectivity](#testing-connectivity)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Assess comprehensive understanding of VLAN segmentation, inter-VLAN routing, and dynamic routing protocols.

### By the end of this lab, you will:

- ✅ Configure VLANs on multiple switches with logical naming
- ✅ Implement inter-VLAN routing using router subinterfaces
- ✅ Configure OSPF dynamic routing protocol
- ✅ Establish IP addressing scheme based on student identification
- ✅ Configure trunk links between switches and routers
- ✅ Verify connectivity within and across VLANs
- ✅ Test communication between all PC endpoints
- ✅ Troubleshoot connectivity issues using proper commands
- ✅ Document network configuration and validation results

---

## ✅ Prerequisites

> **Purpose:** Ensure knowledge of foundational concepts and available resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **VLAN Fundamentals** | Segmentation of networks | 05_VLAN Lab |
| **Inter-VLAN Routing** | Communication across VLANs | 06_Inter_VLAN Lab |
| **OSPF Routing** | Dynamic routing configuration | 10_OSPF_Lab Lab |
| **IP Addressing** | Network addressing and subnetting | 04_Basic Switch Lab |
| **Router CLI** | Configure routers and interfaces | 03_Switch Config Lab |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco IOSv router and switch images
- ✅ VPCS (virtual PC simulator) available
- ✅ Access to EVE-NG web interface
- ✅ Text editor for documentation

---

## 📊 Lab Topology

> **Purpose:** Visualize the three-domain network with VLAN segmentation and OSPF backbone.

### Network Structure Overview

```
┌──────────────────────┐       ┌──────────────────────┐       ┌──────────────────────┐
│    Domain 1 (SW1)    │       │    Domain 2 (SW2)    │       │    Domain 3 (SW3)    │
│  ┌────────────────┐  │       │  ┌────────────────┐  │       │  ┌────────────────┐  │
│  │ PC11           │  │       │  │ PC21           │  │       │  │ PC31           │  │
│  │ VLAN 1XX       │  │       │  │ VLAN 1XX       │  │       │  │ VLAN 1XX       │  │
│  └────────┬───────┘  │       │  └────────┬───────┘  │       │  └────────┬───────┘  │
│  ┌────────┴───────┐  │       │  ┌────────┴───────┐  │       │  ┌────────┴───────┐  │
│  │ PC12           │  │       │  │ PC22           │  │       │  │ PC32           │  │
│  │ VLAN 2XX       │  │       │  │ VLAN 2XX       │  │       │  │ VLAN 2XX       │  │
│  └────────┬───────┘  │       │  └────────┬───────┘  │       │  └────────┬───────┘  │
│           │          │       │           │          │       │           │          │
│           │                  │           │                  │           │          │
│      Trunk │                 │      Trunk │                 │      Trunk │          │
│           │                  │           │                  │           │          │
│        ┌──┴────┐          ┌──┴────┐       │              ┌──┴────┐      │          │
└────────│  SW1  │──────────│  R1   │───────┴──────────────│  R2   │──────┴──────────┘
         └───────┘          └────┬──┘                       └───┬───┘
                                 │                             │
                         OSPF Backbone Link (Area 0)
```

### Detailed Topology Information

**Topology Components:**
- 🌐 **3 Switches** (SW1, SW2, SW3) for VLAN segmentation
- 🔀 **3 Routers** (R1, R2, R3) for inter-domain routing
- 🖥️ **6 PCs** (2 per domain) for connectivity testing
- 📡 **OSPF Area 0** backbone connecting routers
- 🔗 **Trunk Links** between switches and routers

### Network IP Addressing Scheme

> **Note:** XX = Student ID Number (01-08)

#### Domain 1 Addressing (SW1 ↔ R1)

| Device | Interface | IP Address | Subnet | VLAN | Purpose |
|--------|-----------|-----------|--------|------|---------|
| **PC11** | - | 10.1.XX.10 | /24 | 1XX | First VLAN PC |
| **PC12** | - | 10.2.XX.20 | /24 | 2XX | Second VLAN PC |
| **R1** | Gi0/0.1XX | 10.1.XX.1 | /24 | 1XX | Default Gateway for VLAN 1XX |
| **R1** | Gi0/0.2XX | 10.2.XX.1 | /24 | 2XX | Default Gateway for VLAN 2XX |

#### Domain 2 Addressing (SW2 ↔ R2)

| Device | Interface | IP Address | Subnet | VLAN | Purpose |
|--------|-----------|-----------|--------|------|---------|
| **PC21** | - | 20.1.XX.10 | /24 | 1XX | First VLAN PC |
| **PC22** | - | 20.2.XX.20 | /24 | 2XX | Second VLAN PC |
| **R2** | Gi0/0.1XX | 20.1.XX.1 | /24 | 1XX | Default Gateway for VLAN 1XX |
| **R2** | Gi0/0.2XX | 20.2.XX.1 | /24 | 2XX | Default Gateway for VLAN 2XX |

#### Domain 3 Addressing (SW3 ↔ R3)

| Device | Interface | IP Address | Subnet | VLAN | Purpose |
|--------|-----------|-----------|--------|------|---------|
| **PC31** | - | 30.1.XX.10 | /24 | 1XX | First VLAN PC |
| **PC32** | - | 30.2.XX.20 | /24 | 2XX | Second VLAN PC |
| **R3** | Gi0/0.1XX | 30.1.XX.1 | /24 | 1XX | Default Gateway for VLAN 1XX |
| **R3** | Gi0/0.2XX | 30.2.XX.1 | /24 | 2XX | Default Gateway for VLAN 2XX |

#### OSPF Backbone Links

| Router | Interface | IP Address | Subnet | Link |
|--------|-----------|-----------|--------|------|
| **R1** | Gi0/1 | 1.1.XX.1 | /30 | R1-R2 |
| **R2** | Gi0/1 | 1.1.XX.2 | /30 | R1-R2 |
| **R2** | Gi0/2 | 2.2.XX.1 | /30 | R2-R3 |
| **R3** | Gi0/2 | 2.2.XX.2 | /30 | R2-R3 |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the practical exam topology with three switches, three routers, and six PCs.

### Step 1: Create Lab Project

**What:** Create EVE-NG lab for comprehensive networking exam.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Configure:
   - **Lab Name**: `EXAM_Lab`
   - **Description**: `Comprehensive Networking Exam with VLAN and OSPF`
   - **Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Network Devices

**What:** Add all required switches, routers, and PCs for exam topology.

**How to:**
1. Click **Add Node** multiple times:
   - **Switches**: SW1, SW2, SW3 (3x Cisco IOSv L3 or L2)
   - **Routers**: R1, R2, R3 (3x Cisco IOSv)
   - **PCs**: PC11, PC12, PC21, PC22, PC31, PC32 (6x VPCS)

> **ℹ️ Note:** Total of 12 network nodes across three domains.

---

### Step 3: Connect All Devices

**What:** Create all required connections as per topology.

**Domain 1 Connections:**
- PC11 → SW1 Gi0/1
- PC12 → SW1 Gi0/2
- SW1 Gi0/0 ↔ R1 Gi0/0 (Trunk)

**Domain 2 Connections:**
- PC21 → SW2 Gi0/1
- PC22 → SW2 Gi0/2
- SW2 Gi0/0 ↔ R2 Gi0/0 (Trunk)

**Domain 3 Connections:**
- PC31 → SW3 Gi0/1
- PC32 → SW3 Gi0/2
- SW3 Gi0/0 ↔ R3 Gi0/0 (Trunk)

**OSPF Backbone Links:**
- R1 Gi0/1 ↔ R2 Gi0/1
- R2 Gi0/2 ↔ R3 Gi0/2

---

### Step 4: Start Lab

**What:** Power on all devices and verify connectivity.

**How to:**
1. Right-click lab name
2. Select **Start Lab**
3. Wait for all devices to boot (8-10 minutes)
4. Verify all nodes show green status

---

## ⚙️ Network Configuration

> **Purpose:** Configure basic IP addressing on router backbone links.

### Step 5: Configure R1 Backbone Link

**What:** Configure R1 Gi0/1 interface for OSPF backbone.

**Commands:**
```bash
enable
configure terminal
hostname R1

interface gigabitEthernet 0/1
ip address 1.1.XX.1 255.255.255.252
no shutdown
exit

end
```

> **ℹ️ Note:** Replace XX with student ID (01-08).

---

### Step 6: Configure R2 Backbone Links

**What:** Configure R2 interfaces for OSPF backbone connectivity.

**Commands:**
```bash
enable
configure terminal
hostname R2

interface gigabitEthernet 0/1
ip address 1.1.XX.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 2.2.XX.1 255.255.255.252
no shutdown
exit

end
```

---

### Step 7: Configure R3 Backbone Link

**What:** Configure R3 Gi0/2 interface for OSPF backbone.

**Commands:**
```bash
enable
configure terminal
hostname R3

interface gigabitEthernet 0/2
ip address 2.2.XX.2 255.255.255.252
no shutdown
exit

end
```

> **✅ Checkpoint:** All backbone links configured and operational.

---

## 🏢 Switch VLAN Configuration

> **Purpose:** Create VLANs for logical network segmentation.

### Step 8: Configure VLAN on SW1

**What:** Create VLAN 1XX (student first name) and VLAN 2XX (student last name).

**Commands:**
```bash
enable
configure terminal

vlan 1XX
name FIRSTNAME
exit

vlan 2XX
name LASTNAME
exit

interface gigabitEthernet 1/1
switchport mode access
switchport access vlan 1XX
exit

interface gigabitEthernet 1/2
switchport mode access
switchport access vlan 2XX
exit

interface gigabitEthernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 1XX,2XX
exit

end
```

**VLAN Naming Examples:**
- Student 01: VLAN 101 name = **THANATORN**, VLAN 201 name = **ONTONGLANG**
- Student 02: VLAN 102 name = **FIRSTNAME**, VLAN 202 name = **LASTNAME**

---

### Step 9: Configure VLAN on SW2

**What:** Replicate VLAN configuration on second switch.

**Commands:** (Same as Step 8 for SW2)

> **ℹ️ Note:** Use same VLAN IDs (1XX, 2XX) across all switches for consistency.

---

### Step 10: Configure VLAN on SW3

**What:** Replicate VLAN configuration on third switch.

**Commands:** (Same as Step 8 for SW3)

> **✅ Checkpoint:** All switches have matching VLAN configuration.

---

## 🔀 Router Inter-VLAN Configuration

> **Purpose:** Configure router subinterfaces for inter-VLAN routing.

### Step 11: Configure R1 Subinterfaces

**What:** Create subinterfaces on R1 Gi0/0 for VLAN 1XX and 2XX.

**Commands:**
```bash
enable
configure terminal

interface gigabitEthernet 0/0
no ip address
no shutdown
exit

interface gigabitEthernet 0/0.1XX
encapsulation dot1Q 1XX
ip address 10.1.XX.1 255.255.255.0
exit

interface gigabitEthernet 0/0.2XX
encapsulation dot1Q 2XX
ip address 10.2.XX.1 255.255.255.0
exit

end
```

**Verification:**
```bash
show ip interface brief
show interfaces gigabitEthernet 0/0.1XX
```

---

### Step 12: Configure R2 Subinterfaces

**What:** Create subinterfaces on R2 Gi0/0 for Domain 2 VLANs.

**Commands:**
```bash
enable
configure terminal

interface gigabitEthernet 0/0
no ip address
no shutdown
exit

interface gigabitEthernet 0/0.1XX
encapsulation dot1Q 1XX
ip address 20.1.XX.1 255.255.255.0
exit

interface gigabitEthernet 0/0.2XX
encapsulation dot1Q 2XX
ip address 20.2.XX.1 255.255.255.0
exit

end
```

---

### Step 13: Configure R3 Subinterfaces

**What:** Create subinterfaces on R3 Gi0/0 for Domain 3 VLANs.

**Commands:**
```bash
enable
configure terminal

interface gigabitEthernet 0/0
no ip address
no shutdown
exit

interface gigabitEthernet 0/0.1XX
encapsulation dot1Q 1XX
ip address 30.1.XX.1 255.255.255.0
exit

interface gigabitEthernet 0/0.2XX
encapsulation dot1Q 2XX
ip address 30.2.XX.1 255.255.255.0
exit

end
```

> **✅ Checkpoint:** All routers have subinterfaces configured for inter-VLAN routing.

---

## 📡 OSPF Dynamic Routing

> **Purpose:** Configure OSPF routing protocol to enable inter-domain communication.

### Step 14: Configure OSPF on R1

**What:** Enable OSPF on R1 with all network prefixes.

**Commands:**
```bash
enable
configure terminal

router ospf 1
network 10.1.XX.0 0.0.0.255 area 0
network 10.2.XX.0 0.0.0.255 area 0
network 1.1.XX.0 0.0.0.3 area 0
exit

end
```

**Verification:**
```bash
show ip ospf neighbor
show ip ospf interface brief
show ip protocols
```

> **ℹ️ Note:** Verify ospf neighbor adjacency with R2.

---

### Step 15: Configure OSPF on R2

**What:** Enable OSPF on R2 with all network prefixes.

**Commands:**
```bash
enable
configure terminal

router ospf 1
network 20.1.XX.0 0.0.0.255 area 0
network 20.2.XX.0 0.0.0.255 area 0
network 1.1.XX.0 0.0.0.3 area 0
network 2.2.XX.0 0.0.0.3 area 0
exit

end
```

> **ℹ️ Note:** R2 is central hub connecting R1 and R3.

---

### Step 16: Configure OSPF on R3

**What:** Enable OSPF on R3 with all network prefixes.

**Commands:**
```bash
enable
configure terminal

router ospf 1
network 30.1.XX.0 0.0.0.255 area 0
network 30.2.XX.0 0.0.0.255 area 0
network 2.2.XX.0 0.0.0.3 area 0
exit

end
```

> **✅ Checkpoint:** All OSPF routers have formed adjacencies and converged!

---

## 🖥️ PC Configuration

> **Purpose:** Configure IP addressing on all PCs.

### Step 17: Configure PC11 (Domain 1, VLAN 1XX)

**What:** Set IP address for PC11 in VLAN 1XX.

**Commands:**
```bash
# For VPCS
ip 10.1.XX.10 10.1.XX.1 24
show ip
```

> **ℹ️ Note:** Replace XX with student ID. Example for student 01: `ip 10.1.1.10 10.1.1.1 24`

---

### Step 18: Configure PC12 (Domain 1, VLAN 2XX)

**What:** Set IP address for PC12 in VLAN 2XX.

**Commands:**
```bash
ip 10.2.XX.20 10.2.XX.1 24
show ip
```

---

### Step 19: Configure PC21 and PC22 (Domain 2)

**What:** Set IP addresses for PCs in Domain 2.

**Commands (PC21):**
```bash
ip 20.1.XX.10 20.1.XX.1 24
```

**Commands (PC22):**
```bash
ip 20.2.XX.20 20.2.XX.1 24
```

---

### Step 20: Configure PC31 and PC32 (Domain 3)

**What:** Set IP addresses for PCs in Domain 3.

**Commands (PC31):**
```bash
ip 30.1.XX.10 30.1.XX.1 24
```

**Commands (PC32):**
```bash
ip 30.2.XX.20 30.2.XX.1 24
```

> **✅ Checkpoint:** All PCs have IP addresses configured and can reach their gateways.

---

## 🔍 Testing Connectivity

> **Purpose:** Verify connectivity within and across domains.

### Step 21: Test Within VLAN 1XX (First Name Domain)

**What:** Verify PC connectivity within first name VLAN across all domains.

**Test from PC11:**
```bash
ping 20.1.XX.10    # Should reach PC21
ping 30.1.XX.10    # Should reach PC31
```

**Expected Result:**
```
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echoes to 20.1.XX.10
!!!!!
Success rate is 100 percent (5/5), roundtrip min/avg/max = 10/12/15 ms
```

> **✅ Success:** VLAN 1XX communication works across all domains!

---

### Step 22: Test Within VLAN 2XX (Last Name Domain)

**What:** Verify PC connectivity within last name VLAN across all domains.

**Test from PC12:**
```bash
ping 20.2.XX.20    # Should reach PC22
ping 30.2.XX.20    # Should reach PC32
```

> **✅ Success:** VLAN 2XX communication works across all domains!

---

### Step 23: Test Cross-VLAN Communication

**What:** Verify PCs can reach hosts in different VLANs within same domain.

**Test from PC11 (VLAN 1XX):**
```bash
ping 10.2.XX.20    # Should reach PC12 (VLAN 2XX in Domain 1)
```

**Test from PC21 (VLAN 1XX):**
```bash
ping 20.2.XX.20    # Should reach PC22 (VLAN 2XX in Domain 2)
```

> **✅ Success:** Inter-VLAN routing is working correctly!

---

## ✔️ Verification

> **Purpose:** Document network configuration and validate all settings.

### Step 24: Verify VLAN Configuration

**What:** Check VLAN configuration on all switches.

**Commands (on each switch):**
```bash
show vlan brief
```

**Expected Output:**
```
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Gi0/1, Gi0/2, Gi0/3, Gi1/0, Gi1/1
1XX  FIRSTNAME                        active    Gi1/1
2XX  LASTNAME                         active    Gi1/2
```

### Step 25: Verify OSPF Convergence

**What:** Check OSPF neighbor status and routing table.

**Commands (on each router):**
```bash
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
```

**Expected OSPF Neighbor Output:**
```
Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.XX.1          1   FULL/BDR        00:00:37    1.1.XX.2        GigabitEthernet0/1
```

### Step 26: Verify Routing Tables

**What:** Confirm all routes are learned via OSPF.

**Commands (on R1):**
```bash
show ip route
```

**Expected Routes:**
```
O    20.1.XX.0/24 [110/21] via 1.1.XX.2, 00:10:34, GigabitEthernet0/1
O    20.2.XX.0/24 [110/21] via 1.1.XX.2, 00:10:34, GigabitEthernet0/1
O    30.1.XX.0/24 [110/31] via 1.1.XX.2, 00:10:34, GigabitEthernet0/1
O    30.2.XX.0/24 [110/31] via 1.1.XX.2, 00:10:34, GigabitEthernet0/1
O    2.2.XX.0/30 [110/20] via 1.1.XX.2, 00:10:34, GigabitEthernet0/1
```

### Step 27: Verify PC Connectivity End-to-End

**What:** Final validation of all connectivity across network.

**Full Connectivity Matrix:**
```bash
# From PC11: Should ping all other PCs
ping 10.2.XX.20    # PC12 (same domain, different VLAN)
ping 20.1.XX.10    # PC21 (different domain, same VLAN)
ping 20.2.XX.20    # PC22 (different domain, different VLAN)
ping 30.1.XX.10    # PC31 (different domain, same VLAN)
ping 30.2.XX.20    # PC32 (different domain, different VLAN)
```

> **✅ Confirmed:** Full end-to-end connectivity established across all three domains!

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **PC cannot ping gateway** | IP or gateway misconfigured | Verify `show ip` on PC; check router subinterface IP |
| **VLAN ports not responding** | Access port misconfigured | Check `show vlan brief`; verify access vlan assignment |
| **Trunk not working** | Trunk encapsulation missing | Verify `switchport trunk encapsulation dot1q` configured |
| **OSPF neighbors not forming** | Backbone link down | Check `show ip ospf neighbor`; verify backbone IPs configured |
| **Can ping gateway but not other domains** | OSPF routes not learned | Check `show ip route ospf`; verify OSPF network statements |
| **Cross-VLAN communication fails** | Subinterface routing disabled | Verify subinterface IPs with `show ip interface brief` |

### Debugging Commands

```bash
# Check VLAN status
show vlan brief
show interfaces trunk

# Check OSPF status
show ip ospf neighbor
show ip ospf interface brief
show ip protocols

# Check routing
show ip route
show ip route ospf

# Check interface configuration
show ip interface brief
show interfaces gigabitEthernet 0/0.1XX

# Enable debugging (if needed)
debug ip ospf hello
debug ip ospf adjacency
undebug all
```

---

## ✅ Summary & Next Steps

### Lab Completion Checklist

**Congratulations!** You have successfully:

- ✅ Configured VLAN segmentation on three switches with proper naming
- ✅ Implemented inter-VLAN routing on three routers using subinterfaces
- ✅ Configured OSPF dynamic routing to connect all three domains
- ✅ Assigned IP addresses following student identification scheme
- ✅ Verified VLAN functionality and trunk configuration
- ✅ Verified OSPF neighbor adjacencies and route propagation
- ✅ Tested end-to-end connectivity across all domains
- ✅ Validated inter-VLAN and inter-domain communication
- ✅ Documented network configuration and validation results

### Key Concepts Assessed

| Concept | Description | Key Takeaway |
|---------|-------------|--------------|
| **VLAN Segmentation** | Logical network separation | VLANs isolate broadcast domains by ID |
| **Trunk Encapsulation** | Multiplexing multiple VLANs | 802.1Q tagging enables trunk links |
| **Inter-VLAN Routing** | Communication across VLANs | Subinterfaces on routers enable routing |
| **Subinterface Configuration** | Creating virtual router interfaces | One physical link, multiple logical interfaces |
| **OSPF Adjacency** | Router peer relationship | OSPF uses hello protocol for neighbor formation |
| **Route Advertisement** | OSPF network statements | Routers announce configured networks to neighbors |
| **IP Addressing Scheme** | Logical address planning | Consistent numbering simplifies management |
| **Connectivity Testing** | Ping for network validation | ICMP tests layer 3 reachability |

### Configuration Reference

**Quick Reference - Replace XX with Student ID:**

```bash
# VLAN Configuration (on each switch)
vlan 1XX
name FIRSTNAME
vlan 2XX
name LASTNAME

# Trunk Configuration
interface Gi0/0
switchport trunk allowed vlan 1XX,2XX
switchport mode trunk

# Router Subinterfaces
interface Gi0/0.1XX
encapsulation dot1Q 1XX
ip address 10.1.XX.1 255.255.255.0

interface Gi0/0.2XX
encapsulation dot1Q 2XX
ip address 10.2.XX.1 255.255.255.0

# OSPF Configuration
router ospf 1
network 10.1.XX.0 0.0.0.255 area 0
network 10.2.XX.0 0.0.0.255 area 0
network 1.1.XX.0 0.0.0.3 area 0

# PC Configuration
ip 10.1.XX.10 10.1.XX.1 24
```

### What's Next?

**Recommended Advanced Topics:**

1. **🔒 Network Security:**
   - Configure Access Control Lists (ACL) for VLAN security
   - Implement port security on switch ports
   - Enable SSH on routers for secure management

2. **⚡ Advanced OSPF:**
   - Implement OSPF areas and summarization
   - Configure OSPF authentication (MD5)
   - Optimize OSPF timers for faster convergence

3. **🔀 Redundancy & Failover:**
   - Configure Spanning Tree Protocol (STP) for loop prevention
   - Implement Hot Standby Router Protocol (HSRP) for gateway redundancy
   - Test failover scenarios and recovery

4. **📊 Network Monitoring:**
   - Configure NetFlow for traffic analysis
   - Implement SNMP for device monitoring
   - Use syslog for centralized logging

---

## 📚 Useful Resources

- [Cisco VLAN Configuration Guide](https://www.cisco.com/c/en/us/support/docs/lan-switching-and-vlan/vlan-trunking-protocol-vtp/98156-vlan-faq.html)
- [Cisco OSPF Routing Guide](https://www.cisco.com/c/en/us/support/docs/ip/open-shortest-path-first-ospf/7039-1.html)
- [Inter-VLAN Routing Best Practices](https://www.cisco.com/c/en/us/support/docs/lan-switching-and-vlan/configure-vlan/10743-12.html)
- [EVE-NG Documentation](https://www.eve-ng.net/index.php/documentation/)
- [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-software/products-command-reference-list.html)

---

## 💡 Best Practices & Tips

### ✅ Network Design Best Practices

- **Plan IP Addressing:** Use consistent scheme across all domains (10.x, 20.x, 30.x)
- **Name Devices:** Clear hostname conventions aid troubleshooting
- **Document Everything:** Keep track of VLAN IDs, IP addresses, and router configurations
- **Verify Each Step:** Test configuration after each change to isolate issues
- **Use Consistent Subnets:** All domains use same subnet structure (/24 for VLANs, /30 for links)
- **Monitor Convergence:** Allow time for OSPF to converge before testing

### 🔐 Configuration Security

- **Access Control:** Implement ACLs to restrict management access
- **Device Authentication:** Configure console and enable passwords
- **Secure Channels:** Use SSH instead of Telnet for remote management
- **Regular Backups:** Save configurations regularly to restore quickly
- **Log Monitoring:** Enable and review device logs for issues

### ⚡ Performance Optimization

- **OSPF Tuning:** Adjust hello/dead intervals based on convergence needs
- **Bandwidth Optimization:** Monitor interface utilization
- **MTU Configuration:** Verify MTU size across links (typically 1500 bytes)
- **QoS Implementation:** Prioritize critical traffic if needed
- **Load Balancing:** Use multiple paths via OSPF cost adjustment

### 🔧 Troubleshooting Methodology

1. **Start with basics:** Verify physical connections and device status
2. **Check configuration:** Confirm settings match requirements
3. **Test incrementally:** Validate each component before proceeding
4. **Use systematic commands:** Progressively test using show commands
5. **Review outputs carefully:** Look for error messages and unexpected results
6. **Document issues:** Record problems and solutions for future reference
7. **Test thoroughly:** Verify fix before moving to next item

### 📋 Common Configuration Mistakes to Avoid

- ❌ Forgetting `no shutdown` on interfaces
- ❌ Mismatched VLAN IDs on different switches
- ❌ Incorrect subinterface VLAN tagging
- ❌ Missing OSPF network statements
- ❌ Wrong subnet mask or IP address
- ❌ Forgetting to enable OSPF on backbone links
- ❌ Incorrect trunk allowed VLAN list

---

✅ **Comprehensive networking exam lab completed successfully!** 🎓

**You have now demonstrated proficiency in VLAN design, inter-VLAN routing, and dynamic routing protocols! 🚀**
