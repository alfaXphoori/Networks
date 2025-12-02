# 📡 DHCP Services Configuration Lab

> Complete hands-on lab covering DHCP relay agents, multiple pools per VLAN, DHCP snooping for security, conflict detection, and troubleshooting techniques.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [DHCP Relay Configuration](#dhcp-relay-configuration)
6. [Multiple DHCP Pools](#multiple-dhcp-pools)
7. [DHCP Snooping Security](#dhcp-snooping-security)
8. [PC Configuration](#pc-configuration)
9. [Testing DHCP](#testing-dhcp)
10. [Verification](#verification)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master DHCP configuration for automatic IP address assignment across multiple VLANs and domains.

### By the end of this lab, you will:

- ✅ Configure DHCP relay agents on routers
- ✅ Create multiple DHCP pools per VLAN
- ✅ Understand DHCP server vs relay agent roles
- ✅ Configure DHCP snooping for security
- ✅ Detect and prevent DHCP conflicts
- ✅ Verify DHCP bindings and leases
- ✅ Troubleshoot DHCP issues systematically
- ✅ Configure DHCP options and parameters
- ✅ Test dynamic IP address assignment

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **VLAN Fundamentals** | DHCP works with VLANs | 05_VLAN Lab |
| **Inter-VLAN Routing** | DHCP relay crosses VLANs | 06_Inter_VLAN Lab |
| **IP Addressing** | DHCP assigns IP addresses | 04_Basic Switch Lab |
| **Router Interfaces** | DHCP relay on router links | 03_Switch Config Lab |

### Required Resources

- ✅ EVE-NG with IOSv images
- ✅ VPCS virtual PCs
- ✅ Understanding of VLAN configuration
- ✅ Understanding of router subinterfaces

---

## 📊 Lab Topology

> **Purpose:** Three-domain network with DHCP server and relay agents.

```
┌─────────────────────────────────────────┐
│  DHCP Server Domain                     │
│  ┌──────────────────────────┐           │
│  │ DHCP Server (Router R0)  │           │
│  │ IP: 192.168.100.1/24     │           │
│  │ Pools: All VLANs         │           │
│  └───────────┬──────────────┘           │
│              │                          │
│          Backbone Link                  │
│              │                          │
└──────────────┼──────────────────────────┘
               │
    ┌──────────┴──────────┐
    │                     │
    ↓                     ↓
┌─────────────┐      ┌──────────────┐
│ Domain 1    │      │ Domain 2     │
│ (Router R1) │      │ (Router R2)  │
│ Relay Agent │      │ Relay Agent  │
└──────┬──────┘      └──────┬───────┘
       │                    │
    ┌──┴──┐              ┌──┴──┐
    ↓     ↓              ↓     ↓
  VLAN10 VLAN20        VLAN10 VLAN20
  PC1    PC2           PC3    PC4
```

### Network Details

| Device | Interface | IP Address | Purpose |
|--------|-----------|-----------|---------|
| **R0** | Gi0/0 | 192.168.100.1 | DHCP Server |
| **R0** | Gi0/1 | 1.1.1.1/30 | Link to R1 |
| **R0** | Gi0/2 | 2.1.1.1/30 | Link to R2 |
| **R1** | Gi0/1 | 1.1.1.2/30 | Link to R0 |
| **R1** | Gi0/0 | (VLAN SVI) | DHCP Relay |
| **R2** | Gi0/2 | 2.1.1.2/30 | Link to R0 |
| **R2** | Gi0/0 | (VLAN SVI) | DHCP Relay |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the DHCP topology with routers, switches, and PCs.

### Step 1: Create Lab Project

**What:** Create EVE-NG lab for DHCP services.

**How to:**
1. Log into EVE-NG
2. Click **Add Lab**
3. Configure:
   - **Lab Name**: `DHCP_Lab`
   - **Description**: `DHCP Configuration and Relay Agents`
   - **Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Devices

**What:** Add routers, switches, and PCs.

**Devices to add:**
- **3 Routers** (R0 - DHCP Server, R1 - Relay, R2 - Relay)
- **2 Switches** (SW1, SW2)
- **4 PCs** (PC1, PC2, PC3, PC4)

---

### Step 3: Connect Devices

**What:** Create all required connections.

**Connections:**
- R0 Gi0/1 ↔ R1 Gi0/1 (backbone)
- R0 Gi0/2 ↔ R2 Gi0/2 (backbone)
- R1 Gi0/0 ↔ SW1 Gi0/0 (trunk to VLAN switch)
- R2 Gi0/0 ↔ SW2 Gi0/0 (trunk to VLAN switch)
- SW1 Gi1/1 ↔ PC1 (VLAN 10)
- SW1 Gi1/2 ↔ PC2 (VLAN 20)
- SW2 Gi1/1 ↔ PC3 (VLAN 10)
- SW2 Gi1/2 ↔ PC4 (VLAN 20)

---

### Step 4: Start Lab

**What:** Boot all devices.

**How to:**
1. Right-click lab name
2. Select **Start Lab**
3. Wait for all devices to boot
4. Verify green status

---

## ⚙️ DHCP Relay Configuration

> **Purpose:** Configure DHCP relay agents on routers to forward DHCP requests to central server.

### Step 5: Configure DHCP Server (R0)

**What:** Set up central DHCP server with multiple pools.

**Commands:**
```bash
enable
configure terminal
hostname R0

interface gigabitEthernet 0/0
ip address 192.168.100.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
ip address 1.1.1.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/2
ip address 2.1.1.1 255.255.255.252
no shutdown
exit

end
```

**Verification:**
```bash
show ip interface brief
```

> **ℹ️ Note:** DHCP server is on its backbone interface, not serving directly.

---

### Step 6: Configure Relay on R1

**What:** Configure R1 to relay DHCP requests from Domain 1 to R0.

**Commands:**
```bash
enable
configure terminal
hostname R1

! Configure backbone link
interface gigabitEthernet 0/1
ip address 1.1.1.2 255.255.255.252
no shutdown
exit

! Create VLAN 10 SVI
interface gigabitEthernet 0/0
no ip address
no shutdown
exit

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 10.1.1.1 255.255.255.0
ip helper-address 192.168.100.1
exit

! Create VLAN 20 SVI
interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 20.1.1.1 255.255.255.0
ip helper-address 192.168.100.1
exit

end
```

**Key Command Explanation:**

| Command | Purpose |
|---------|---------|
| **ip helper-address 192.168.100.1** | Forward DHCP broadcasts to server |
| **encapsulation dot1Q XX** | Tag VLAN traffic |
| **ip address X.X.X.1** | Gateway for VLAN |

> **✅ Important:** `ip helper-address` is the magic command for DHCP relay!

---

### Step 7: Configure Relay on R2

**What:** Configure R2 with same relay setup for Domain 2.

**Commands:**
```bash
enable
configure terminal
hostname R2

interface gigabitEthernet 0/2
ip address 2.1.1.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet 0/0
no ip address
no shutdown
exit

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 10.2.1.1 255.255.255.0
ip helper-address 192.168.100.1
exit

interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 20.2.1.1 255.255.255.0
ip helper-address 192.168.100.1
exit

end
```

> **✅ Checkpoint:** All relay agents configured!

---

## 📚 Multiple DHCP Pools

> **Purpose:** Configure DHCP server to serve all VLANs with appropriate address pools.

### Step 8: Configure DHCP Pool for VLAN 10

**What:** Create DHCP pool for VLAN 10 across both domains.

**Commands on R0:**
```bash
enable
configure terminal

! Pool for VLAN 10 Domain 1
ip dhcp pool VLAN10_D1
network 10.1.1.0 255.255.255.0
default-router 10.1.1.1
dns-server 8.8.8.8 8.8.4.4
lease 1
exit

! Pool for VLAN 10 Domain 2
ip dhcp pool VLAN10_D2
network 10.2.1.0 255.255.255.0
default-router 10.2.1.1
dns-server 8.8.8.8 8.8.4.4
lease 1
exit

end
```

**Pool Details Explained:**

| Parameter | Purpose | Value |
|-----------|---------|-------|
| **network** | Address range | 10.1.1.0/24 |
| **default-router** | Gateway for clients | 10.1.1.1 |
| **dns-server** | DNS servers | Google DNS |
| **lease** | Lease time (hours) | 1 hour |

---

### Step 9: Configure DHCP Pool for VLAN 20

**What:** Create DHCP pool for VLAN 20.

**Commands on R0:**
```bash
enable
configure terminal

! Pool for VLAN 20 Domain 1
ip dhcp pool VLAN20_D1
network 20.1.1.0 255.255.255.0
default-router 20.1.1.1
dns-server 8.8.8.8 8.8.4.4
lease 1
exit

! Pool for VLAN 20 Domain 2
ip dhcp pool VLAN20_D2
network 20.2.1.0 255.255.255.0
default-router 20.2.1.1
dns-server 8.8.8.8 8.8.4.4
lease 1
exit

! Exclude gateway addresses
ip dhcp excluded-address 10.1.1.1
ip dhcp excluded-address 10.2.1.1
ip dhcp excluded-address 20.1.1.1
ip dhcp excluded-address 20.2.1.1

end
```

> **ℹ️ Note:** Always exclude router IP addresses from DHCP pool!

---

### Step 10: Configure Excluded Addresses

**What:** Prevent DHCP from assigning router IPs.

**Commands on R0:**
```bash
enable
configure terminal

ip dhcp excluded-address 10.1.1.1 10.1.1.10
ip dhcp excluded-address 10.2.1.1 10.2.1.10
ip dhcp excluded-address 20.1.1.1 20.1.1.10
ip dhcp excluded-address 20.2.1.1 20.2.1.10

end
```

**Why exclude?**
- Prevents IP conflicts with router
- Reserves space for static devices (printers, servers)
- Best practice for production networks

> **✅ Checkpoint:** DHCP pools configured for all VLANs!

---

## 🔒 DHCP Snooping Security

> **Purpose:** Enable DHCP snooping to prevent rogue DHCP servers.

### Step 11: Enable DHCP Snooping on Switch

**What:** Configure DHCP snooping on SW1.

**Commands on SW1:**
```bash
enable
configure terminal

! Enable DHCP snooping globally
ip dhcp snooping
ip dhcp snooping vlan 10 20

! Set trusted ports (connected to router/server)
interface gigabitEthernet 0/0
ip dhcp snooping trust
exit

! Set untrusted ports (client access)
interface gigabitEthernet 1/1
ip dhcp snooping limit rate 10
exit

interface gigabitEthernet 1/2
ip dhcp snooping limit rate 10
exit

end
```

**DHCP Snooping Explained:**

| Setting | Purpose |
|---------|---------|
| **ip dhcp snooping** | Enable snooping globally |
| **ip dhcp snooping vlan 10 20** | Apply to specific VLANs |
| **ip dhcp snooping trust** | Trust ports (server/relay) |
| **limit rate 10** | Limit DHCP packets per port |

> **🔒 Security:** Prevents rogue DHCP servers on untrusted ports!

---

### Step 12: Enable DHCP Snooping on Switch 2

**What:** Configure DHCP snooping on SW2.

**Commands:** (Same as Step 11 for SW2)

> **✅ Checkpoint:** DHCP security enabled!

---

## 🖥️ PC Configuration

> **Purpose:** Configure PCs to receive DHCP addresses automatically.

### Step 13: Configure DHCP on All PCs

**What:** Set all PCs to use DHCP (default in VPCS).

**Commands on PC1:**
```bash
dhcp
```

**Commands on PC2:**
```bash
dhcp
```

**Commands on PC3:**
```bash
dhcp
```

**Commands on PC4:**
```bash
dhcp
```

**Verify:**
```bash
show ip
```

> **✅ Note:** VPCS uses DHCP by default, so just run `dhcp` command to request.

---

## 🔍 Testing DHCP

> **Purpose:** Verify DHCP is working across all domains.

### Step 14: Test DHCP on PC1

**What:** Verify PC1 receives DHCP address from VLAN 10 pool.

**Commands:**
```bash
# Request DHCP address
dhcp

# View assigned IP
show ip

# Expected: 10.1.1.x (from Domain 1 VLAN 10 pool)
```

**Expected Output:**
```
NAME        : PC1
IP/MASK     : 10.1.1.11/24
GATEWAY     : 10.1.1.1
DNS         : 8.8.8.8, 8.8.4.4
DHCP SERVER : 192.168.100.1
LEASE TIME  : 1 hour
```

> **✅ Success:** DHCP relay is working!

---

### Step 15: Test DHCP on PC3

**What:** Verify PC3 (Domain 2) also receives DHCP address.

**Commands:**
```bash
dhcp
show ip
# Expected: 10.2.1.x (from Domain 2 VLAN 10 pool)
```

> **✅ Success:** Cross-domain DHCP relay works!

---

### Step 16: Test DHCP Renewal

**What:** Test lease renewal and address persistence.

**Commands on PC1:**
```bash
dhcp
show ip
# Note the IP address

# Wait 30 seconds, then
dhcp
show ip
# Should be same or similar address
```

> **ℹ️ Note:** DHCP renewal maintains stable addressing for services.

---

## ✔️ Verification

> **Purpose:** Verify DHCP server and relay operation.

### Step 17: Check DHCP Bindings on Server

**What:** View all active DHCP leases.

**Commands on R0:**
```bash
show ip dhcp binding
```

**Expected Output:**
```
Bindings from all pools not associated with VRF:

IP address          Client-ID/              Lease expiration        Type       State
                    Hardware address/
                    User name
10.1.1.11           0100.50.56.8f.39.01     Dec 02 2025 10:23 PM    Automatic  Active
10.1.1.12           0100.50.56.8f.39.02     Dec 02 2025 10:23 PM    Automatic  Active
10.2.1.11           0100.50.56.8f.39.03     Dec 02 2025 10:23 PM    Automatic  Active
20.1.1.11           0100.50.56.8f.39.04     Dec 02 2025 10:23 PM    Automatic  Active
```

| Field | Meaning |
|-------|---------|
| **IP address** | Assigned to client |
| **Client-ID** | MAC address |
| **Lease expiration** | When address expires |
| **Type** | Automatic = DHCP |
| **State** | Active = assigned |

---

### Step 18: Check DHCP Pool Status

**What:** View DHCP pool statistics.

**Commands on R0:**
```bash
show ip dhcp pool
```

**Expected Output:**
```
Pool VLAN10_D1 :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 2
 Available addresses            : 252
 Management address             : 10.1.1.1
 Is management address excluded : Yes
 Exclusion range(s)             : 10.1.1.1-10.1.1.10
```

---

### Step 19: Verify Relay Agent Bindings

**What:** Check relay agent statistics on routers.

**Commands on R1:**
```bash
show ip dhcp relay information statistics
```

**Expected Output:**
```
DHCP Relay Agent Statistics for router R1:

Packets relayed: 4
DHCP DISCOVER packets received: 4
DHCP OFFER packets sent: 4
DHCP REQUEST packets received: 2
DHCP ACK packets sent: 2
```

> **✅ Checkpoint:** All DHCP services verified and operational!

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **PC gets no IP address** | DHCP relay not working | Verify `ip helper-address` on router |
| **PC gets 169.x.x.x (APIPA)** | DHCP server unreachable | Check backbone link connectivity |
| **Lease expires immediately** | Lease time too short | Increase lease time to reasonable value |
| **DHCP pool exhausted** | Too many clients | Expand pool size or reduce lease time |
| **PC gets wrong IP (wrong VLAN)** | VLAN tagging incorrect | Verify subinterface VLAN IDs match |
| **Rogue DHCP detected** | Someone added DHCP server | Check DHCP snooping, untrust ports |

### Debugging Commands

```bash
# Enable DHCP debugging (on server)
debug ip dhcp server packets

# Check interface helpers
show ip helper-address

# View DHCP statistics
show ip dhcp statistics

# Check DHCP configuration
show running-config | include dhcp

# Test connectivity to server
ping 192.168.100.1
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured DHCP relay agents across multiple domains
- ✅ Created multiple DHCP pools for different VLANs
- ✅ Implemented DHCP snooping for security
- ✅ Verified automatic IP address assignment
- ✅ Tested cross-domain DHCP functionality
- ✅ Mastered DHCP troubleshooting techniques

### Key Concepts Learned

| Concept | Description | Key Takeaway |
|---------|-------------|--------------|
| **DHCP Server** | Central address assignment | One server can serve entire network |
| **DHCP Relay Agent** | Forwards broadcasts | Extends DHCP across VLANs/domains |
| **Helper Address** | `ip helper-address` command | Enables relay function on router |
| **DHCP Pool** | Address range per VLAN | Pools prevent IP conflicts |
| **DHCP Snooping** | Security for DHCP | Prevents rogue DHCP servers |
| **Lease Time** | Duration of IP assignment | Balance between stability and flexibility |
| **Excluded Addresses** | Reserved IPs | Prevents router/server conflicts |

### DHCP Quick Reference

```bash
# Server setup
ip dhcp pool POOL_NAME
network X.X.X.0 255.255.255.0
default-router X.X.X.1
dns-server 8.8.8.8

# Relay setup (on router subinterface)
ip helper-address 192.168.100.1

# Verification
show ip dhcp binding
show ip dhcp pool
show ip dhcp relay information statistics
```

### What's Next?

**Choose your next topic:**

1. **🌐 DNS Configuration** - Continue with DNS lab
2. **🔄 NAT Configuration** - Learn address translation
3. **🔀 Combine All Three** - Integrated DHCP+DNS+NAT lab
4. **🔐 DHCP Security** - Deep dive into snooping
5. **⚡ Advanced DHCP** - Relay agent options and debugging

---

## 📚 Useful Resources

- [Cisco DHCP Configuration Guide](https://www.cisco.com/c/en/us/support/docs/ip-mobility-and-wireless/dhcp-protocol/13847-dhcp-faq.html)
- [DHCP Relay Agent Documentation](https://www.cisco.com/c/en/us/support/docs/ip-mobilit-and-wireless/dhcp-protocol/13722-38.html)
- [DHCP Snooping Best Practices](https://www.cisco.com/c/en/us/support/docs/lan-switching-and-vlan/dhcp-snooping.html)
- [RFC 2131 - DHCP Protocol](https://tools.ietf.org/html/rfc2131)

---

## 💡 Best Practices

### ✅ DHCP Best Practices

- **Exclude Router IPs:** Always exclude gateway and server addresses
- **Reasonable Lease Time:** 1-8 hours typical (depends on mobility)
- **Centralized Servers:** One DHCP server per domain (HA via failover)
- **Documented Pools:** Keep pool documentation current
- **Monitor Utilization:** Set alerts when pools exceed 80% utilization
- **Snooping Enabled:** Mandatory in production networks
- **Backup DHCP:** Configure failover for high availability

### 🔐 DHCP Security

- **Enable Snooping:** Block rogue DHCP servers
- **Trust Ports Carefully:** Only trust server/relay connections
- **Rate Limiting:** Limit DHCP packets per port
- **DHCP Starvation:** Monitor for DoS attacks
- **Authentication:** Use DHCP authentication in secure environments

### ⚡ DHCP Optimization

- **Pool Sizing:** Size pools for peak concurrent users +20%
- **Lease Renewal:** 50% of lease time (RFC standard)
- **DNS Configuration:** Push corporate DNS via DHCP
- **Default Gateway:** Always specify correct gateway per VLAN
- **Option 82:** Enable relay agent information for tracking

---

✅ **DHCP lab complete! You now understand automatic IP assignment across enterprises.** 🎓

**Ready for NAT or DNS next?** 🚀
