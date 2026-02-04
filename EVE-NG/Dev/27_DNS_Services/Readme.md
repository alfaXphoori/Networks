# 🌐 DNS Configuration Lab

> Complete hands-on lab covering DNS server configuration, DNS relay/forwarding, static DNS entries, recursive queries, and DNS troubleshooting in enterprise networks.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Lab Objectives](#lab-objectives)
2. [Prerequisites](#prerequisites)
3. [Lab Topology](#lab-topology)
4. [Creating the Lab](#creating-the-lab)
5. [DNS Fundamentals](#dns-fundamentals)
6. [Router DNS Configuration](#router-dns-configuration)
7. [Static DNS Entries](#static-dns-entries)
8. [DNS Relay/Forwarding](#dns-relayforwarding)
9. [PC DNS Configuration](#pc-dns-configuration)
10. [Testing DNS](#testing-dns)
11. [Troubleshooting](#troubleshooting)
12. [Summary & Next Steps](#summary--next-steps)

---

## 🎯 Lab Objectives

> **Purpose:** Master DNS configuration for domain name resolution in enterprise networks.

### By the end of this lab, you will:

- ✅ Configure DNS on routers
- ✅ Create static DNS entries
- ✅ Configure DNS forwarding to external servers
- ✅ Understand DNS query types (recursive, iterative)
- ✅ Configure DNS relay on router interfaces
- ✅ Verify DNS resolution from clients
- ✅ Troubleshoot DNS issues systematically
- ✅ Integrate DNS with DHCP for automatic setup
- ✅ Understand DNS security considerations

---

## ✅ Prerequisites

> **Purpose:** Ensure foundational knowledge for DNS configuration.

### Required Knowledge

| Topic | Why It Matters | Reference |
|-------|---------------|---------  |
| **IP Addressing** | DNS servers have IP addresses | 04_Basic Switch Lab |
| **Router Interfaces** | DNS runs on router interfaces | 03_Switch Config Lab |
| **DHCP** | DNS pushed via DHCP | 15_DHCP_Services Lab |
| **Network Basics** | DNS uses UDP port 53 | 08_Basic_Routing Lab |

### Required Resources

- ✅ EVE-NG with IOSv routers
- ✅ VPCS for client testing
- ✅ Understanding of domain names
- ✅ Understanding of IP addresses vs domain names

---

## 📊 Lab Topology

> **Purpose:** DNS topology with multiple domains and query paths.

```
INTERNAL NETWORK                    DNS RESOLUTION PATH

┌─────────────────────┐
│ Internal Domain     │             External
│ company.local       │             DNS Server
│                     │             8.8.8.8 (Google)
│ R1 (Router)         │                 │
│ IP: 10.0.0.1        │                 │
│ DNS Server: YES     │                 │
│                     │    Forwarding   │
│ PC1                 │◄──────────────► │
│ 10.0.0.10           │                 │
│ DNS: 10.0.0.1       │                 │
│                     │
│ PC2                 │
│ 10.0.0.11           │
│ DNS: 10.0.0.1       │
└─────────────────────┘

Query Flow:
1. PC1 queries "www.example.com" to 10.0.0.1 (R1)
2. R1 doesn't have it locally, forwards to 8.8.8.8
3. 8.8.8.8 responds with IP address
4. R1 returns answer to PC1
5. PC1 connects to www.example.com
```

### Network Configuration

| Device | Interface | IP Address | Role |
|--------|-----------|-----------|------|
| **R1** | Gi0/0 | 10.0.0.1 | DNS Server + Router |
| **R1** | Gi0/1 | 8.8.8.8.0.1 | External (toward ISP) |
| **PC1** | - | 10.0.0.10 | DNS Client |
| **PC2** | - | 10.0.0.11 | DNS Client |

---

## 🔧 Creating the Lab

> **Purpose:** Set up DNS topology.

### Step 1: Create Lab

**What:** Create EVE-NG lab for DNS configuration.

**How to:**
1. Log into EVE-NG
2. Click **Add Lab**
3. Configure:
   - **Lab Name**: `DNS_Lab`
   - **Description**: `DNS Configuration and Resolution`
4. Click **Create**

---

### Step 2: Add Devices

**What:** Add routers, switch, and PCs.

**Devices:**
- **2 Routers** (R1 - DNS server, R2 - client gateway)
- **1 Switch** (SW1)
- **2 PCs** (PC1, PC2 - DNS clients)

---

### Step 3: Connect Devices

**Connections:**
- R1 Gi0/0 ↔ R2 Gi0/1 (backbone)
- R2 Gi0/0 ↔ SW1 Gi0/0 (switch connection)
- SW1 Gi1/1 ↔ PC1 (Client 1)
- SW1 Gi1/2 ↔ PC2 (Client 2)
- R1 Gi0/1 ↔ (Simulated external link)

---

### Step 4: Start Lab

**What:** Boot all devices.

---

## 📚 DNS Fundamentals

> **Purpose:** Understand DNS before configuration.

### DNS Key Concepts

| Concept | Explanation |
|---------|-------------|
| **DNS** | Translates domain names (www.google.com) to IP addresses (142.251.32.46) |
| **Recursive Query** | Client asks DNS server; server finds answer (or tries harder) |
| **Iterative Query** | DNS server asks another DNS server for partial answer |
| **Authoritative Server** | Owns/manages specific domain records |
| **Recursive Resolver** | Performs full lookup on behalf of client |
| **Forwarder** | Forwards queries to upstream DNS servers |
| **Cache** | Stores previous answers for quick responses |
| **TTL** | Time To Live - how long to cache a response |

### DNS Query Process

```
Step 1: PC asks R1
        "What is the IP for www.google.com?"
        
Step 2: R1 checks local cache
        Cache MISS - "I don't have this"
        
Step 3: R1 forwards query upstream
        "Google DNS, what is IP for www.google.com?"
        
Step 4: Google DNS responds
        "www.google.com = 142.251.32.46"
        
Step 5: R1 caches answer
        "Remember this for next 3600 seconds (TTL)"
        
Step 6: R1 responds to PC
        "www.google.com = 142.251.32.46"
        
Step 7: PC connects to 142.251.32.46
        ✓ Connection successful!
```

---

## ⚙️ Router DNS Configuration

> **Purpose:** Configure router as DNS server.

### Step 5: Configure R1 as DNS Server

**What:** Enable DNS service on R1.

**Commands:**
```bash
enable
configure terminal
hostname R1

! Enable DNS service on router
ip dns server

! Configure router interface IP
interface gigabitEthernet 0/0
description Internal Interface
ip address 10.0.0.1 255.255.255.0
no shutdown
exit

! Configure external interface
interface gigabitEthernet 0/1
description External Interface
ip address 203.0.113.1 255.255.255.0
no shutdown
exit

end
```

**What `ip dns server` Does:**
- Enables DNS server on router
- Listens on port 53 (UDP and TCP)
- Responds to DNS queries from clients
- Can be combined with forwarding

---

### Step 6: Configure DNS Forwarding

**What:** Forward unknown queries to upstream DNS servers.

**Commands:**
```bash
enable
configure terminal

! Set upstream DNS servers (Google DNS)
ip name-server 8.8.8.8
ip name-server 8.8.4.4

! Enable DNS forwarding
ip dns forwarding

end
```

**Configuration Explanation:**

| Command | Purpose |
|---------|---------|
| **ip name-server 8.8.8.8** | Primary upstream DNS |
| **ip name-server 8.8.4.4** | Secondary upstream DNS |
| **ip dns forwarding** | Forward unknown queries |

> **ℹ️ Query Path:** Unknown query → 8.8.8.8 → 8.8.4.4 (if first fails)

---

### Step 7: Verify DNS Server Status

**What:** Confirm DNS is enabled and configured.

**Commands:**
```bash
show ip dns servers
show running-config | include dns
```

**Expected Output:**
```
Default Domain Name: (not set)
Domain Name Servers:
    8.8.8.8
    8.8.4.4

DNS server is enabled.
DNS forwarding is enabled.
DNS static mappings are being used.
```

---

## 📝 Static DNS Entries

> **Purpose:** Create local DNS records for internal hosts.

### Step 8: Configure Static DNS Entries

**What:** Create local DNS records for company domain.

**Commands on R1:**
```bash
enable
configure terminal

! Create local domain for company
ip domain-name company.local

! Add static DNS entries
ip host web1.company.local 10.0.0.100
ip host mail.company.local 10.0.0.101
ip host ftp.company.local 10.0.0.102
ip host dns.company.local 10.0.0.1

! Create shorthand names
ip host router 10.0.0.1

end
```

**Static Entries Breakdown:**

| Entry | Maps To | Purpose |
|-------|---------|---------|
| **web1.company.local** | 10.0.0.100 | Web server |
| **mail.company.local** | 10.0.0.101 | Mail server |
| **ftp.company.local** | 10.0.0.102 | FTP server |
| **dns.company.local** | 10.0.0.1 | DNS server itself |

---

### Step 9: Verify Static Entries

**What:** Confirm DNS records are configured.

**Commands:**
```bash
show hosts
show ip host
```

**Expected Output:**
```
Default Domain is company.local

Host                      Port  Protocol Address
dns.company.local         None  IP      10.0.0.1
web1.company.local        None  IP      10.0.0.100
mail.company.local        None  IP      10.0.0.101
ftp.company.local         None  IP      10.0.0.102
router                    None  IP      10.0.0.1
```

---

## 🔀 DNS Relay/Forwarding

> **Purpose:** Advanced DNS query handling.

### Step 10: Configure DNS Cache

**What:** Optimize DNS performance with caching.

**Commands:**
```bash
enable
configure terminal

! Enable DNS caching
ip dns cache enable

! Set cache parameters
ip dns cache size 200
ip dns cache ttl 3600

end
```

**Cache Configuration:**

| Parameter | Meaning | Value |
|-----------|---------|-------|
| **cache enable** | Enable caching | Yes |
| **cache size** | Max entries | 200 |
| **cache ttl** | Default expiry | 3600 sec (1 hr) |

---

### Step 11: Configure DNS Resolution Timeout

**What:** Set query timeout for reliability.

**Commands:**
```bash
enable
configure terminal

! DNS lookup timeout
ip dns timeout 3

! DNS query retries
ip dns retries 2

end
```

> **ℹ️ Behavior:** Wait 3 seconds, retry 2 times before giving up

---

## 🖥️ PC DNS Configuration

> **Purpose:** Configure PCs to use router as DNS server.

### Step 12: Configure PC1 DNS Settings

**What:** Set R1 as DNS server for PC1.

**Commands on PC1:**
```bash
# Set static DNS server
ip 10.0.0.10 10.0.0.1 24

# Set DNS server IP manually
set dns 10.0.0.1

# Or use DHCP (if DHCP configured with DNS)
dhcp

# Verify
show ip
```

**Expected Output:**
```
NAME        : PC1
IP/MASK     : 10.0.0.10/24
GATEWAY     : 10.0.0.1
DNS         : 10.0.0.1
DHCP SERVER : 10.0.0.1 (if using DHCP)
```

---

### Step 13: Configure PC2 DNS Settings

**What:** Set R1 as DNS server for PC2.

**Commands on PC2:**
```bash
ip 10.0.0.11 10.0.0.1 24
set dns 10.0.0.1
```

> **✅ Checkpoint:** All PCs configured to use R1 for DNS!

---

## 🔍 Testing DNS

> **Purpose:** Verify DNS resolution works.

### Step 14: Test Local DNS Resolution

**What:** Resolve internal domain names.

**Commands on PC1:**
```bash
# Query local domain
nslookup web1.company.local

# Or simple DNS lookup
dns web1.company.local
```

**Expected Output:**
```
web1.company.local resolves to 10.0.0.100
```

---

### Step 15: Test External DNS Resolution

**What:** Resolve external domain names through forwarding.

**Commands on PC1:**
```bash
# Query external domain
nslookup www.google.com

# Or
dns www.google.com
```

**Expected Output:**
```
www.google.com resolves to 142.251.32.46
(or similar Google IP)
```

---

### Step 16: Test DNS Caching

**What:** Verify repeated queries are cached.

**Commands on PC1:**
```bash
# First query (from external DNS)
nslookup www.google.com
# Check response time: ~100ms

# Second query (from cache)
nslookup www.google.com
# Check response time: <5ms (much faster!)
```

> **ℹ️ Caching:** Second query much faster = cache working!

---

## ✔️ Verification

> **Purpose:** Monitor DNS operations.

### Step 17: Check DNS Cache

**What:** View cached DNS entries.

**Commands on R1:**
```bash
show ip dns cache
```

**Expected Output:**
```
Cache Statistics:
  Entries: 5
  Size: 1024 bytes
  
Cached Entries:
  www.google.com (TTL: 3456) = 142.251.32.46
  web1.company.local (TTL: 86400) = 10.0.0.100
  ...
```

---

### Step 18: Monitor DNS Queries

**What:** See DNS query statistics.

**Commands:**
```bash
show ip dns statistics

debug ip dns
# Shows real-time DNS operations
```

**Sample Statistics:**
```
DNS Statistics:
  Queries received: 24
  Queries resolved locally: 12
  Queries forwarded: 12
  Responses sent: 24
  Failed lookups: 0
```

---

### Step 19: Test DNS from Router

**What:** Use router's DNS lookup capability.

**Commands on R1:**
```bash
# Lookup command
nslookup www.google.com

# Or ping by name
ping www.google.com
# Router resolves name automatically
```

---

## 🆘 Troubleshooting

| 🔴 Issue | 🔧 Diagnosis | ✅ Solution |
|---------|-----------|-----------|
| **PC can't resolve names** | DNS server not set | Verify `set dns X.X.X.X` on PC |
| **DNS server not responding** | Service not enabled | Check `show ip dns servers` |
| **External queries fail** | Forwarding not configured | Add `ip name-server` and `ip dns forwarding` |
| **Slow DNS responses** | Cache not working | Enable cache with `ip dns cache enable` |
| **Wrong IP returned** | Static entry misconfigured | Verify with `show ip host` |
| **Domain suffix issues** | Domain not set | Configure `ip domain-name` |

### Debugging Commands

```bash
# Enable DNS debugging
debug ip dns all

# Show DNS configuration
show running-config | include dns

# Check DNS servers configured
show ip name servers

# View DNS statistics
show ip dns statistics

# Clear DNS cache
clear ip dns cache

# Turn off debugging
undebug all
```

---

## ✅ Summary & Next Steps

### Lab Completion

**Congratulations!** You have successfully:

- ✅ Configured router as DNS server
- ✅ Created static DNS entries for internal domains
- ✅ Configured DNS forwarding for external queries
- ✅ Enabled DNS caching for performance
- ✅ Configured clients to use DNS
- ✅ Verified all DNS resolution types
- ✅ Monitored DNS cache and statistics
- ✅ Troubleshot DNS issues

### Key Concepts

| Concept | Purpose | Command |
|---------|---------|---------|
| **DNS Server** | Responds to queries | `ip dns server` |
| **DNS Forwarding** | Forward unknown queries | `ip dns forwarding` |
| **Name Servers** | Upstream DNS targets | `ip name-server` |
| **Static Hosts** | Local DNS entries | `ip host` |
| **DNS Cache** | Fast response from memory | `ip dns cache enable` |
| **Domain Name** | Default domain suffix | `ip domain-name` |

### DNS Quick Reference

```bash
# Enable DNS server
ip dns server
ip dns forwarding

# Set upstream DNS
ip name-server 8.8.8.8
ip name-server 8.8.4.4

# Create static entries
ip host web1.company.local 10.0.0.100
ip domain-name company.local

# Enable caching
ip dns cache enable
ip dns cache size 200
ip dns cache ttl 3600

# Verify
show ip dns servers
show ip host
show ip dns cache
```

### What's Next?

**Integration Labs:**
1. 📡 **DHCP + DNS Integration** - Push DNS via DHCP
2. 🔄 **DHCP + DNS + NAT** - Full enterprise stack
3. 🔐 **DNS Security** - DNSSEC and query filtering
4. 📊 **DNS Analytics** - Monitoring and troubleshooting

---

✅ **DNS lab complete! You now understand domain name resolution!** 🎓

**Ready for the integrated DHCP+DNS+NAT lab?** 🚀
