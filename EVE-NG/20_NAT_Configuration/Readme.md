# 🔄 Network Address Translation (NAT) Lab

> Complete hands-on lab covering static NAT for servers, dynamic NAT for clients, Port Address Translation (PAT), NAT with dynamic routing protocols, and comprehensive troubleshooting.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [NAT Fundamentals](#nat-fundamentals)
6. [Static NAT Configuration](#static-nat-configuration)
7. [Dynamic NAT Configuration](#dynamic-nat-configuration)
8. [Port Address Translation (PAT)](#port-address-translation-pat)
9. [NAT with OSPF](#nat-with-ospf)
10. [Testing NAT](#testing-nat)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master network address translation for internal-to-external communication and security.

### By the end of this lab, you will:

- ✅ Understand NAT fundamentals and terminology
- ✅ Configure static NAT for server access
- ✅ Configure dynamic NAT for multiple clients
- ✅ Implement PAT (many-to-one) for bandwidth efficiency
- ✅ Configure NAT with dynamic routing (OSPF)
- ✅ Verify NAT translations in real-time
- ✅ Troubleshoot NAT issues systematically
- ✅ Design bidirectional NAT for enterprise networks
- ✅ Understand NAT implications for protocols

---

## ✅ Prerequisites

> **Purpose:** Ensure foundational knowledge for NAT configuration.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **IP Addressing** | Public vs private addresses | 04_Basic Switch Lab |
| **Routing Basics** | NAT works with routing | 08_Basic_Routing Lab |
| **OSPF Routing** | OSPF with NAT interaction | 10_OSPF_Lab Lab |
| **ACLs** | Define inside/outside networks | 13_ACL_Security Lab |
| **Network Design** | Inside vs outside interfaces | 06_Inter_VLAN Lab |

### Required Resources

- ✅ EVE-NG with IOSv routers
- ✅ VPCS for client testing
- ✅ Understanding of private IP ranges (10.x, 172.16-31.x, 192.168.x)
- ✅ Understanding of public IP addresses

---

## 📊 Lab Topology

> **Purpose:** Network design with NAT between private and public networks.

```
PRIVATE NETWORK (Inside)       NAT ROUTER          PUBLIC NETWORK (Outside)
                               
┌──────────────────┐           ┌──────────┐        ┌──────────────────┐
│ Internal Network │           │          │        │ ISP/External Net │
│ 10.0.0.0/24      │           │  R1 NAT  │        │ 203.0.113.0/24   │
│                  │───────────│          │───────│                  │
│ R2 (Router)      │   Inside  │ Gi0/0    │Outside│ External Server  │
│ 10.0.0.1         │           │ 203.0.113│.1     │ 203.0.113.20     │
│                  │           │          │        │                  │
│ PC1: 10.0.0.10   │           │ Gi0/1    │        │                  │
│ PC2: 10.0.0.11   │           │ (Public) │        │                  │
│ Server: 10.0.0.5 │           │          │        │                  │
│ (Web)            │           └──────────┘        │                  │
└──────────────────┘                               └──────────────────┘

NAT Types:
├─ Static NAT:  10.0.0.5 ↔ 203.0.113.20 (Server - 1:1)
├─ Dynamic NAT: 10.0.0.10-11 ↔ 203.0.113.50-60 (Clients - 1:many)
└─ PAT:         All ↔ 203.0.113.1:xxxx (All traffic - many:1 with ports)
```

### Device Details

| Device | Interface | IP Address | Role |
|--------|-----------|-----------|------|
| **R2** | Gi0/0 | 10.0.0.1 | Internal gateway |
| **R1** | Gi0/0 | 10.0.0.254 | Inside interface |
| **R1** | Gi0/1 | 203.0.113.1 | Outside interface |
| **PC1** | - | 10.0.0.10 | Client 1 |
| **PC2** | - | 10.0.0.11 | Client 2 |
| **Server** | - | 10.0.0.5 | Internal server |

---

## 🔧 Creating the Lab

> **Purpose:** Set up NAT topology with internal and external networks.

### Step 1: Create Lab

**What:** Create EVE-NG lab for NAT configuration.

**How to:**
1. Log into EVE-NG
2. Click **Add Lab**
3. Configure:
   - **Lab Name**: `NAT_Lab`
   - **Description**: `Network Address Translation Topology`
4. Click **Create**

---

### Step 2: Add Devices

**What:** Add routers, switches, and PCs.

**Devices:**
- **2 Routers** (R1 - NAT router, R2 - Internal gateway)
- **1 Switch** (SW1 - Internal network)
- **3 PCs** (PC1, PC2, Server)

---

### Step 3: Connect Devices

**Connections:**
- R1 Gi0/0 ↔ R2 Gi0/1 (Internal link)
- R2 Gi0/0 ↔ SW1 Gi0/0 (Internal switch)
- SW1 Gi1/1 ↔ PC1 (Client 1)
- SW1 Gi1/2 ↔ PC2 (Client 2)
- SW1 Gi1/3 ↔ Server (Web server)
- R1 Gi0/1 ↔ (Simulated ISP connection)

---

### Step 4: Start Lab

**What:** Boot all devices.

---

## 📚 NAT Fundamentals

> **Purpose:** Understand NAT concepts before configuration.

### NAT Terminology

| Term | Meaning | Example |
|------|---------|---------|
| **Inside Local** | IP client on internal network | 10.0.0.10 |
| **Inside Global** | Public IP of internal client | 203.0.113.50 |
| **Outside Local** | IP of external server (as seen internally) | 203.0.113.20 |
| **Outside Global** | Real IP of external server | 203.0.113.20 |

### Three NAT Types

```
1. STATIC NAT (One-to-one mapping)
   Inside Local    →    Inside Global
   10.0.0.5        →    203.0.113.20
   Purpose: Public access to specific servers
   Scale: Limited (one public IP per server)

2. DYNAMIC NAT (Pool of addresses)
   Inside Local    →    Inside Global (from pool)
   10.0.0.10       →    203.0.113.50
   10.0.0.11       →    203.0.113.51
   Purpose: Nat multiple clients to pool
   Scale: Better than static, still limited

3. PAT (Port Address Translation - many-to-one)
   Inside Local:Port      →    Inside Global:Port
   10.0.0.10:45000        →    203.0.113.1:45000
   10.0.0.11:45001        →    203.0.113.1:45001
   Purpose: All clients use one IP + different ports
   Scale: Unlimited (port multiplexing)
```

---

## ⚙️ Static NAT Configuration

> **Purpose:** Set up one-to-one mapping for server access.

### Step 5: Configure Inside Interface on R1

**What:** Set up R1's inside interface (connected to internal network).

**Commands:**
```bash
enable
configure terminal
hostname R1

interface gigabitEthernet 0/0
description Inside Interface
ip address 10.0.0.254 255.255.255.0
no shutdown
exit

end
```

---

### Step 6: Configure Outside Interface on R1

**What:** Set up R1's outside interface (connected to ISP).

**Commands:**
```bash
enable
configure terminal

interface gigabitEthernet 0/1
description Outside Interface
ip address 203.0.113.1 255.255.255.0
no shutdown
exit

end
```

---

### Step 7: Mark Interfaces for NAT

**What:** Designate inside and outside interfaces for NAT.

**Commands:**
```bash
enable
configure terminal

interface gigabitEthernet 0/0
ip nat inside
exit

interface gigabitEthernet 0/1
ip nat outside
exit

end
```

**What These Do:**

| Command | Effect |
|---------|--------|
| **ip nat inside** | Marks interface as internal network side |
| **ip nat outside** | Marks interface as external network side |

---

### Step 8: Configure Static NAT

**What:** Map internal server to public IP permanently.

**Commands:**
```bash
enable
configure terminal

! Static NAT: 10.0.0.5 (internal server) ↔ 203.0.113.20 (public)
ip nat inside source static 10.0.0.5 203.0.113.20

end
```

**Configuration Explanation:**

```
ip nat inside source static 10.0.0.5 203.0.113.20
                           │           │
                    Inside Local   Inside Global
                    (Private IP)   (Public IP)
```

> **ℹ️ Static NAT:** Permanent 1:1 mapping - traffic to 203.0.113.20 always goes to 10.0.0.5

---

### Step 9: Verify Static NAT

**What:** Check static NAT configuration.

**Commands:**
```bash
show ip nat statistics
show ip nat translations
show running-config | include nat
```

**Expected Output:**
```
Pro Inside global      Inside local       Outside local      Outside global
--- 203.0.113.20       10.0.0.5           ---                ---
```

> **✅ Checkpoint:** Static NAT configured for server!

---

## ⚙️ Dynamic NAT Configuration

> **Purpose:** Set up address pool for multiple clients.

### Step 10: Define ACL for Inside Addresses

**What:** Specify which inside addresses can use dynamic NAT.

**Commands:**
```bash
enable
configure terminal

! ACL 1 = addresses allowed to be natted
access-list 1 permit 10.0.0.10
access-list 1 permit 10.0.0.11

! (Exclude server since it uses static NAT)
access-list 1 deny 10.0.0.5

end
```

**ACL Logic:**
- ✅ PC1 (10.0.0.10) - permitted (will be nat'd)
- ✅ PC2 (10.0.0.11) - permitted (will be nat'd)
- ❌ Server (10.0.0.5) - denied (uses static nat)

---

### Step 11: Create Dynamic NAT Pool

**What:** Create pool of public IPs for dynamic NAT.

**Commands:**
```bash
enable
configure terminal

! Create pool named EXTERNAL_POOL
ip nat pool EXTERNAL_POOL 203.0.113.50 203.0.113.60 netmask 255.255.255.0

! Configure dynamic NAT
ip nat inside source list 1 pool EXTERNAL_POOL

end
```

**Configuration Breakdown:**

| Component | Meaning |
|-----------|---------|
| **ip nat pool** | Create address pool |
| **EXTERNAL_POOL** | Pool name |
| **203.0.113.50** | First public IP |
| **203.0.113.60** | Last public IP (11 addresses) |
| **list 1** | Use ACL 1 for matching |

---

### Step 12: Verify Dynamic NAT

**What:** Check dynamic NAT setup.

**Commands:**
```bash
show ip nat statistics
show ip nat pool
show ip nat translations
```

**Expected Pools:**
```
Pool        Start IP        End IP          Netmask         Type
EXTERNAL_  203.0.113.50    203.0.113.60    255.255.255.0   redundancy none
```

> **✅ Checkpoint:** Dynamic NAT pool created!

---

## 🔀 Port Address Translation (PAT)

> **Purpose:** Most common NAT - many clients sharing one public IP.

### Step 13: Configure PAT (Optional - Replaces Dynamic NAT)

**What:** Replace dynamic NAT with PAT for efficiency.

**Note:** Skip this if you want to keep both static + dynamic.

**Commands:**
```bash
enable
configure terminal

! Remove previous dynamic NAT
no ip nat inside source list 1 pool EXTERNAL_POOL

! Configure PAT - all clients use 203.0.113.1 with different ports
ip nat inside source list 1 interface gigabitEthernet 0/1 overload

end
```

**PAT Explanation:**

```
PAT = "Many to One" + Port multiplexing

Before PAT (Dynamic NAT):
PC1 (10.0.0.10:45000)  → 203.0.113.50:45000  (uses separate public IP)
PC2 (10.0.0.11:45001)  → 203.0.113.51:45001  (uses separate public IP)

After PAT:
PC1 (10.0.0.10:45000)  → 203.0.113.1:45000   (all use same IP, different ports!)
PC2 (10.0.0.11:45001)  → 203.0.113.1:45001
```

> **ℹ️ Key Difference:** `overload` keyword enables PAT (port multiplexing)

---

## 🌐 NAT with OSPF

> **Purpose:** Handle interaction between NAT and dynamic routing.

### Step 14: Configure OSPF with NAT

**What:** Enable OSPF on internal interfaces.

**Commands on R1:**
```bash
enable
configure terminal

! Create OSPF process
router ospf 1
network 10.0.0.0 0.0.0.255 area 0
exit

end
```

**Commands on R2:**
```bash
enable
configure terminal

router ospf 1
network 10.0.0.0 0.0.0.255 area 0
exit

end
```

---

### Step 15: Configure NAT with OSPF

**What:** Make OSPF work correctly with NAT.

**Commands on R1:**
```bash
enable
configure terminal

! Tell OSPF which routes to not NAT
route-map NO_NAT deny 10
match ip address 101
exit

route-map NO_NAT permit 20
exit

! Apply to outside routes
ip nat inside source route-map NO_NAT interface gigabitEthernet 0/1 overload

! ACL 101 = don't NAT these
access-list 101 deny ip 10.0.0.0 0.0.0.255 10.0.0.0 0.0.0.255

end
```

> **ℹ️ Advanced:** Route maps prevent NAT from breaking OSPF adjacencies

---

## 🔍 Testing NAT

> **Purpose:** Verify NAT functionality with traffic flows.

### Step 16: Configure PC Addresses

**What:** Set static IPs on PCs for testing.

**Commands on PC1:**
```bash
ip 10.0.0.10 10.0.0.1 24
show ip
```

**Commands on PC2:**
```bash
ip 10.0.0.11 10.0.0.1 24
show ip
```

**Commands on Server:**
```bash
ip 10.0.0.5 10.0.0.1 24
show ip
```

---

### Step 17: Test Static NAT (Internal to External)

**What:** Verify external host can reach internal server via static NAT.

**Simulation (on external device):**
```
Ping to 203.0.113.20 (static NAT for internal 10.0.0.5)
Expected: Reaches 10.0.0.5 on internal network
```

---

### Step 18: Test Dynamic NAT / PAT

**What:** Test multiple clients using NAT.

**Commands on PC1:**
```bash
ping 203.0.113.1
# Should work - outgoing traffic through NAT
```

**Commands on PC2:**
```bash
ping 203.0.113.1
# Should also work - different port for same destination
```

> **✅ Success:** Multiple clients using NAT simultaneously!

---

## ✔️ Verification

> **Purpose:** Verify and monitor NAT translations.

### Step 19: View Active NAT Translations

**What:** See real-time NAT translations in action.

**Commands on R1:**
```bash
show ip nat translations
```

**Expected Output:**
```
Pro Inside global      Inside local       Outside local      Outside global
--- 203.0.113.20       10.0.0.5           ---                ---
tcp 203.0.113.1:45000  10.0.0.10:45000    203.0.113.1:45000  203.0.113.1:45000
tcp 203.0.113.1:45001  10.0.0.11:45001    203.0.113.1:45001  203.0.113.1:45001
```

| Column | Meaning |
|--------|---------|
| **Inside global** | Public IP seen outside |
| **Inside local** | Private IP on internal |
| **Outside local** | Remote IP as seen internally |
| **Outside global** | Remote IP's real address |

---

### Step 20: Check NAT Statistics

**What:** View NAT resource utilization.

**Commands:**
```bash
show ip nat statistics
```

**Sample Output:**
```
Total active translations: 3 (1 static, 2 dynamic; 2 extended)
Outside interfaces:
  GigabitEthernet0/1
Inside interfaces:
  GigabitEthernet0/0
Hits: 48    Misses: 2
Expired translations: 0
Dynamic mappings:
 -- EXTERNAL_POOL pool (50-60): 2 translations active
```

---

### Step 21: Monitor NAT Entries

**What:** Track specific connections.

**Commands:**
```bash
debug ip nat
# Shows real-time NAT decisions

show ip nat translations verbose
# Shows detailed translation information
```

> **✅ Checkpoint:** NAT fully operational and verified!

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **No NAT translation** | Inside/outside not marked | Check `show ip nat statistics` |
| **Clients can't reach outside** | ACL too restrictive | Verify ACL permits source IPs |
| **PAT port conflicts** | Too many connections | Increase port range or add IPs |
| **Static NAT not working** | Reverse traffic not translated | Verify firewall allows return traffic |
| **OSPF neighbors down** | NAT breaking hello packets | Use route-map to exclude OSPF traffic |
| **Returning traffic dropped** | Translation not matched | Enable connection tracking |

### Debugging Commands

```bash
# Enable NAT debugging
debug ip nat detailed

# Show all translations with details
show ip nat translations verbose

# Check which ACL matches
show access-list

# Verify interface markings
show ip nat statistics

# Turn off debugging
undebug all
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured static NAT for server access
- ✅ Implemented dynamic NAT with address pool
- ✅ Set up PAT for bandwidth efficiency
- ✅ Integrated NAT with dynamic routing
- ✅ Verified all translation types
- ✅ Monitored active translations
- ✅ Troubleshot NAT issues

### Key Concepts

| Concept | Purpose | When to Use |
|---------|---------|-----------|
| **Static NAT** | Permanent public IP for servers | Servers needing fixed public IPs |
| **Dynamic NAT** | Pool of IPs for clients | Multiple servers/clients |
| **PAT** | One IP multiple ports | Small offices, branch sites |
| **Route Maps** | Control NAT behavior | Complex networks with routing |
| **ACLs** | Define what to NAT | Exclude certain traffic from NAT |

### NAT Quick Reference

```bash
# Mark interfaces
ip nat inside
ip nat outside

# Static NAT
ip nat inside source static 10.0.0.5 203.0.113.20

# Dynamic NAT
ip nat pool POOL 203.0.113.50 203.0.113.60 netmask 255.255.255.0
ip nat inside source list 1 pool POOL

# PAT
ip nat inside source list 1 interface Gi0/1 overload

# Verify
show ip nat translations
show ip nat statistics
```

### What's Next?

**Continue learning:**
1. 🌐 **DNS Configuration** - Add domain name service
2. 💾 **DHCP Integration** - Combine DHCP + NAT
3. 🔐 **Security with NAT** - NAT as security layer
4. ☁️ **NAT with Cloud** - Hybrid network NAT design

---

✅ **NAT lab complete! You now understand all types of address translation!** 🎓

**Ready for DNS next?** 🚀
