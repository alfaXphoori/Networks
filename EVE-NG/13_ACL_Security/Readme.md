# 🔒 Access Control Lists (ACLs) Security Lab

> Complete hands-on lab covering standard ACLs, extended ACLs, named ACLs, and advanced filtering techniques for network security and traffic control.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [ACL Fundamentals](#acl-fundamentals)
6. [Basic Router & PC Configuration](#basic-router--pc-configuration)
7. [Standard ACL Configuration](#standard-acl-configuration)
8. [Extended ACL Configuration](#extended-acl-configuration)
9. [Named ACL Configuration](#named-acl-configuration)
10. [ACL Placement & Best Practices](#acl-placement--best-practices)
11. [Testing & Verification](#testing--verification)
12. [Troubleshooting](#troubleshooting)
13. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master Access Control Lists for network security, traffic filtering, and policy enforcement.

### By the end of this lab, you will:

- ✅ Understand ACL types and use cases
- ✅ Configure standard ACLs (filter by source IP)
- ✅ Configure extended ACLs (filter by protocol, port, source, destination)
- ✅ Create named ACLs for better management
- ✅ Apply ACLs to router interfaces (inbound/outbound)
- ✅ Implement permit and deny rules
- ✅ Understand implicit deny all rule
- ✅ Use wildcard masks correctly
- ✅ Verify ACL functionality with ping and telnet
- ✅ Troubleshoot ACL misconfigurations

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **IP Addressing** | ACLs filter based on IP | 04_Basic Switch Lab |
| **Router Configuration** | ACLs applied on routers | 08_Basic_Routing Lab |
| **TCP/UDP Ports** | Extended ACLs filter ports | Networking fundamentals |
| **Network Traffic Flow** | Understand inbound/outbound | Previous labs |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco router images available (IOSv)
- ✅ VPCS virtual PCs available
- ✅ Access to EVE-NG web interface
- ✅ Completed basic routing labs (recommended)

---

## 📊 Lab Topology

> **Purpose:** Three-router network with multiple security zones requiring traffic filtering.

![ACL Lab Topology](imgs/diagram.png)

```
     BRANCH OFFICE              HEADQUARTERS              DMZ/SERVERS
     (Untrusted)                (Trusted Core)            (Public Services)
  
┌─────────────────┐         ┌─────────────────┐       ┌─────────────────┐
│   Network A     │         │   Network B     │       │   Network C     │
│  10.1.1.0/24    │         │  10.2.2.0/24    │       │  10.3.3.0/24    │
│                 │         │                 │       │                 │
│  PC1: .10       │◄───────►│     (HQ)        │◄─────►│  Web: .10       │
│  PC2: .20       │   ACL1  │                 │  ACL2 │  FTP: .20       │
└────────┬────────┘         └────────┬────────┘       └────────┬────────┘
         │                           │                         │
      Gi0/0                       Gi0/1                     Gi0/0
         │                           │                         │
    ┌────┴─────┐               ┌────┴─────┐             ┌────┴─────┐
    │    R1    │───────────────│    R2    │─────────────│    R3    │
    │ (Branch) │   Gi0/1       │   (HQ)   │   Gi0/2     │  (DMZ)   │
    └──────────┘  .1      .2   └──────────┘  .2    .1   └──────────┘
                10.10.1.0/30              10.20.2.0/30

Security Policies:
├─ Branch can access HQ HTTP/HTTPS only
├─ HQ can access all networks
├─ DMZ servers accessible from outside on specific ports only
└─ Telnet/SSH restricted to admin network only
```

### Topology Details

#### Network A - Branch Office (Untrusted)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | 10.1.1.1 | 255.255.255.0 | Branch gateway |
| **R1** | Gi0/1 | 10.10.1.1 | 255.255.255.252 | Link to R2 (HQ) |
| **PC1** | eth0 | 10.1.1.10 | 255.255.255.0 | Branch client |
| **PC2** | eth0 | 10.1.1.20 | 255.255.255.0 | Branch client |

#### Network B - Headquarters (Trusted)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R2** | Gi0/0 | 10.2.2.1 | 255.255.255.0 | HQ gateway |
| **R2** | Gi0/1 | 10.10.1.2 | 255.255.255.252 | Link to R1 (Branch) |
| **R2** | Gi0/2 | 10.20.2.2 | 255.255.255.252 | Link to R3 (DMZ) |
| **PC3** | eth0 | 10.2.2.10 | 255.255.255.0 | HQ admin client |

#### Network C - DMZ (Public Servers)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R3** | Gi0/0 | 10.3.3.1 | 255.255.255.0 | DMZ gateway |
| **R3** | Gi0/1 | 10.20.2.1 | 255.255.255.252 | Link to R2 (HQ) |
| **PC4** | eth0 | 10.3.3.10 | 255.255.255.0 | Web Server |
| **PC5** | eth0 | 10.3.3.20 | 255.255.255.0 | FTP Server |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the three-router ACL security topology in EVE-NG.

### Step 1: Create a New Lab

**What:** Create the lab project for ACL configuration.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Enter lab details:
   - **Lab Name**: `ACL_Security_Lab`
   - **Lab Description**: `Access Control Lists for Network Security`
   - **Lab Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Router Nodes

**What:** Add three Cisco routers for multi-zone security.

**How to:**
1. Click **Add Node**
2. Select **Cisco** → **IOSv** (router)
3. Add three routers:
   - **R1** (Branch router)
   - **R2** (HQ Core router)
   - **R3** (DMZ router)
4. Click **Save**

---

### Step 3: Add PC Nodes

**What:** Add five virtual PCs for testing.

**How to:**
1. Click **Add Node**
2. Select **Linux** → **VPCS**
3. Add five PCs:
   - **PC1, PC2** (Branch clients)
   - **PC3** (HQ admin)
   - **PC4, PC5** (DMZ servers)
4. Click **Save**

---

### Step 4: Connect All Devices

**What:** Create network connections between routers and PCs.

**Connection List:**

| From Device | Interface | To Device | Interface | Purpose |
|-------------|-----------|-----------|-----------|---------|
| **R1** | Gi0/0 | **PC1** | eth0 | Branch Network A |
| **R1** | Gi0/0 | **PC2** | eth0 | Branch Network A |
| **R1** | Gi0/1 | **R2** | Gi0/1 | Branch-to-HQ link |
| **R2** | Gi0/0 | **PC3** | eth0 | HQ Network B |
| **R2** | Gi0/2 | **R3** | Gi0/1 | HQ-to-DMZ link |
| **R3** | Gi0/0 | **PC4** | eth0 | DMZ Network C (Web) |
| **R3** | Gi0/0 | **PC5** | eth0 | DMZ Network C (FTP) |

> **💡 Tip:** Connect multiple PCs to the same router interface to simulate a switch network.

---

### Step 5: Start All Devices

**What:** Power on all routers and PCs.

**How to:**
1. Click **Start All** or start each device individually
2. Wait for devices to boot (routers take 1-2 minutes)
3. Verify all devices show "Running" status

---

## 📚 ACL Fundamentals

> **Purpose:** Understand ACL concepts before configuration.

### What are Access Control Lists?

**ACLs** are ordered lists of rules that control network traffic by:
- ✅ **Permitting** or **denying** packets based on criteria
- ✅ Filtering traffic at router interfaces
- ✅ Implementing security policies
- ✅ Controlling routing updates
- ✅ Defining interesting traffic (for VPNs, NAT, etc.)

---

### ACL Types Comparison

| Type | Number Range | Filters By | Use Case |
|------|--------------|-----------|----------|
| **Standard ACL** | 1-99, 1300-1999 | Source IP only | Simple filtering, close to destination |
| **Extended ACL** | 100-199, 2000-2699 | Source, Dest, Protocol, Port | Complex filtering, close to source |
| **Named ACL** | N/A (uses names) | Same as standard/extended | Easier to manage and modify |

---

### Wildcard Masks Explained

**Wildcard masks** are inverse subnet masks used in ACLs:

| Subnet Mask | Wildcard Mask | Meaning |
|-------------|---------------|---------|
| 255.255.255.255 | 0.0.0.0 | Match exact host |
| 255.255.255.0 | 0.0.0.255 | Match entire /24 network |
| 255.255.0.0 | 0.0.255.255 | Match entire /16 network |
| 255.0.0.0 | 0.255.255.255 | Match entire /8 network |

**Rules:**
- `0` = must match exactly
- `255` = don't care (any value)

**Examples:**
```
10.1.1.10 0.0.0.0       → Match only 10.1.1.10
10.1.1.0 0.0.0.255      → Match 10.1.1.0 - 10.1.1.255
0.0.0.0 255.255.255.255 → Match ANY IP (same as "any")
```

---

### ACL Processing Rules

1. **Top-down processing** - Rules evaluated in order
2. **First match wins** - Processing stops at first match
3. **Implicit deny all** - Traffic not matched is denied
4. **One ACL per interface per direction** - Can't apply multiple
5. **Inbound vs Outbound** - Choose direction carefully

---

### ACL Placement Best Practices

```
┌──────────────────────────────────────────────────────┐
│  Standard ACLs: Place CLOSE TO DESTINATION           │
│  (filters by source only, avoid blocking too early)  │
├──────────────────────────────────────────────────────┤
│  Extended ACLs: Place CLOSE TO SOURCE                │
│  (filters specifically, block early to save bandwidth)│
└──────────────────────────────────────────────────────┘
```

---

## ⚙️ Basic Router & PC Configuration

> **Purpose:** Configure IP addresses and routing before applying ACLs.

### Step 1: Router IP Configuration

**What:** Configure IP addresses on all router interfaces and enable routing.

**R1 Configuration:**

```bash
enable
configure terminal

hostname R1

# Configure Branch network interface
interface GigabitEthernet0/0
 ip address 10.1.1.1 255.255.255.0
 no shutdown
 exit

# Configure WAN link to R2
interface GigabitEthernet0/1
 ip address 10.10.1.1 255.255.255.252
 no shutdown
 exit

# Configure routing (OSPF)
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 10.10.1.0 0.0.0.3 area 0
 exit

write memory
```

**R2 Configuration:**

```bash
enable
configure terminal

hostname R2

# Configure HQ network interface
interface GigabitEthernet0/0
 ip address 10.2.2.1 255.255.255.0
 no shutdown
 exit

# Configure WAN link to R1
interface GigabitEthernet0/1
 ip address 10.10.1.2 255.255.255.252
 no shutdown
 exit

# Configure WAN link to R3
interface GigabitEthernet0/2
 ip address 10.20.2.2 255.255.255.252
 no shutdown
 exit

# Configure routing (OSPF)
router ospf 1
 network 10.2.2.0 0.0.0.255 area 0
 network 10.10.1.0 0.0.0.3 area 0
 network 10.20.2.0 0.0.0.3 area 0
 exit

write memory
```

**R3 Configuration:**

```bash
enable
configure terminal

hostname R3

# Configure DMZ network interface
interface GigabitEthernet0/0
 ip address 10.3.3.1 255.255.255.0
 no shutdown
 exit

# Configure WAN link to R2
interface GigabitEthernet0/1
 ip address 10.20.2.1 255.255.255.252
 no shutdown
 exit

# Configure routing (OSPF)
router ospf 1
 network 10.3.3.0 0.0.0.255 area 0
 network 10.20.2.0 0.0.0.3 area 0
 exit

write memory
```

---

### Step 2: PC IP Configuration

**What:** Configure IP addresses and default gateways on all PCs.

**PC1 Configuration (Branch Client):**

```bash
ip 10.1.1.10/24 10.1.1.1
save
```

**PC2 Configuration (Branch Client):**

```bash
ip 10.1.1.20/24 10.1.1.1
save
```

**PC3 Configuration (HQ Admin):**

```bash
ip 10.2.2.10/24 10.2.2.1
save
```

**PC4 Configuration (Web Server):**

```bash
ip 10.3.3.10/24 10.3.3.1
save
```

**PC5 Configuration (FTP Server):**

```bash
ip 10.3.3.20/24 10.3.3.1
save
```

---

### Step 3: Verify Basic Connectivity

**What:** Test network connectivity before applying ACLs.

**Test from PC1:**

```bash
# Ping local gateway
ping 10.1.1.1

# Ping HQ network
ping 10.2.2.10

# Ping DMZ network
ping 10.3.3.10

# All should succeed
```

**Test from R1:**

```bash
# Verify OSPF neighbors
show ip ospf neighbor

# Verify routing table
show ip route

# Should see routes to all networks
```

**Expected Results:**
- ✅ All routers can ping each other
- ✅ All PCs can ping their gateways
- ✅ All PCs can ping across networks
- ✅ OSPF adjacencies established

> **⚠️ Important:** Document this baseline connectivity. After applying ACLs, some pings will fail by design.

---

## 🔒 Standard ACL Configuration

> **Purpose:** Configure source-based filtering using standard ACLs.

### Scenario 1: Block Specific Host from Branch

**Requirement:** Block PC1 (10.1.1.10) from accessing HQ network, but allow PC2.

**R2 Configuration:**

```bash
# Access R2 console
enable
configure terminal

# Create standard ACL 10
access-list 10 deny 10.1.1.10 0.0.0.0
access-list 10 permit any

# Apply ACL to interface (inbound from R1)
interface GigabitEthernet0/1
 ip access-group 10 in
 exit

# Save configuration
write memory
```

**Verification:**

```bash
# Show ACL configuration
show access-lists

# Show ACL on interface
show ip interface GigabitEthernet0/1

# Expected output:
# Access List 10 is applied (10 deny 10.1.1.10, permit any)
```

**Test from PC1:**
```bash
# From PC1 console
ping 10.2.2.10
# Should fail (blocked by ACL)
```

**Test from PC2:**
```bash
# From PC2 console
ping 10.2.2.10
# Should succeed (permitted by ACL)
```

---

### Scenario 2: Allow Only Specific Network

**Requirement:** Allow only HQ network (10.2.2.0/24) to access DMZ servers.

**R3 Configuration:**

```bash
enable
configure terminal

# Create standard ACL 20
access-list 20 permit 10.2.2.0 0.0.0.255
# Implicit deny all (no need to write it)

# Apply ACL to DMZ network interface (outbound)
interface GigabitEthernet0/0
 ip access-group 20 out
 exit

write memory
```

---

## 🛡️ Extended ACL Configuration

> **Purpose:** Configure granular filtering with protocol, port, source, and destination.

### Scenario 3: Allow HTTP/HTTPS Only from Branch

**Requirement:** Branch office can access HQ web services (HTTP/HTTPS) only, block all other traffic.

**R1 Configuration (close to source):**

```bash
enable
configure terminal

# Create extended ACL 100
# Permit HTTP (port 80)
access-list 100 permit tcp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255 eq 80

# Permit HTTPS (port 443)
access-list 100 permit tcp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255 eq 443

# Permit ICMP for troubleshooting (optional)
access-list 100 permit icmp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255

# Deny everything else (implicit, but can be explicit)
access-list 100 deny ip any any

# Apply ACL outbound on interface to HQ
interface GigabitEthernet0/1
 ip access-group 100 out
 exit

write memory
```

---

### Scenario 4: Restrict Telnet Access

**Requirement:** Only HQ admin PC (10.2.2.10) can telnet to any router.

**R1, R2, R3 Configuration (apply on all routers):**

```bash
enable
configure terminal

# Create extended ACL 101
# Permit telnet from admin PC only
access-list 101 permit tcp host 10.2.2.10 any eq 23

# Deny telnet from all other sources
access-list 101 deny tcp any any eq 23

# Permit all other traffic
access-list 101 permit ip any any

# Apply to VTY lines (not interface - special case)
line vty 0 4
 access-class 101 in
 exit

write memory
```

---

### Scenario 5: DMZ Server Access Control

**Requirement:** 
- Web server (10.3.3.10) accessible on HTTP (80) and HTTPS (443) from anywhere
- FTP server (10.3.3.20) accessible on FTP (21) from HQ only

**R3 Configuration:**

```bash
enable
configure terminal

# Extended ACL 110 for inbound DMZ traffic
# Allow HTTP to web server
access-list 110 permit tcp any host 10.3.3.10 eq 80
access-list 110 permit tcp any host 10.3.3.10 eq 443

# Allow FTP to FTP server from HQ only
access-list 110 permit tcp 10.2.2.0 0.0.0.255 host 10.3.3.20 eq 21

# Allow ICMP for troubleshooting
access-list 110 permit icmp any any

# Deny all other traffic (implicit)

# Apply ACL to DMZ interface (inbound to DMZ)
interface GigabitEthernet0/0
 ip access-group 110 in
 exit

write memory
```

---

## 📝 Named ACL Configuration

> **Purpose:** Use descriptive names for easier ACL management and modification.

### Scenario 6: Named ACL for Branch Security

**Requirement:** Replace numbered ACL with named ACL for better readability.

**R1 Configuration:**

```bash
enable
configure terminal

# Remove old numbered ACL if exists
no access-list 100

# Create named extended ACL
ip access-list extended BRANCH_TO_HQ_FILTER
 remark *** Allow web traffic only ***
 permit tcp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255 eq 80
 permit tcp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255 eq 443
 remark *** Allow ICMP for diagnostics ***
 permit icmp any any
 remark *** Deny all other traffic ***
 deny ip any any
 exit

# Apply named ACL to interface
interface GigabitEthernet0/1
 ip access-group BRANCH_TO_HQ_FILTER out
 exit

write memory
```

---

### Named ACL Advantages

✅ **Descriptive names** - `BRANCH_TO_HQ_FILTER` vs `100`
✅ **Add/remove specific lines** - Can edit without recreating entire ACL
✅ **Documentation** - Use `remark` statements
✅ **Sequence numbers** - Reorder rules easily

---

### Modifying Named ACLs

**Add a new rule:**

```bash
ip access-list extended BRANCH_TO_HQ_FILTER
 15 permit tcp 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255 eq 22
 exit
```

**Remove a specific rule:**

```bash
ip access-list extended BRANCH_TO_HQ_FILTER
 no 15
 exit
```

**View with sequence numbers:**

```bash
show ip access-lists BRANCH_TO_HQ_FILTER
```

---

## 🎓 ACL Placement & Best Practices

> **Purpose:** Learn optimal ACL placement strategies.

### Rule Summary

| ACL Type | Place Close To | Reason |
|----------|---------------|--------|
| **Standard** | Destination | Filters only source, avoid blocking too early |
| **Extended** | Source | Specific filtering, save bandwidth |

### Example Placement

```
     PC1 ──► [R1] ──────► [R2] ──────► [R3] ──► Server
    Source          Transit         Transit      Dest

Standard ACL:  Place on R3 (near server)
Extended ACL:  Place on R1 (near PC1)
```

---

### Best Practices Checklist

- ✅ **Most specific rules first** - Order from specific to general
- ✅ **Use named ACLs** - Easier to manage
- ✅ **Document with remarks** - Explain purpose of rules
- ✅ **Test before deployment** - Verify with ping/telnet
- ✅ **Monitor traffic** - Use `show access-lists` to see hit counts
- ✅ **Avoid implicit deny** - Add explicit permits first
- ✅ **One ACL per direction** - Inbound OR outbound per interface
- ✅ **Backup configs** - Save before and after changes

---

## ✅ Testing & Verification

> **Purpose:** Verify ACL functionality after applying security policies.

### Step 1: Apply ACLs and Test

**Apply ACLs from scenarios above, then test:**

#### Test Standard ACL (Scenario 1):

```bash
# From PC1 (blocked)
ping 10.2.2.10
# Expected: Failed (timed out)

# From PC2 (allowed)
ping 10.2.2.10
# Expected: Success
```

#### Test Extended ACL (Scenario 3):

```bash
# From PC1 - test HTTP/HTTPS (would need web server)
# Use telnet to test ports

# From R1 or PC1 (if telnet available):
telnet 10.2.2.10 80    # Should succeed (HTTP allowed)
telnet 10.2.2.10 23    # Should fail (Telnet blocked)
```

#### Test Named ACL:

```bash
# From any router
show ip access-lists BRANCH_TO_HQ_FILTER

# Check hit counts
# (matches) column shows how many packets matched each rule
```

---

### Step 2: Verification Commands

**Show all ACLs:**

```bash
show access-lists
```

**Show specific ACL:**

```bash
show access-lists 10
show ip access-lists BRANCH_TO_HQ_FILTER
```

**Show ACLs on interface:**

```bash
show ip interface GigabitEthernet0/1
```

**Show ACL statistics:**

```bash
show access-lists
# Look for "(X matches)" after each rule
```

**Clear ACL counters:**

```bash
clear access-list counters
# Or specific:
clear access-list counters 10
```

---

## 🔧 Troubleshooting

> **Purpose:** Diagnose and fix common ACL issues.

### Common Problems & Solutions

#### Problem 1: Traffic Still Blocked After Permitting

**Symptoms:**
- Added permit rule but traffic still fails
- Ping/connection times out

**Possible Causes:**

1. **Rule order wrong** - Deny rule before permit
   ```bash
   # WRONG ORDER:
   access-list 100 deny ip any any
   access-list 100 permit tcp any any eq 80
   # Deny matches first, permit never evaluated
   
   # CORRECT ORDER:
   access-list 100 permit tcp any any eq 80
   access-list 100 deny ip any any
   ```

2. **Wildcard mask incorrect**
   ```bash
   # WRONG (subnet mask instead of wildcard):
   access-list 10 permit 10.1.1.0 255.255.255.0
   
   # CORRECT:
   access-list 10 permit 10.1.1.0 0.0.0.255
   ```

3. **Applied to wrong interface or direction**
   ```bash
   # Check interface:
   show ip interface Gi0/1
   
   # Should show:
   # Inbound access list is 10
   # OR
   # Outbound access list is 10
   ```

**Solution:**
```bash
# Remove ACL from interface
interface GigabitEthernet0/1
 no ip access-group 100 in
 exit

# Delete and recreate ACL in correct order
no access-list 100
access-list 100 permit tcp any any eq 80
access-list 100 deny ip any any

# Reapply correctly
interface GigabitEthernet0/1
 ip access-group 100 in
 exit
```

---

#### Problem 2: Entire Network Blocked

**Symptoms:**
- Applied ACL, now nothing works
- All pings fail

**Cause:** Forgot to add permit rule, hit implicit deny all

**Solution:**
```bash
# Check ACL:
show access-lists 10

# If only deny rules or empty:
# Add permit statement
access-list 10 permit any

# Or remove ACL from interface temporarily:
interface Gi0/1
 no ip access-group 10 in
 exit
```

---

#### Problem 3: Can't Modify Standard/Extended Numbered ACL

**Symptoms:**
- Need to insert rule in middle
- Can't reorder without deleting entire ACL

**Solution:** Convert to named ACL:

```bash
# Old numbered ACL:
access-list 100 permit tcp any any eq 80
access-list 100 deny ip any any

# Convert to named:
no access-list 100

ip access-list extended WEB_FILTER
 10 permit tcp any any eq 80
 20 permit tcp any any eq 443
 30 deny ip any any
 exit

# Now can insert at sequence 15:
ip access-list extended WEB_FILTER
 15 permit tcp any any eq 22
 exit
```

---

#### Problem 4: Return Traffic Blocked

**Symptoms:**
- Outbound works, inbound fails
- TCP connections don't complete

**Cause:** ACL blocks return packets

**Solution:** Use `established` keyword for TCP:

```bash
# Allow outbound connections and return traffic:
access-list 110 permit tcp 10.1.1.0 0.0.0.255 any
access-list 110 permit tcp any 10.1.1.0 0.0.0.255 established
# "established" matches TCP packets with ACK or RST flags
```

---

### Debugging Commands

**Enable ACL logging:**

```bash
access-list 100 deny ip any any log
# Or for named ACL:
ip access-list extended TEST
 deny ip any any log
 exit
```

**View logged ACL violations:**

```bash
show logging
# Look for ACL deny messages
```

**Test ACL matching without applying:**

```bash
# Use packet tracer (on some IOS versions):
debug ip packet detail
```

**Monitor ACL hits in real-time:**

```bash
# Before test:
clear access-list counters

# Run test traffic

# Check which rules matched:
show access-lists
# Look for (X matches)
```

---

## 📈 Summary & Next Steps

> **Purpose:** Consolidate learning and plan advancement.

### What You've Learned

✅ **ACL Types**
- Standard ACLs (source IP only)
- Extended ACLs (protocol, port, source, destination)
- Named ACLs (better management)

✅ **Configuration Skills**
- Create and apply ACLs
- Use wildcard masks correctly
- Place ACLs optimally
- Modify and troubleshoot ACLs

✅ **Security Concepts**
- Permit and deny rules
- Implicit deny all
- Inbound vs outbound filtering
- Traffic flow control

---

### Real-World Applications

| Industry | ACL Use Case |
|----------|--------------|
| **Enterprise** | Segment departments, restrict server access |
| **ISP** | Filter customer traffic, prevent attacks |
| **Government** | Enforce security policies, protect classified networks |
| **Finance** | PCI compliance, protect payment systems |
| **Healthcare** | HIPAA compliance, protect patient data |

---

### Security Best Practices Learned

1. ✅ **Deny by default** - Use implicit deny, permit what's needed
2. ✅ **Least privilege** - Grant minimum access required
3. ✅ **Defense in depth** - Multiple security layers
4. ✅ **Document everything** - Use remarks in named ACLs
5. ✅ **Regular audits** - Review ACLs periodically
6. ✅ **Test thoroughly** - Verify before production
7. ✅ **Monitor logs** - Track denied traffic

---

### Common Interview Questions

**Q1: What's the difference between standard and extended ACLs?**
> Standard ACLs filter by source IP only (1-99). Extended ACLs filter by source, destination, protocol, and port (100-199).

**Q2: Where should you place standard vs extended ACLs?**
> Standard ACLs close to destination (filter broadly). Extended ACLs close to source (filter specifically).

**Q3: What is the implicit deny all?**
> At the end of every ACL is an invisible "deny any any" rule. Traffic not explicitly permitted is blocked.

**Q4: How do you allow return traffic for TCP?**
> Use the `established` keyword: `permit tcp any any established` matches packets with ACK/RST flags.

**Q5: Can you reorder rules in a numbered ACL?**
> No, must delete and recreate. Use named ACLs for better management (sequence numbers).

---

### Next Steps

Now that you've mastered ACLs, continue your security journey:

#### Immediate Next Lab: **14_HSRP_Redundancy**
- Configure gateway redundancy
- Test failover scenarios
- Ensure high availability

#### Related Advanced Topics:

1. **Firewall Concepts** (Lab 15)
   - Stateful inspection
   - Zone-based firewalls
   - Application layer filtering

2. **NAT with ACLs** (Lab 20)
   - Define NAT pools
   - Policy-based NAT
   - ACLs for interesting traffic

3. **VPN Access Control** (Future)
   - Crypto ACLs
   - VPN traffic filtering
   - Remote access security

4. **QoS with ACLs** (Lab 19)
   - Class maps
   - Traffic classification
   - Policy enforcement

---

### Practice Scenarios

Try these on your own:

1. **DMZ Hardening**
   - Create DMZ with web/DB servers
   - Allow HTTP/HTTPS from outside
   - Allow DB access from web tier only
   - Block all other traffic

2. **Guest Network Isolation**
   - Create guest VLAN
   - Allow internet access only
   - Block access to internal networks
   - Rate limit bandwidth

3. **Management Plane Security**
   - Restrict SSH/Telnet to admin subnet
   - Block SNMP from untrusted networks
   - Allow NTP to specific servers only

---

### Certification Alignment

This lab covers topics from:

- ✅ **CompTIA Network+** - ACL fundamentals, security filtering
- ✅ **Cisco CCNA** - Standard/extended ACLs, wildcard masks, placement
- ✅ **CCNP Security** - Advanced ACL techniques, optimization

---

### Additional Resources

**Cisco Documentation:**
- [ACL Configuration Guide](https://www.cisco.com/c/en/us/support/docs/security/ios-firewall/23602-confaccesslists.html)
- [Wildcard Mask Calculator](https://www.calculator.net/ip-subnet-calculator.html)

**Practice:**
- Packet Tracer ACL labs
- GNS3 advanced scenarios
- Real equipment configuration

---

## 🏆 Congratulations!

You've completed the **Access Control Lists (ACLs) Security Lab**!

You can now:
- ✅ Secure networks with ACLs
- ✅ Filter traffic based on multiple criteria
- ✅ Implement security policies
- ✅ Troubleshoot ACL issues
- ✅ Design multi-zone secure networks

**You're now 13/28 labs complete!** 🎉

Continue to **[Lab 14: HSRP Redundancy](../14_HSRP_Redundancy/Readme.md)** to ensure high availability.

---

## 📞 Support

Questions or issues? 
- Open an issue on GitHub
- Review the troubleshooting section
- Check Cisco documentation

---

**Remember:** Security is not a product, it's a process. Keep practicing! 🔒
