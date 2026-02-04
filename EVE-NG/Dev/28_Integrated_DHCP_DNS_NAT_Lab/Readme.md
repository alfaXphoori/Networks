# 🌐 Integrated DHCP + DNS + NAT Enterprise Lab

> Master lab combining automatic IP assignment (DHCP), domain name resolution (DNS), and address translation (NAT) in a realistic enterprise network topology.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [Architecture Overview](#architecture-overview)
6. [DHCP Configuration](#dhcp-configuration)
7. [DNS Configuration](#dns-configuration)
8. [NAT Configuration](#nat-configuration)
9. [Integration & Verification](#integration--verification)
10. [End-to-End Testing](#end-to-end-testing)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master the three core services that operate in 100% of production networks.

### By the end of this lab, you will:

- ✅ Configure DHCP with multiple pools across VLANs
- ✅ Configure DNS for internal and external domains
- ✅ Implement all three NAT types (static, dynamic, PAT)
- ✅ Integrate DHCP with DNS (DNS via DHCP options)
- ✅ Handle NAT with DNS/DHCP traffic
- ✅ Build complete enterprise network stack
- ✅ Troubleshoot complex multi-service issues
- ✅ Monitor all three services simultaneously
- ✅ Understand real-world service dependencies

---

## ✅ Prerequisites

> **Purpose:** Ensure you've mastered foundational labs.

### Required Labs

| Lab | Reason | Link |
|-----|--------|------|
| **15_DHCP_Services** | DHCP configuration | Learn DHCP server/relay |
| **27_DNS_Services** | DNS resolution | Learn DNS server |
| **20_NAT_Configuration** | Address translation | Learn all NAT types |
| **05_VLAN** | VLAN fundamentals | Multi-domain setup |
| **08_Basic_Routing** | Router configuration | Core networking |

### Network Concepts Needed

- ✅ DHCP pools and relay agents
- ✅ DNS recursive queries and forwarding
- ✅ Static, dynamic, and PAT translation
- ✅ ACLs for traffic qualification
- ✅ Route-map for selective routing
- ✅ OSPF for dynamic routing

---

## 📊 Lab Topology

> **Purpose:** Enterprise network with multiple domains, internal/external separation, and complete service stack.

```
╔════════════════════════════════════════════════════════════════╗
║              ENTERPRISE NETWORK WITH DHCP+DNS+NAT             ║
╚════════════════════════════════════════════════════════════════╝

INTERNAL NETWORK                    EXTERNAL NETWORK
(Private IP Space)                  (Public IP Space)

┌──────────────────────┐
│ DOMAIN-A (VLAN 10)   │
│ 10.0.10.0/24         │
│                      │
│ DHCP Pool:           │      ┌─────────────┐
│ 10.0.10.50-100       │      │ ISP DNS     │
│ Gateway: 10.0.10.1   │◄────►│ 8.8.8.8     │
│ DNS: 10.0.10.1       │      │             │
│ Domain: acme.local   │      │ External    │
│                      │      │ Users       │
│ PC-A1                │      └─────────────┘
│ PC-A2 (via DHCP)     │
└──────────────────────┘
         │
    ┌────▼──────┐
    │ Core       │
    │ Router R0  │     ┌─────────────────────────────────┐
    │ 10.0.100.1 │────►│ NAT Translator                  │
    │            │     │ Inside (10.0.0.0/16)           │
    │ Functions: │     │ Outside (203.0.113.0/25)       │
    │ - DHCP     │     │                                 │
    │   Server   │     │ Static NAT:                     │
    │ - DNS      │     │ 10.0.10.100 ◄─► 203.0.113.10  │
    │   Server   │     │ (Web Server)                    │
    │ - NAT      │     └─────────────────────────────────┘
    │   Gateway  │              │
    └────┬──────┘              │ External Link
         │                     │ 203.0.113.0/24
    ┌────▼──────┐              │
    │ Core       │              │
    │ Router R1  │◄─────────────┘
    │ 10.0.20.1  │
    │ DHCP Relay │
    │ 203.0.113.2│
    └────┬──────┘
         │
┌────────▼─────────┐
│ DOMAIN-B         │
│ (VLAN 20)        │
│ 10.0.20.0/24     │
│                  │
│ DHCP Pool:       │
│ 10.0.20.50-100   │
│ Gateway: 10.0.20.1
│ DNS: 10.0.10.1   │ (via DHCP option 6)
│ Domain: acme.local
│                  │
│ PC-B1            │
│ PC-B2 (via DHCP) │
└──────────────────┘

Service Integration Map:

PC-A1 (10.0.10.50 via DHCP)
  │
  ├─ DHCP: Requests 10.0.10.50 from R0
  ├─ DNS Option 6: Receives 10.0.10.1 as DNS server
  └─ Queries: web1.acme.local
       │
       └─→ NAT: Outside request 203.0.113.10
           │
           └─→ NAT Translates to 10.0.10.100
               (Web server responds)
```

### Key Network Details

| Device | Interface | IP Address | Role |
|--------|-----------|-----------|------|
| **R0** | Gi0/0 | 10.0.10.1 | DHCP Server (Domain-A) |
| **R0** | Gi0/1 | 10.0.100.1 | Core backbone |
| **R0** | Gi0/2 | 203.0.113.1 | NAT outside |
| **R1** | Gi0/0 | 10.0.100.2 | Core backbone |
| **R1** | Gi0/1 | 10.0.20.1 | DHCP Relay (Domain-B) |
| **R1** | Gi0/2 | 203.0.113.2 | Secondary NAT |
| **PC-A1** | eth0 | 10.0.10.10 | Manual config (testing) |
| **PC-A2** | eth0 | 10.0.10.50-100 | DHCP client |
| **PC-B1** | eth0 | 10.0.20.10 | Manual config (testing) |
| **PC-B2** | eth0 | 10.0.20.50-100 | DHCP client |
| **Web Server** | eth0 | 10.0.10.100 | Server (static NAT target) |

---

## 🔧 Creating the Lab

> **Purpose:** Build enterprise topology.

### Step 1: Create Lab

**What:** Create EVE-NG lab for integrated services.

**How to:**
1. Log into EVE-NG
2. Click **Add Lab**
3. Configure:
   - **Lab Name**: `DHCP_DNS_NAT_Enterprise`
   - **Description**: `Integration of DHCP, DNS, and NAT`
   - **Version**: `8.5`
4. Click **Create**

---

### Step 2: Add Devices

**Devices Required:**
- **2 Routers** (R0 - Core/DHCP/DNS/NAT, R1 - Relay/NAT)
- **2 Switches** (SW1, SW2 - one per domain)
- **4 PCs** (PC-A1, PC-A2, PC-B1, PC-B2)
- **1 Server** (Web server for NAT testing)

---

### Step 3: Connect Topology

**Connections:**
- R0 Gi0/0 ↔ SW1 Gi0/0 (Domain-A)
- R0 Gi0/1 ↔ R1 Gi0/0 (Core backbone)
- R0 Gi0/2 ↔ ISP (External)
- R1 Gi0/1 ↔ SW2 Gi0/0 (Domain-B)
- R1 Gi0/2 ↔ ISP (External alternative)
- SW1: PC-A1, PC-A2, Web Server
- SW2: PC-B1, PC-B2

---

### Step 4: Start Lab

**What:** Boot all devices and wait for boot completion.

---

## 🏗️ Architecture Overview

> **Purpose:** Understand service flow and dependencies.

### Service Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ NETWORK APPLICATION LAYER                                   │
│                                                              │
│ Clients want to reach: web1.acme.local                     │
│ BUT: How does client get IP? How find server IP?           │
│      How does response route back?                          │
└──────────────────────┬──────────────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
    ┌────────────┐ ┌────────────┐ ┌────────────┐
    │ DHCP       │ │ DNS        │ │ NAT        │
    │ LAYER      │ │ LAYER      │ │ LAYER      │
    ├────────────┤ ├────────────┤ ├────────────┤
    │ Purpose:   │ │ Purpose:   │ │ Purpose:   │
    │ Assign IP  │ │ Resolve    │ │ Translate  │
    │            │ │ Names→IPs  │ │ Addresses  │
    │ Server:    │ │ Server:    │ │ Gateway:   │
    │ 10.0.10.1  │ │ 10.0.10.1  │ │ 203.0.113.1
    └──────┬─────┘ └─────┬──────┘ └──────┬─────┘
           │             │              │
           │             ▼              │
           │    ┌─────────────────────┐ │
           └────►│ DNS forwards to    │ │
                 │ ISP (8.8.8.8)      │ │
                 │ if not local       │ │
                 └─────────────────────┘ │
                                         │
                 ┌───────────────────────┘
                 │
    ┌────────────▼─────────────┐
    │ ROUTING LAYER            │
    │                          │
    │ Route traffic between:   │
    │ - Domains (10.0.10/20)   │
    │ - Internal ↔ External    │
    │ - NAT transformations    │
    └──────────────────────────┘
```

### Service Dependencies

| Layer | Service | Input | Output | Impact |
|-------|---------|-------|--------|--------|
| **7** | Applications | "web1.acme.local" | Connection | None |
| **6** | DNS | Query: web1.acme.local | IP: 10.0.10.100 | Can't resolve = connection fails |
| **3** | NAT | 10.0.10.100 | 203.0.113.10 | Route fails = no external access |
| **3** | Routing | 203.0.113.0/24 | 10.0.10.0/24 | Route fails = no path |
| **2** | DHCP | Request IP | 10.0.10.50 | No IP = no network |

---

## ⚙️ DHCP Configuration

> **Purpose:** Enable automatic IP assignment with DNS option.

### Step 5: Configure R0 as DHCP Server

**What:** Enable DHCP on R0 for Domain-A.

**Commands:**
```bash
enable
configure terminal
hostname R0

! Interface configuration
interface gigabitEthernet 0/0
description Domain-A (VLAN 10)
ip address 10.0.10.1 255.255.255.0
no shutdown
exit

! Core backbone
interface gigabitEthernet 0/1
description Core Backbone
ip address 10.0.100.1 255.255.255.0
no shutdown
exit

! External link
interface gigabitEthernet 0/2
description External Link
ip address 203.0.113.1 255.255.255.0
no shutdown
exit

! Enable DHCP server
ip dhcp server

! Create DHCP pool for Domain-A
ip dhcp pool DOMAIN_A
network 10.0.10.0 255.255.255.0
default-router 10.0.10.1
dns-server 10.0.10.1
domain-name acme.local
lease 0 8 0
excluded-address 10.0.10.1 10.0.10.49
exit

end
```

---

### Step 6: Configure R1 as DHCP Relay

**What:** R1 relays DHCP requests to R0.

**Commands on R1:**
```bash
enable
configure terminal
hostname R1

! Interface configuration
interface gigabitEthernet 0/0
description Core Backbone
ip address 10.0.100.2 255.255.255.0
no shutdown
exit

interface gigabitEthernet 0/1
description Domain-B (VLAN 20)
ip address 10.0.20.1 255.255.255.0
! Enable DHCP relay
ip helper-address 10.0.10.1
no shutdown
exit

interface gigabitEthernet 0/2
description External Link
ip address 203.0.113.2 255.255.255.0
no shutdown
exit

end
```

---

### Step 7: Create DHCP Pool for Domain-B

**What:** Add DHCP pool on R0 for Domain-B (relayed through R1).

**Commands on R0:**
```bash
enable
configure terminal

! Create DHCP pool for Domain-B
ip dhcp pool DOMAIN_B
network 10.0.20.0 255.255.255.0
default-router 10.0.20.1
dns-server 10.0.10.1
domain-name acme.local
lease 0 8 0
excluded-address 10.0.20.1 10.0.20.49
exit

end
```

---

### Step 8: Verify DHCP Configuration

**What:** Confirm DHCP is ready.

**Commands on R0:**
```bash
show ip dhcp pool
show ip dhcp binding
```

**Expected Output:**
```
DHCP Pool: DOMAIN_A
Network: 10.0.10.0/24
Gateway: 10.0.10.1
DNS: 10.0.10.1
Domain: acme.local
Leases: 0/50

DHCP Pool: DOMAIN_B
Network: 10.0.20.0/24
Gateway: 10.0.20.1
DNS: 10.0.10.1
Domain: acme.local
Leases: 0/50
```

---

## 🌐 DNS Configuration

> **Purpose:** Enable name resolution with DHCP integration.

### Step 9: Configure R0 as DNS Server

**What:** Enable DNS on R0 and create local entries.

**Commands on R0:**
```bash
enable
configure terminal

! Enable DNS server
ip dns server
ip dns forwarding

! Set upstream DNS for external queries
ip name-server 8.8.8.8
ip name-server 8.8.4.4

! Configure domain
ip domain-name acme.local

! Create static DNS entries for internal servers
ip host web1.acme.local 10.0.10.100
ip host mail.acme.local 10.0.10.101
ip host ftp.acme.local 10.0.10.102
ip host gateway.acme.local 10.0.10.1

! Enable DNS cache
ip dns cache enable
ip dns cache size 200
ip dns cache ttl 3600

end
```

---

### Step 10: Verify DNS Configuration

**What:** Confirm DNS is operational.

**Commands on R0:**
```bash
show hosts
show ip dns servers
show ip dns statistics
```

**Expected Output:**
```
DNS Servers: 8.8.8.8, 8.8.4.4
DNS Cache: 200 entries, 3600 TTL

Hosts:
web1.acme.local = 10.0.10.100
mail.acme.local = 10.0.10.101
ftp.acme.local = 10.0.10.102
gateway.acme.local = 10.0.10.1
```

---

## 🔄 NAT Configuration

> **Purpose:** Implement address translation for external connectivity.

### Step 11: Configure R0 NAT - Static NAT

**What:** Static NAT for web server (10.0.10.100 ↔ 203.0.113.10).

**Commands on R0:**
```bash
enable
configure terminal

! Mark interfaces
interface gigabitEthernet 0/0
ip nat inside
exit

interface gigabitEthernet 0/2
ip nat outside
exit

! Static NAT for web server
ip nat inside source static 10.0.10.100 203.0.113.10

! Static NAT for mail server
ip nat inside source static 10.0.10.101 203.0.113.11

end
```

---

### Step 12: Configure R0 NAT - Dynamic NAT/PAT

**What:** Dynamic NAT for all other internal clients.

**Commands on R0:**
```bash
enable
configure terminal

! Create ACL for inside addresses to NAT
access-list 1 permit 10.0.0.0 0.0.255.255

! Create NAT pool for external addresses
ip nat pool EXTERNAL_POOL 203.0.113.20 203.0.113.30 netmask 255.255.255.0

! Dynamic NAT (one-to-one)
ip nat inside source list 1 pool EXTERNAL_POOL

! Or use PAT (many-to-one) - more common
no ip nat inside source list 1 pool EXTERNAL_POOL
ip nat inside source list 1 interface gigabitEthernet 0/2 overload

end
```

---

### Step 13: Configure NAT OSPF Integration

**What:** Ensure NAT doesn't break OSPF routing.

**Commands on R0:**
```bash
enable
configure terminal

! Prevent NAT from affecting OSPF updates
ip nat inside source route-map NAT_FILTER list 1 interface gigabitEthernet 0/2 overload

! Route map to exclude OSPF
route-map NAT_FILTER permit 10
match ip address 2
exit

! ACL 2 - exclude OSPF neighbors
access-list 2 deny 10.0.100.2
access-list 2 permit 10.0.0.0 0.0.255.255

end
```

---

### Step 14: Verify NAT Configuration

**What:** Confirm NAT is active.

**Commands on R0:**
```bash
show ip nat statistics
show ip nat translations
show access-lists
```

**Expected Output:**
```
NAT Statistics:
  Total inside addresses: 1
  Total outside addresses: 1
  Hits: 0
  Misses: 0
  Expired: 0
  Checksum failures: 0
  Translations active: 0

NAT Translations:
  (Populated after connections made)
```

---

## 🔗 Integration & Verification

> **Purpose:** Test DHCP→DNS→NAT pipeline.

### Step 15: Configure PC Clients

**What:** Set up PCs to receive DHCP configuration.

**Commands on PC-A2 (DHCP client):**
```bash
# Get IP via DHCP
dhcp

# Verify DHCP received configuration
show ip
```

**Expected Output:**
```
NAME        : PC-A2
IP/MASK     : 10.0.10.50/24 (from DHCP)
GATEWAY     : 10.0.10.1
DNS         : 10.0.10.1 (from DHCP option 6)
DHCP SERVER : 10.0.10.1
```

**Commands on PC-B2 (DHCP from relay):**
```bash
dhcp
show ip
```

**Expected Output:**
```
NAME        : PC-B2
IP/MASK     : 10.0.20.50/24 (from R1 relay)
GATEWAY     : 10.0.20.1
DNS         : 10.0.10.1 (from DHCP option 6)
DHCP SERVER : 10.0.10.1 (relayed through R1)
```

---

### Step 16: Test DNS Resolution

**What:** Verify DNS works from both domains.

**Commands on PC-A2:**
```bash
# Test internal domain
nslookup web1.acme.local

# Test external domain
nslookup www.google.com
```

**Expected Output:**
```
Internal: web1.acme.local resolves to 10.0.10.100
External: www.google.com resolves to 142.251.32.46
```

**Commands on PC-B2:**
```bash
nslookup web1.acme.local
nslookup www.google.com
```

---

### Step 17: Test NAT Translation

**What:** Verify NAT transforms addresses.

**Commands on R0:**
```bash
# Monitor NAT translations in real-time
debug ip nat
```

**On PC-A2 connect to web server:**
```bash
telnet 10.0.10.100 80
```

**On R0 (watch NAT debug):**
```
Inside address: 10.0.10.50 → Outside address: 203.0.113.20
```

---

## ✅ End-to-End Testing

> **Purpose:** Complete service verification.

### Step 18: Test Full Service Chain

**What:** Trace DHCP → DNS → NAT → Connectivity.

**Scenario: PC-A2 connects to web server**

1. **DHCP Assignment:**
```bash
# PC-A2 boots and requests IP
dhcp

# R0 assigns 10.0.10.50
# Includes DNS option 6 = 10.0.10.1
```

2. **DNS Resolution:**
```bash
# PC-A2 queries DNS
nslookup web1.acme.local

# R0 responds: 10.0.10.100
```

3. **NAT Translation (if accessing externally):**
```bash
# External client connects to 203.0.113.10
# R0 translates to 10.0.10.100
# Web server responds through NAT
```

---

### Step 19: Monitor All Services

**What:** Real-time monitoring of integrated services.

**On R0 - Terminal 1 (DHCP):**
```bash
show ip dhcp binding
watch ip dhcp statistics
```

**On R0 - Terminal 2 (DNS):**
```bash
show ip dns cache
show ip dns statistics
```

**On R0 - Terminal 3 (NAT):**
```bash
show ip nat translations
debug ip nat
```

---

### Step 20: Verify Integration Points

**What:** Confirm services work together.

| Test | Command | Expected | Status |
|------|---------|----------|--------|
| **PC gets IP** | `show ip dhcp binding` | 10.0.10.50 assigned | ✓ |
| **PC gets DNS** | `show ip` on PC | DNS = 10.0.10.1 | ✓ |
| **DNS resolves** | `nslookup web1.acme.local` | Returns 10.0.10.100 | ✓ |
| **NAT active** | `show ip nat statistics` | Translations > 0 | ✓ |
| **Routing works** | `ping 10.0.20.10` | From Domain-A | ✓ |

---

## 🆘 Troubleshooting

> **Purpose:** Diagnose and resolve integration issues.

### Issue: DHCP Not Assigning IPs

| Symptom | Diagnosis | Solution |
|---------|-----------|----------|
| PC shows `UNKNOWN` | DHCP disabled or unreachable | `show ip dhcp pool` on R0 |
| Wrong IP range | Wrong pool configured | Verify pool matches interface subnet |
| No gateway received | Missing `default-router` | Add `default-router` to pool |

**Debug:**
```bash
# On Router
debug ip dhcp server events
debug ip dhcp server packets

# On PC
dhcp
```

---

### Issue: DNS Not Resolving

| Symptom | Diagnosis | Solution |
|---------|-----------|----------|
| "Host not found" | DNS server not set | Verify PC has DNS via `show ip` |
| Local names fail | Host entries wrong | `show ip host` on router |
| External names fail | Forwarding not enabled | `show ip dns servers` |

**Debug:**
```bash
# On Router
debug ip dns all

# On PC
nslookup www.google.com
```

---

### Issue: NAT Not Working

| Symptom | Diagnosis | Solution |
|---------|-----------|----------|
| Can't reach server | Interface not marked | `show ip nat statistics` |
| Translation fails | ACL blocking traffic | `show access-lists` |
| Wrong address | Static NAT wrong | Verify with `show ip nat translations` |

**Debug:**
```bash
# On Router
debug ip nat
debug ip nat detailed

# Monitor translations
show ip nat statistics
```

---

### Comprehensive Troubleshooting Checklist

```bash
# DHCP Troubleshooting
show ip dhcp pool
show ip dhcp binding
show ip dhcp statistics
debug ip dhcp server events

# DNS Troubleshooting
show ip dns servers
show ip host
show ip dns cache
show ip dns statistics
debug ip dns all

# NAT Troubleshooting
show ip nat statistics
show ip nat translations
show access-lists
debug ip nat

# Routing Troubleshooting
show ip route
show ip ospf neighbor
ping 10.0.20.1
trace 10.0.20.10
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured DHCP with automatic IP assignment
- ✅ Implemented DNS for name resolution
- ✅ Set up NAT for address translation
- ✅ Integrated all three services
- ✅ Verified DHCP→DNS→NAT pipeline
- ✅ Tested multi-domain connectivity
- ✅ Monitored all services simultaneously
- ✅ Troubleshot integration issues

### Service Integration Summary

| Service | Role | Key Command | Integration |
|---------|------|-------------|-------------|
| **DHCP** | Assign IPs | `ip dhcp pool` | Includes DNS in options |
| **DNS** | Resolve names | `ip dns server` | Forwarding for external |
| **NAT** | Translate addresses | `ip nat inside source` | Transparent to clients |

### Enterprise Network Architecture

```
Client Workflow:
1. Boot → DHCP → Get IP + DNS server info
2. Query → DNS → Resolve name to IP
3. Connect → Routing → Find path to destination
4. Cross-domain → NAT → Translate address if needed
5. Response → Return translated back to client
```

### Key Integration Commands

```bash
# DHCP with DNS option
ip dhcp pool DOMAIN_A
dns-server 10.0.10.1
domain-name acme.local

# DNS with forwarding
ip dns server
ip dns forwarding
ip name-server 8.8.8.8

# NAT transparent to both
ip nat inside source static 10.0.10.100 203.0.113.10
ip nat inside source list 1 interface Gi0/2 overload
```

### What's Next?

**Advanced Topics:**
1. 🔐 **DHCP Security** - DHCP snooping, starvation attacks
2. 🔐 **DNS Security** - DNSSEC, DNS filtering
3. 🔐 **NAT Concerns** - End-to-end encryption impacts
4. 📊 **Service Analytics** - Monitoring and troubleshooting
5. 🌐 **High Availability** - DHCP failover, DNS redundancy

### Enterprise Deployment Readiness

You now understand the core services deployed in **100% of production networks**:
- ✅ DHCP (99% of networks)
- ✅ DNS (100% of networks)
- ✅ NAT (95% of networks)

**Job Market Impact:**
- These three services account for 40% of network engineer troubleshooting
- Understanding their integration is critical for any network role
- Real-world scenarios almost always involve all three together

---

✅ **Congratulations! You've mastered the enterprise service stack!** 🎓

**You're now ready for advanced topics like MPLS, BGP, or specialized areas like cybersecurity!** 🚀
