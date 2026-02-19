# 🌐 Network Address Translation (NAT/PAT) Lab

> Complete hands-on lab covering Static NAT, Dynamic NAT, PAT (NAT Overload), and Port Forwarding for Internet connectivity and address conservation.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [NAT Fundamentals](#nat-fundamentals)
4. [Lab Topology](#lab-topology)
5. [Creating the Lab](#creating-the-lab)
6. [Static NAT Configuration](#static-nat-configuration)
7. [Dynamic NAT Configuration](#dynamic-nat-configuration)
8. [PAT Configuration (NAT Overload)](#pat-configuration-nat-overload)
9. [Port Forwarding](#port-forwarding)
10. [Verification & Troubleshooting](#verification--troubleshooting)
11. [Real-World Scenarios](#real-world-scenarios)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master Network Address Translation for Internet connectivity, IP address conservation, and security.

### By the end of this lab, you will:

- ✅ Understand NAT types and use cases
- ✅ Configure Static NAT (one-to-one mapping)
- ✅ Configure Dynamic NAT (pool-based translation)
- ✅ Configure PAT/NAT Overload (many-to-one with ports)
- ✅ Implement Port Forwarding for servers
- ✅ Provide Internet access to internal networks
- ✅ Conserve public IP addresses
- ✅ Verify NAT translations
- ✅ Troubleshoot NAT issues
- ✅ Understand inside/outside interfaces

---

## ✅ Prerequisites

> **Purpose:** Ensure you have necessary knowledge and resources.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **IP Addressing** | NAT translates IP addresses | 04_Basic Switch Lab |
| **Router Configuration** | NAT configured on routers | 08_Basic_Routing Lab |
| **ACLs (Optional)** | NAT uses ACLs for matching | 13_ACL_Security Lab |
| **Routing Protocols** | NAT works with routing | 08-12 Routing Labs |

### Required Resources

- ✅ EVE-NG installed and running
- ✅ Cisco router images available (IOSv)
- ✅ VPCS or Linux VMs for testing
- ✅ Access to EVE-NG web interface
- ✅ Understanding of public vs private IP addresses

---

## 📚 NAT Fundamentals

> **Purpose:** Understand NAT concepts before configuration.

### What is NAT?

**Network Address Translation (NAT)** translates private IP addresses to public IP addresses, enabling:
- ✅ **Internet connectivity** for private networks
- ✅ **IP address conservation** (one public IP for many private IPs)
- ✅ **Security** through address hiding
- ✅ **Network flexibility** in addressing schemes

---

### NAT Terminology

| Term | Definition |
|------|-----------|
| **Inside Local** | Private IP address assigned to internal host |
| **Inside Global** | Public IP address representing internal host to outside |
| **Outside Local** | IP address of external host as seen from inside |
| **Outside Global** | Public IP address of external host |
| **Inside Interface** | Router interface connected to internal network |
| **Outside Interface** | Router interface connected to external network (Internet) |

---

### NAT Types Comparison

| Type | Description | Use Case | IP Usage |
|------|-------------|----------|----------|
| **Static NAT** | One-to-one permanent mapping | Web servers, mail servers | 1 public IP per internal host |
| **Dynamic NAT** | Pool-based temporary mapping | Temporary Internet access | Multiple public IPs for many hosts |
| **PAT (Overload)** | Many-to-one with port translation | Most common for Internet access | 1 public IP for thousands of hosts |
| **Port Forwarding** | External port maps to internal IP:port | Hosting services behind NAT | Part of PAT configuration |

---

### Private vs Public IP Addresses

**Private IP Ranges (RFC 1918):**
```
10.0.0.0/8        → 10.0.0.0 - 10.255.255.255
172.16.0.0/12     → 172.16.0.0 - 172.31.255.255
192.168.0.0/16    → 192.168.0.0 - 192.168.255.255
```

**Public IP Addresses:**
- All other IPv4 addresses
- Routable on the Internet
- Assigned by ISPs or obtained from RIRs
- Limited supply (IPv4 exhaustion)

---

### How NAT Works

```
OUTBOUND (Internal → Internet):
┌─────────────┐          ┌─────────────┐          ┌─────────────┐
│   PC1       │          │   Router    │          │  Internet   │
│ 192.168.1.10├─────────►│    NAT      ├─────────►│   Server    │
│             │          │             │          │ 8.8.8.8     │
└─────────────┘          └─────────────┘          └─────────────┘
     SRC: 192.168.1.10        SRC: 203.0.113.5         SRC: 203.0.113.5
     DST: 8.8.8.8             DST: 8.8.8.8             DST: 8.8.8.8
     
INBOUND (Internet → Internal):
┌─────────────┐          ┌─────────────┐          ┌─────────────┐
│   PC1       │          │   Router    │          │  Internet   │
│ 192.168.1.10│◄─────────┤    NAT      │◄─────────┤   Server    │
│             │          │             │          │ 8.8.8.8     │
└─────────────┘          └─────────────┘          └─────────────┘
     SRC: 8.8.8.8             SRC: 8.8.8.8             SRC: 8.8.8.8
     DST: 192.168.1.10        DST: 203.0.113.5         DST: 203.0.113.5

NAT Translation Table:
Inside Local       Inside Global
192.168.1.10       203.0.113.5
```

---

### PAT (Port Address Translation) Explained

**How PAT translates ports:**

```
Internal Network              NAT Router              Internet
─────────────────────────────────────────────────────────────
PC1: 192.168.1.10:55001  →   203.0.113.1:10001   →   8.8.8.8:80
PC2: 192.168.1.20:55002  →   203.0.113.1:10002   →   8.8.8.8:80
PC3: 192.168.1.30:55003  →   203.0.113.1:10003   →   8.8.8.8:80

NAT Translation Table:
Inside Local              Inside Global              Outside Global
192.168.1.10:55001        203.0.113.1:10001         8.8.8.8:80
192.168.1.20:55002        203.0.113.1:10002         8.8.8.8:80
192.168.1.30:55003        203.0.113.1:10003         8.8.8.8:80

Result: 3 different PCs share 1 public IP using different ports!
```

---

## 📊 Lab Topology

> **Purpose:** Multi-zone network with different NAT types for various scenarios.

![NAT Lab Topology](imgs/diagram.png)

```
     BRANCH OFFICE              EDGE ROUTER               INTERNET/ISP
     (Private: 192.168.1.0/24)  (NAT Gateway)             (Public Network)

         LAN                       R1                        ISP
    ┌─────────┐              ┌─────────┐              ┌─────────┐
    │ Switch  │              │ Gi0/1   │──────────────│ Gi0/0   │
    └────┬────┘              │         │ 203.0.113.0/30│   R2    │
         │                   │         │              │  ISP    │
    ┌────┴─────┬──────┐      │ Gi0/0   │              └────┬────┘
    │          │      │      └────┬────┘                   │
   PC1        PC2   Web_Sv       │                    ┌────┴────┐
 (.10/24)  (.20/24) (.100/24)    │                    │ Internet│
                                  │                    │ 8.8.8.8 │
                             192.168.1.0/24            └─────────┘

NAT Scenarios:
├─ PC1, PC2: PAT (share one public IP)
├─ Web_Sv: Static NAT (permanent public IP)
└─ Port Forwarding: External access to Web_Sv

Public IPs Available: 203.0.113.1 - 203.0.113.10
```

### Topology Details

#### Internal Network (Private)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | 192.168.1.1 | 255.255.255.0 | Internal gateway (Inside Interface) |
| **Switch** | - | - | - | Internal LAN switch |
| **PC1** | eth0/ens3 | 192.168.1.10 | 255.255.255.0 | Internal client (uses PAT) |
| **PC2** | eth0 | 192.168.1.20 | 255.255.255.0 | Internal client (uses PAT) |
| **Web_Sv** | eth0/ens3 | 192.168.1.100 | 255.255.255.0 | Internal web server (uses Static NAT) |

#### WAN/Internet Connection (Public)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/1 | 203.0.113.1 | 255.255.255.252 | WAN connection (Outside Interface) |
| **R2 (ISP)** | Gi0/0 | 203.0.113.2 | 255.255.255.252 | ISP edge router |
| **Internet** | - | 8.8.8.8 | - | Simulated Internet host |

#### NAT Pool & Mappings

| Service | Inside Local | Inside Global | Type |
|---------|--------------|---------------|------|
| **PC1, PC2** | 192.168.1.10-20 | 203.0.113.1 | PAT (Overload) |
| **Web_Sv** | 192.168.1.100 | 203.0.113.5 | Static NAT |
| **Web_Sv HTTP** | 192.168.1.100:80 | 203.0.113.1:8080 | Port Forwarding |

---

## 🔧 Creating the Lab

> **Purpose:** Set up the NAT topology in EVE-NG.

### Step 1: Create a New Lab

**What:** Create the lab project for NAT configuration.

**How to:**
1. Log into EVE-NG web interface
2. Click **Add Lab**
3. Enter lab details:
   - **Lab Name**: `NAT_Configuration_Lab`
   - **Lab Description**: `Network Address Translation and PAT`
   - **Lab Version**: `1.0`
4. Click **Create**

---

### Step 2: Add Router Nodes

**What:** Add edge router (R1) and ISP router (R2).

**How to:**
1. Click **Add Node**
2. Select **Cisco** → **IOSv** (router)
3. Add two routers:
   - **R1** (Edge router with NAT)
   - **R2** (ISP router - simulates Internet)
4. Click **Save**

---

### Step 3: Add Switch and PC Nodes

**What:** Add internal network devices.

**How to:**
1. Click **Add Node**
2. Add **Switch** (IOSvL2 or unmanaged switch)
3. Add **PC nodes**:
   - **PC1** (VPCS or Linux)
   - **PC2** (VPCS or Linux)
   - **Web_Sv** (Linux recommended for web server)
4. Click **Save**

---

### Step 4: Add Internet Simulation

**What:** Add a node to simulate Internet host.

**How to:**
1. Click **Add Node**
2. Option 1: Add another Linux VM as Internet host (8.8.8.8)
3. Option 2: Use loopback on R2 for testing
4. Click **Save**

---

### Step 5: Connect All Devices

**What:** Create network connections.

**Connection List:**

| From Device | Interface | To Device | Interface | Purpose |
|-------------|-----------|-----------|-----------|---------|
| **R1** | Gi0/0 | **Switch** | Gi0/0 | Internal LAN connection |
| **R1** | Gi0/1 | **R2** | Gi0/0 | WAN link to ISP |
| **Switch** | Gi0/1 | **PC1** | eth0/ens3 | Client 1 |
| **Switch** | Gi0/2 | **PC2** | eth0 | Client 2 |
| **Switch** | Gi0/3 | **Web_Sv** | eth0/ens3 | Web server |
| **R2** | Gi0/1 | **Internet** | - | Simulated Internet (optional) |

---

### Step 6: Start All Devices

**What:** Power on all routers, switches, and PCs.

**How to:**
1. Click **Start All**
2. Wait for devices to boot (1-2 minutes for routers)
3. Verify all devices show "Running" status

---

## ⚙️ Basic Router & PC Configuration

> **Purpose:** Configure IP addresses and routing before applying NAT.

### Step 1: R1 (Edge Router) Configuration

**What:** Configure internal and external interfaces (DO NOT enable NAT yet).

**R1 Configuration:**

```bash
enable
configure terminal

hostname R1

# Configure INSIDE interface (Internal LAN)
interface GigabitEthernet0/0
 description Internal LAN
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit

# Configure OUTSIDE interface (WAN to ISP)
interface GigabitEthernet0/1
 description WAN to ISP
 ip address 203.0.113.1 255.255.255.252
 no shutdown
 exit

# Configure default route to ISP
ip route 0.0.0.0 0.0.0.0 203.0.113.2

write memory
```

---

### Step 2: R2 (ISP Router) Configuration

**What:** Configure ISP router to simulate Internet connectivity.

**R2 Configuration:**

```bash
enable
configure terminal

hostname R2_ISP

# Configure WAN interface (connection to R1)
interface GigabitEthernet0/0
 description WAN to Customer R1
 ip address 203.0.113.2 255.255.255.252
 no shutdown
 exit

# Configure loopback to simulate Internet host (8.8.8.8)
interface Loopback0
 description Simulated Internet Host
 ip address 8.8.8.8 255.255.255.255
 no shutdown
 exit

# Static route to NAT pool (so R2 knows how to reach translated IPs)
ip route 203.0.113.0 255.255.255.240 203.0.113.1

write memory
```

---

### Step 3: PC IP Configuration

**What:** Configure IP addresses on internal PCs.

**PC1 Configuration:**

**Option 1: Using VPCS**
```bash
ip 192.168.1.10/24 192.168.1.1
save
```

**Option 2: Using Linux**
```bash
sudo ip addr add 192.168.1.10/24 dev ens3
sudo ip route add default via 192.168.1.1 dev ens3

# Make persistent (Ubuntu/Debian)
sudo nano /etc/netplan/00-installer-config.yaml
# Add:
# network:
#   ethernets:
#     ens3:
#       addresses:
#         - 192.168.1.10/24
#       routes:
#         - to: default
#           via: 192.168.1.1
#   version: 2

sudo netplan apply
```

**PC2 Configuration:**

**Using VPCS**
```bash
ip 192.168.1.20/24 192.168.1.1
save
```

**Web_Sv Configuration:**

**Using Linux (recommended for web server)**
```bash
sudo ip addr add 192.168.1.100/24 dev ens3
sudo ip route add default via 192.168.1.1 dev ens3

# Make persistent
sudo netplan apply

# Configure Debian sources.list (for Debian Stretch archive)
echo "deb https://archive.debian.org/debian/ stretch main contrib" | sudo tee /etc/apt/sources.list
echo "deb-src https://archive.debian.org/debian/ stretch main contrib" | sudo tee -a /etc/apt/sources.list

# Install web server
sudo apt update
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2

# Verify web server
curl http://localhost
```

---

### Step 4: Verify Basic Connectivity (Before NAT)

**What:** Test connectivity BEFORE enabling NAT.

**Test from PC1:**

```bash
# Ping default gateway
ping 192.168.1.1
# Expected: Success

# Ping ISP router
ping 203.0.113.2
# Expected: FAIL (no NAT yet, private IP not routable)

# Ping Internet host
ping 8.8.8.8
# Expected: FAIL (no NAT yet)
```

**Test from R1:**

```bash
# Ping ISP router
ping 203.0.113.2
# Expected: Success

# Ping Internet host
ping 8.8.8.8
# Expected: Success

# Verify routing table
show ip route
# Should see default route via 203.0.113.2
```

**Expected Results:**
- ✅ Internal LAN devices can ping R1 (192.168.1.1)
- ✅ R1 can ping ISP router and Internet
- ❌ Internal devices CANNOT ping Internet (no NAT yet)

> **⚠️ Important:** This is expected! NAT configuration will fix this.

---

## 🔒 Static NAT Configuration

> **Purpose:** Configure one-to-one permanent IP address mapping for Web_Sv.

### Scenario: Public Web Server

**Requirement:** Web_Sv (192.168.1.100) needs a permanent public IP (203.0.113.5) so Internet users can access it.

**R1 Configuration:**

```bash
enable
configure terminal

# Define inside and outside interfaces
interface GigabitEthernet0/0
 ip nat inside
 exit

interface GigabitEthernet0/1
 ip nat outside
 exit

# Configure Static NAT mapping
# Syntax: ip nat inside source static <inside-local> <inside-global>
ip nat inside source static 192.168.1.100 203.0.113.5

write memory
```

---

### Verification

**Show NAT translations:**

```bash
# On R1
show ip nat translations

# Expected output:
# Pro Inside global      Inside local       Outside local      Outside global
# --- 203.0.113.5        192.168.1.100      ---                ---
```

**Show NAT statistics:**

```bash
show ip nat statistics

# Expected output:
# Total active translations: 1 (1 static, 0 dynamic; 0 extended)
# Inside interfaces:
#   GigabitEthernet0/0
# Outside interfaces:
#   GigabitEthernet0/1
```

**Test from Internet (R2_ISP):**

```bash
# From R2_ISP console
ping 203.0.113.5

# Expected: Success (pings Web_Sv via Static NAT)
```

**Test from Web_Sv:**

```bash
# Ping Internet host
ping 8.8.8.8

# Expected: Success (return traffic works via Static NAT)
```

**On R1, check translations after traffic:**

```bash
show ip nat translations verbose

# Should show active translation with traffic counters
```

---

## 🔄 Dynamic NAT Configuration

> **Purpose:** Configure pool-based NAT for temporary translations.

### Scenario: Dynamic NAT Pool

**Requirement:** Provide 5 public IPs (203.0.113.6-10) for dynamic assignment to internal hosts.

**R1 Configuration:**

```bash
enable
configure terminal

# Define NAT pool
# Syntax: ip nat pool <name> <start-ip> <end-ip> netmask <mask>
ip nat pool PUBLIC_POOL 203.0.113.6 203.0.113.10 netmask 255.255.255.0

# Define which internal IPs can use NAT (using ACL)
# Allow 192.168.1.0/24 (except Web_Sv which uses Static NAT)
access-list 10 permit 192.168.1.0 0.0.0.255

# Link ACL to NAT pool
# Syntax: ip nat inside source list <acl> pool <pool-name>
ip nat inside source list 10 pool PUBLIC_POOL

write memory
```

---

### Verification

**Show NAT pool:**

```bash
show ip nat pool

# Expected output:
# Pool PUBLIC_POOL: netmask 255.255.255.0
#         start 203.0.113.6 end 203.0.113.10
#         type generic, total addresses 5, allocated 0 (0%), misses 0
```

**Test from PC2:**

```bash
# Generate traffic to Internet
ping 8.8.8.8

# Note: Might fail because pool IPs may not be routed by ISP yet
# For lab purposes, we'll use PAT instead (more realistic)
```

**Show active translations:**

```bash
# On R1
show ip nat translations

# Expected: See dynamic entries (with timeout)
# Pro Inside global      Inside local       Outside local      Outside global
# icmp 203.0.113.6:1     192.168.1.20:1     8.8.8.8:1          8.8.8.8:1
```

**Show NAT statistics:**

```bash
show ip nat statistics

# Check:
# - Dynamic mappings
# - Pool utilization
# - Miss counters (if pool exhausted)
```

> **💡 Note:** Dynamic NAT requires enough public IPs. If pool is exhausted, connections fail. This is why PAT is more common.

---

## 🌐 PAT Configuration (NAT Overload)

> **Purpose:** Configure many-to-one NAT with port translation (most common scenario).

### Scenario: Internet Access for All Internal Hosts

**Requirement:** All internal hosts (PC1, PC2, etc.) share ONE public IP (203.0.113.1) for Internet access.

**R1 Configuration:**

```bash
enable
configure terminal

# Remove previous dynamic NAT configuration (optional)
no ip nat inside source list 10 pool PUBLIC_POOL
no ip nat pool PUBLIC_POOL
no access-list 10

# Define which internal IPs can use PAT
access-list 1 permit 192.168.1.0 0.0.0.255

# Configure PAT using interface IP (most common method)
# Syntax: ip nat inside source list <acl> interface <interface> overload
ip nat inside source list 1 interface GigabitEthernet0/1 overload

# Alternative: PAT using specific public IP
# ip nat inside source list 1 interface GigabitEthernet0/1 overload

write memory
```

---

### Verification

**Test from PC1:**

```bash
# Ping Internet host
ping 8.8.8.8

# Expected: Success (via PAT)
```

**Test from PC2:**

```bash
# Ping Internet host
ping 8.8.8.8

# Expected: Success (via PAT, different port than PC1)
```

**Show NAT translations (while traffic is active):**

```bash
# On R1 (quickly after generating traffic)
show ip nat translations

# Expected output (example):
# Pro Inside global        Inside local         Outside local        Outside global
# icmp 203.0.113.1:1024    192.168.1.10:1024    8.8.8.8:1024         8.8.8.8:1024
# icmp 203.0.113.1:1025    192.168.1.20:1025    8.8.8.8:1025         8.8.8.8:1025
# --- 203.0.113.5          192.168.1.100        ---                  ---
```

**Show NAT statistics:**

```bash
show ip nat statistics

# Check:
# Total translations: X (1 static, X dynamic; extended)
# Hits: X  Misses: 0
# Peak translations: X
# Outside interfaces: GigabitEthernet0/1
# Inside interfaces: GigabitEthernet0/0
```

**Debug NAT (for troubleshooting):**

```bash
# Enable debug (use carefully - generates lots of output)
debug ip nat

# Generate traffic from PC1
# On R1 console, you'll see NAT translation messages

# Disable debug
undebug all
```

---

### PAT vs Dynamic NAT Comparison

| Feature | Dynamic NAT | PAT (Overload) |
|---------|-------------|----------------|
| **Public IPs needed** | One per connection | One for thousands |
| **Port translation** | No | Yes |
| **Configuration** | Needs IP pool | Uses interface IP |
| **Cost** | Expensive (many IPs) | Cheap (one IP) |
| **Use case** | Legacy systems | Modern networks |
| **Scalability** | Limited by pool size | ~65,000 ports per IP |

---

## 🔀 Port Forwarding

> **Purpose:** Allow external users to access internal server on specific port.

### Scenario: External Access to Internal Web Server

**Requirement:** Internet users connect to 203.0.113.1:8080 and reach Web_Sv (192.168.1.100:80).

**R1 Configuration:**

```bash
enable
configure terminal

# Configure Port Forwarding (Static NAT with port)
# Syntax: ip nat inside source static <protocol> <inside-local-ip> <inside-port> <inside-global-ip> <global-port>

# Forward TCP port 8080 to Web_Sv port 80
ip nat inside source static tcp 192.168.1.100 80 203.0.113.1 8080

# Forward TCP port 2222 to Web_Sv port 22 (SSH)
ip nat inside source static tcp 192.168.1.100 22 203.0.113.1 2222

write memory
```

---

### Verification

**Show NAT translations:**

```bash
show ip nat translations

# Expected:
# Pro Inside global        Inside local         Outside local        Outside global
# tcp 203.0.113.1:8080     192.168.1.100:80     ---                  ---
# tcp 203.0.113.1:2222     192.168.1.100:22     ---                  ---
# --- 203.0.113.5          192.168.1.100        ---                  ---
```

**Test from Internet (R2_ISP):**

```bash
# Test HTTP access (if telnet available)
telnet 203.0.113.1 8080

# Expected: Connection to Web_Sv:80
# GET / HTTP/1.0
# (press Enter twice)
# Should receive HTML response
```

**Test from PC with curl (if Linux):**

```bash
# From another network or R2_ISP
curl http://203.0.113.1:8080

# Expected: Web server response from 192.168.1.100
```

---

## ✅ Verification & Troubleshooting

> **Purpose:** Verify NAT is working and troubleshoot common issues.

### Verification Commands

**1. Show all NAT translations:**

```bash
show ip nat translations

# Shows:
# - Static NAT entries
# - Dynamic NAT entries
# - PAT entries with ports
# - Port forwarding entries
```

**2. Show NAT statistics:**

```bash
show ip nat statistics

# Shows:
# - Total active translations
# - Hits/misses
# - Inside/outside interfaces
# - Pool statistics (if using pools)
```

**3. Show detailed translation:**

```bash
show ip nat translations verbose

# Shows additional info:
# - Creation time
# - Last use time
# - Flags (extended, static, etc.)
```

**4. Clear NAT translations (for testing):**

```bash
# Clear dynamic NAT translations (not static)
clear ip nat translation *

# Clear specific translation
clear ip nat translation inside 192.168.1.10 outside 8.8.8.8
```

---

### Testing Scenarios

**Test 1: PAT Internet Access**

```bash
# From PC1
ping 8.8.8.8

# On R1, immediately check:
show ip nat translations

# Expected: See icmp translation with ports
```

**Test 2: Static NAT**

```bash
# From R2_ISP
ping 203.0.113.5

# Should reach Web_Sv (192.168.1.100)

# On R1:
show ip nat translations | include 203.0.113.5
```

**Test 3: Port Forwarding**

```bash
# From R2_ISP (if telnet available)
telnet 203.0.113.1 8080

# Should connect to Web_Sv:80

# On R1:
show ip nat translations | include 8080
```

---

### Troubleshooting Common Issues

#### Issue 1: NAT Not Working

**Symptoms:** Internal hosts cannot reach Internet even with NAT configured.

**Checklist:**
```bash
# 1. Verify inside/outside interfaces configured
show ip interface brief | include NAT
show run | section interface

# Look for:
# interface GigabitEthernet0/0
#  ip nat inside
# interface GigabitEthernet0/1
#  ip nat outside

# 2. Verify ACL permits traffic
show access-lists 1

# Expected: permit 192.168.1.0 0.0.0.255

# 3. Verify default route exists
show ip route

# Should see: S* 0.0.0.0/0 [1/0] via 203.0.113.2

# 4. Verify NAT configuration
show run | include ip nat

# 5. Check for NAT statistics
show ip nat statistics

# If hits = 0, NAT not processing traffic
```

---

#### Issue 2: NAT Pool Exhausted

**Symptoms:** Some hosts can access Internet, others cannot.

**Fix:**
```bash
# Check pool utilization
show ip nat pool

# Pool PUBLIC_POOL: netmask 255.255.255.0
#   start 203.0.113.6 end 203.0.113.10
#   type generic, total addresses 5, allocated 5 (100%), misses 10

# Solution 1: Increase pool size
ip nat pool PUBLIC_POOL 203.0.113.6 203.0.113.15 netmask 255.255.255.0

# Solution 2: Use PAT instead (recommended)
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

---

#### Issue 3: Port Forwarding Not Working

**Symptoms:** External users cannot access internal server via port forwarding.

**Checklist:**
```bash
# 1. Verify port forwarding entry exists
show ip nat translations | include <port>

# 2. Verify service is running on internal server
# On Web_Sv:
netstat -tuln | grep :80

# 3. Verify ACL not blocking traffic (if ACL applied)
show access-lists

# 4. Test from internal network first
# From PC1:
curl http://192.168.1.100:80

# 5. Check NAT statistics for denials
show ip nat statistics
```

---

#### Issue 4: NAT Translations Not Clearing

**Symptoms:** Old NAT entries remain even after traffic stops.

**Fix:**
```bash
# View translation timeout settings
show ip nat translations verbose

# Adjust timeout (default is 24 hours for TCP, 5 min for UDP/ICMP)
ip nat translation timeout 3600
ip nat translation tcp-timeout 7200
ip nat translation udp-timeout 300

# Manually clear translations
clear ip nat translation *
```

---

## 🌍 Real-World Scenarios

> **Purpose:** Apply NAT to realistic enterprise situations.

### Scenario 1: Small Office Internet Access

**Situation:** 50 employees need Internet access, only 1 public IP available.

**Solution: PAT (NAT Overload)**

```bash
# On Edge Router
interface GigabitEthernet0/0
 description Internal LAN
 ip address 10.10.10.1 255.255.255.0
 ip nat inside
 exit

interface GigabitEthernet0/1
 description WAN to ISP
 ip address <ISP-assigned-IP> <ISP-assigned-mask>
 ip nat outside
 exit

# Create ACL for internal network
access-list 1 permit 10.10.10.0 0.0.0.255

# Configure PAT
ip nat inside source list 1 interface GigabitEthernet0/1 overload

# Default route to ISP
ip route 0.0.0.0 0.0.0.0 <ISP-gateway>
```

**Result:**
- ✅ All 50 employees share 1 public IP
- ✅ Each connection uses unique port
- ✅ Cost-effective (no additional IPs needed)
- ✅ Scalable up to ~65,000 concurrent connections

---

### Scenario 2: Hosting Multiple Servers

**Situation:** Company has 1 public IP but wants to host:
- Web server (HTTP/HTTPS)
- Mail server (SMTP)
- FTP server

**Solution: Port Forwarding**

```bash
# Static NAT for Web Server
ip nat inside source static tcp 192.168.1.10 80 203.0.113.1 80
ip nat inside source static tcp 192.168.1.10 443 203.0.113.1 443

# Port forwarding for Mail Server
ip nat inside source static tcp 192.168.1.20 25 203.0.113.1 25
ip nat inside source static tcp 192.168.1.20 587 203.0.113.1 587

# Port forwarding for FTP Server
ip nat inside source static tcp 192.168.1.30 21 203.0.113.1 21
```

**Result:**
- ✅ External users access http://203.0.113.1 → Web Server
- ✅ Mail clients send to 203.0.113.1:25 → Mail Server
- ✅ FTP clients connect to 203.0.113.1:21 → FTP Server
- ✅ All services share 1 public IP

---

### Scenario 3: Branch Office with DMZ

**Situation:** Branch office needs:
- Internal users (PAT for Internet)
- DMZ web server (Static NAT)
- Secure separation

**Solution: Combination NAT**

```bash
# Inside interface (Internal LAN)
interface GigabitEthernet0/0
 ip address 10.1.1.1 255.255.255.0
 ip nat inside
 exit

# DMZ interface
interface GigabitEthernet0/2
 ip address 172.16.1.1 255.255.255.0
 ip nat inside
 exit

# Outside interface (WAN)
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.252
 ip nat outside
 exit

# PAT for internal users
access-list 10 permit 10.1.1.0 0.0.0.255
ip nat inside source list 10 interface GigabitEthernet0/1 overload

# Static NAT for DMZ web server
ip nat inside source static 172.16.1.10 203.0.113.5

# Port forwarding for remote admin
ip nat inside source static tcp 172.16.1.10 22 203.0.113.1 2222
```

**Result:**
- ✅ Internal users (10.1.1.0/24) use PAT
- ✅ DMZ server (172.16.1.10) has public IP via Static NAT
- ✅ SSH access via port 2222
- ✅ Security separation maintained

---

### Scenario 4: ISP Customer with Multiple Public IPs

**Situation:** ISP customer has /29 public subnet (8 IPs, 6 usable) and needs:
- Static NAT for 3 servers
- PAT for all other hosts

**Solution: Combination of Static NAT and PAT**

```bash
# Public subnet: 203.0.113.0/29
# Usable IPs: 203.0.113.1-6
# Gateway: 203.0.113.1

# Static NAT mappings
ip nat inside source static 192.168.1.10 203.0.113.2   # Web Server
ip nat inside source static 192.168.1.20 203.0.113.3   # Mail Server
ip nat inside source static 192.168.1.30 203.0.113.4   # DB Server

# PAT for remaining hosts
access-list 20 permit 192.168.1.0 0.0.0.255
access-list 20 deny 192.168.1.10
access-list 20 deny 192.168.1.20
access-list 20 deny 192.168.1.30
ip nat inside source list 20 interface GigabitEthernet0/1 overload
```

**Result:**
- ✅ 3 servers have dedicated public IPs
- ✅ All other hosts share remaining IP via PAT
- ✅ Efficient use of limited public IP space

---

## 📊 NAT Best Practices

### Design Guidelines

1. **✅ Use PAT for most scenarios**
   - Most cost-effective
   - Highly scalable
   - Industry standard

2. **✅ Use Static NAT for servers**
   - Permanent public IP
   - Easier to manage DNS
   - Required for inbound services

3. **✅ Reserve NAT pool for special cases**
   - Legacy applications that break with PAT
   - Applications requiring 1:1 mapping
   - Compliance requirements

4. **✅ Document NAT mappings**
   - Keep spreadsheet of translations
   - Label configurations clearly
   - Use remarks in config

---

### Security Considerations

```
NAT provides some security benefits, but is NOT a firewall:

✅ Security Benefits:
├─ Hides internal IP structure
├─ Prevents direct addressing from Internet
└─ Forces stateful connection tracking

❌ NOT Security Features:
├─ Does NOT block malicious traffic
├─ Does NOT inspect packet contents
├─ Does NOT prevent attacks on forwarded ports
└─ Should always be combined with ACLs/Firewall
```

**Example: NAT + ACL for Security**

```bash
# NAT configuration (connectivity)
ip nat inside source list 1 interface GigabitEthernet0/1 overload

# ACL for security (on outside interface)
ip access-list extended OUTSIDE_IN
 deny   ip any 192.168.0.0 0.0.255.255
 deny   ip any 10.0.0.0 0.255.255.255
 permit tcp any host 203.0.113.1 eq 8080
 permit icmp any any echo-reply
 deny   ip any any log

interface GigabitEthernet0/1
 ip access-group OUTSIDE_IN in
 exit
```

---

### Performance Considerations

**NAT Impact:**
- ✅ Minimal CPU impact on modern routers
- ⚠️ Translation table consumes memory
- ⚠️ High-volume sites may need dedicated NAT device

**Optimization:**
```bash
# Adjust translation timeouts to free memory faster
ip nat translation timeout 600         # 10 minutes (default 86400)
ip nat translation tcp-timeout 3600    # 1 hour (default 86400)
ip nat translation udp-timeout 300     # 5 minutes (default 300)

# Monitor NAT performance
show ip nat statistics
show memory

# For high-volume environments:
# - Use hardware with NAT acceleration
# - Consider NAT device (not just router)
# - Monitor translation table size
```

---

## 📝 Summary & Next Steps

### What You Learned

✅ **NAT Fundamentals**
- Private vs public IP addresses
- Inside local/global, outside local/global
- NAT types and use cases

✅ **Static NAT**
- One-to-one permanent mapping
- Configuration for servers
- Verification commands

✅ **Dynamic NAT**
- Pool-based temporary translation
- ACL-based matching
- Pool management

✅ **PAT (NAT Overload)**
- Many-to-one with port translation
- Most common NAT type
- Highly scalable solution

✅ **Port Forwarding**
- External access to internal services
- Multiple services on one IP
- Configuration and testing

✅ **Troubleshooting**
- Verification commands
- Common issues and fixes
- Debug techniques

✅ **Real-World Applications**
- Small office Internet access
- Hosting services
- Branch office design
- Enterprise scenarios

---

### Key Commands Reference

```bash
# Configure inside/outside interfaces
interface <interface>
 ip nat inside    # or
 ip nat outside

# Static NAT
ip nat inside source static <inside-local> <inside-global>

# Dynamic NAT
ip nat pool <name> <start-ip> <end-ip> netmask <mask>
access-list <#> permit <network> <wildcard>
ip nat inside source list <#> pool <name>

# PAT (NAT Overload)
ip nat inside source list <#> interface <interface> overload

# Port Forwarding
ip nat inside source static tcp <inside-ip> <inside-port> <global-ip> <global-port>

# Verification
show ip nat translations
show ip nat statistics
clear ip nat translation *
debug ip nat
```

---

### What's Next?

**Recommended progression:**

```
You completed: Lab 14 - NAT/PAT Configuration ✓

Next labs:
├─ Lab 15: DHCP Services (3-4h)
│  └─ Automate IP address assignment
│
├─ Lab 16: Firewall Concepts (4-5h)  
│  └─ Combine NAT with security policies
│
└─ TIER 1 Complete → Job Ready! 🎉
```

**Career Impact:**
- ✅ NAT is ESSENTIAL skill for any network role
- ✅ Used in 99% of enterprise networks
- ✅ Interview question staple
- ✅ Daily task for network engineers

---

## 🎓 Practice Exercises

**Exercise 1: Basic PAT Setup**
- Configure PAT for 192.168.10.0/24 network
- Test Internet connectivity from 3 PCs
- Verify each PC uses different port

**Exercise 2: Web Server Hosting**
- Configure Static NAT for internal web server
- Add port forwarding for SSH (port 2222)
- Test external access to both services

**Exercise 3: Multi-Zone NAT**
- Create 3 internal networks (LAN, DMZ, Guest)
- Use Static NAT for DMZ servers
- Use PAT for LAN and Guest
- Verify isolation and connectivity

**Exercise 4: Troubleshooting**
- Intentionally misconfigure NAT
- Use verification commands to identify issues
- Fix and verify resolution

---

**🎯 You now understand NAT/PAT comprehensively!**

**Next:** Choose your path—continue with more labs or start applying for network positions. NAT mastery is a highly marketable skill! 🚀

---

## 📞 Additional Resources

- [Cisco NAT Configuration Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_nat/configuration/xe-16/nat-xe-16-book.html)
- [RFC 1918 - Private Address Space](https://tools.ietf.org/html/rfc1918)
- [RFC 3022 - Traditional NAT](https://tools.ietf.org/html/rfc3022)

---

**Remember:** NAT is not a security feature, it's a routing/addressing feature. Always combine NAT with proper firewall rules and ACLs for security!
