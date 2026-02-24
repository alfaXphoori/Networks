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

> **Purpose:** Updated topology with Ftp_SV and correct device/interface names.

![NAT Lab Topology](imgs/diagram.png)

**Figure: NAT Lab Topology (with Ftp_SV, Web_SV, PC1, PC2, and correct interface mapping)**

Devices:
- **R1**: Edge router (Gi0/0 to Internet, Gi0/1 to R2)
- **R2**: ISP/Internet router (Gi0/0 to R1, Gi0/3 to SW)
- **SW**: Switch (Gi1/3 uplink, Gi0/0–Gi1/1 to end devices)
- **PC1, PC2**: Internal clients (e0)
- **Web_SV, Ftp_SV**: Internal servers (e0)

Refer to the diagram above for interface and device connections.

### Topology Details


#### Internal Network (Private)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/1 | 10.10.10.1 | 255.255.255.0 | Internal gateway (LAN to R2) |
| **R2** | Gi0/1 | 10.10.10.2 | 255.255.255.0 | Internal router (LAN from R1) |
| **Switch** | - | - | - | Internal LAN switch |
| **PC1** | eth0/ens3 | 10.10.10.10 | 255.255.255.0 | Internal client (uses PAT) |
| **PC2** | eth0 | 10.10.10.20 | 255.255.255.0 | Internal client (uses PAT) |
| **Web_Sv** | eth0/ens3 | 10.10.10.100 | 255.255.255.0 | Internal web server (uses Static NAT) |
| **Mail_Sv** | eth0/ens3 | 10.10.10.200 | 255.255.255.0 | Internal mail server (uses Static NAT) |

#### WAN/Internet Connection (Public)

| Device | Interface | IP Address | Subnet Mask | Role |
|--------|-----------|-----------|-------------|------|
| **R1** | Gi0/0 | DHCP (from ISP) | (from ISP) | WAN connection (Outside Interface) |
| **ISP/Internet** | - | - | - | Simulated Internet host |

#### NAT Pool & Mappings

| Service | Inside Local | Inside Global | Type |
|---------|--------------|---------------|------|
| **PC1, PC2** | 192.168.1.10-20 | 203.0.113.1 | PAT (Overload) |
| **Web_Sv** | 192.168.1.100 | 203.0.113.5 | Static NAT |
| **Mail_Sv** | 192.168.1.200 | 203.0.113.6 | Static NAT |
| **Web_Sv HTTP** | 192.168.1.100:80 | 203.0.113.1:8080 | Port Forwarding |
| **Mail_Sv SMTP** | 192.168.1.200:25 | 203.0.113.1:25 | Port Forwarding |
| **Mail_Sv IMAP** | 192.168.1.200:143 | 203.0.113.1:143 | Port Forwarding |

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
   - **Mail_Sv** (Linux recommended for mail server)
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
| **Switch** | Gi0/4 | **Mail_Sv** | eth0/ens3 | Mail server |
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

### Step 3: Switch (SW) Configuration

**What:** Configure VLANs, trunk uplink to R2, and access ports for each VLAN.

**Switch Configuration (IOSvL2):**

```bash
enable
configure terminal

hostname SW

vlan 20
 name Other
vlan 30
 name Member
vlan 101
 name WebServer
vlan 201
 name FtpServer


! ตั้ง trunk ไป R2
interface GigabitEthernet1/3
 description Trunk to R2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown

! Access port สำหรับแต่ละ VLAN
interface GigabitEthernet0/0
 description Access VLAN 20
 switchport mode access
 switchport access vlan 20
 no shutdown

interface GigabitEthernet0/1
 description Access VLAN 30
 switchport mode access
 switchport access vlan 30
 no shutdown

interface GigabitEthernet1/0
 description Access VLAN 101
 switchport mode access
 switchport access vlan 101
 no shutdown

interface GigabitEthernet1/1
 description Access VLAN 201
 switchport mode access
 switchport access vlan 201
 no shutdown

write memory
```

> **Purpose:** Configure IP addresses and routing before applying NAT.

### Step 1: R1 (Edge Router) Configuration

**What:** Configure internal and external interfaces (DO NOT enable NAT yet).

**R1 Configuration:**

```bash
enable
configure terminal

hostname R1

! ตั้งค่า interface ฝั่ง WAN (รับ DHCP จาก ISP)
interface GigabitEthernet0/0
 description WAN to ISP (DHCP)
 ip address dhcp
 ip nat outside
 no shutdown
 exit

! ตั้งค่า interface ฝั่ง LAN (ไป R2)
interface GigabitEthernet0/1
 description LAN to R2
 ip address 10.10.10.1 255.255.255.0
 ip address 20.0.0.1 255.255.255.0


## R1 Configuration (Edge Router & NAT)


R1 acts as the Edge Router connecting the internal network to the Internet (ISP) and serves as the NAT Gateway (PAT) so that internal IPs can access the Internet.

### Configuration Commands

Copy and paste the following commands into the R1 console:

```bash
enable
configure terminal
hostname R1

! 1. Configure Internet-facing Interface (WAN)
interface GigabitEthernet0/0
 description Connection to ISP
 ip address dhcp
 ip nat outside
 no shutdown
exit

! 2. Configure Internal-facing Interface (LAN)
interface GigabitEthernet0/1
 description Connection to R2
 ip address 10.10.10.1 255.255.255.0
 ip nat inside
 no shutdown
exit

! 3. Configure NAT Overload (PAT)
access-list 1 permit any
ip nat inside source list 1 interface GigabitEthernet0/0 overload

! 4. Configure OSPF and Default Route
router ospf 1
 network 10.10.10.0 0.0.0.255 area 0
 default-information originate
exit

end
write memory
```

![R1 Configuration Example](imgs/R1_config.png)
*Figure: R1 configuration in the EVE-NG console*

### Additional Recommendations After Configuration

- Check WAN IP: `show ip interface brief` (verify Gi0/0 received IP from DHCP)
- Check Routing Table: `show ip route`
- Check NAT status: `show ip nat translations` (should see entries when traffic passes)

---


## R2 Configuration (Core Router)


R2 acts as the Core Router (Transit) between R1 (Edge Router) and SW (Layer 3 Switch), using OSPF for internal routing.

### Configuration Commands

Copy and paste the following commands into the R2 console:

```bash
enable
configure terminal
hostname R2

! 1. Configure Interface to R1
interface GigabitEthernet0/0
 description Connection to R1
 ip address 10.10.10.2 255.255.255.0
 no shutdown
exit

! 2. Configure Interface to Switch (SW)
interface GigabitEthernet0/1
 description Connection to SW
 ip address 5.0.0.1 255.255.255.0
 no shutdown
exit

! 3. Configure OSPF Routing
! Advertise directly connected networks so other routers learn the routes
router ospf 1
 network 10.10.10.0 0.0.0.255 area 0
 network 5.0.0.0 0.0.0.255 area 0
exit

end
write memory
```

![R2 Configuration Example](imgs/R2_config.png)
*Figure: R2 configuration in the EVE-NG console*

### Additional Recommendations After Configuration

- Check OSPF Neighbors: `show ip ospf neighbor` (should see R1 in FULL state)
- Check Routing Table: `show ip route` (should see Default Route O*E2 from R1)
- Test connectivity: `ping 10.10.10.1` (R1's IP)

---


## SW Configuration (Layer 3 Switch)


SW acts as the Layer 3 Switch, providing gateway IPs for all endpoints (PC1, PC2, Web_SV, Ftp_SV) and connecting OSPF routing back to R2 for Internet access.

### Configuration Commands

Copy and paste the following commands into the SW console:

```bash
enable
configure terminal
hostname SW

! Enable routing on the switch (critical)
ip routing

! 1. Configure Uplink Interface to R2
interface GigabitEthernet1/3
 description Connection to R2
 no switchport
 ip address 5.0.0.2 255.255.255.0
 no shutdown
exit

! 2. Configure Interface for PC1 (Gateway for 50.0.0.0/24)
interface GigabitEthernet0/0
 description Gateway for PC1
 no switchport
 ip address 50.0.0.1 255.255.255.0
 no shutdown
exit

! 3. Configure Interface for PC2 (Gateway for 60.0.0.0/24)
interface GigabitEthernet0/1
 description Gateway for PC2
 no switchport
 ip address 60.0.0.1 255.255.255.0
 no shutdown
exit

! 4. Configure Interface for Web_SV (Gateway for 100.0.0.8/30)
! Subnet /30 has two usable IPs: .9 (gateway), .10 (host)
interface GigabitEthernet1/0
 description Gateway for Web_SV
 no switchport
 ip address 100.0.0.9 255.255.255.252
 no shutdown
exit

! 5. Configure Interface for Ftp_SV (Gateway for 200.0.0.8/30)
interface GigabitEthernet1/1
 description Gateway for Ftp_SV
 no switchport
 ip address 200.0.0.9 255.255.255.252
 no shutdown
exit

! 6. Configure OSPF Routing
! Advertise all directly connected networks so R1 and R2 learn routes to PCs/Servers
router ospf 1
 network 5.0.0.0 0.0.0.255 area 0
 network 50.0.0.0 0.0.0.255 area 0
 network 60.0.0.0 0.0.0.255 area 0
 network 100.0.0.8 0.0.0.3 area 0
 network 200.0.0.8 0.0.0.3 area 0
exit

end
write memory
```

![SW Configuration Example](imgs/SW_config.png)
*Figure: Layer 3 Switch configuration in the EVE-NG console*

### Additional Recommendations After Configuration

- Check port and IP status: `show ip interface brief` (all ports should have correct IP and be up/up)
- Check OSPF Neighbors: `show ip ospf neighbor` (should see R2 IP 5.0.0.1 in FULL state)
- Check Routing Table: `show ip route` (should see O*E2 or Default Route via 5.0.0.1 from R1)

---


## PC/Server (VPC/Linux) Configuration


**PC1:** ip 50.0.0.10/24 50.0.0.1

**PC2:** ip 60.0.0.10/24 60.0.0.1

**Web_SV:** ip 100.0.0.10/30 100.0.0.9

**Ftp_SV:** ip 200.0.0.10/30 200.0.0.9

### PC1 and PC2 (Linux Slax) Configuration

For Linux Slax nodes in EVE-NG, the default interface is usually eth0. Open a terminal and use the following commands to set IP, default gateway, and DNS server:

#### 🖥️ PC1 Configuration

Network Info: IP 50.0.0.10/24, Gateway 50.0.0.1

Type these commands one by one in PC1's terminal:

# 1. Set IP address and subnet mask on eth0 and bring it up
ifconfig eth0 50.0.0.10 netmask 255.255.255.0 up

# Or with ip command (modern Linux):
# ip addr add 50.0.0.10/24 dev eth0
# ip link set eth0 up

# 2. Set default gateway to the switch (SW)
route add default gw 50.0.0.1

# 3. Set DNS server (for domain name resolution)
echo "nameserver 8.8.8.8" > /etc/resolv.conf

![PC1 Configuration Example](imgs/PC1_Config.png)
*Figure: PC1 network configuration*


#### 🖥️ PC2 Configuration

Network Info: IP 60.0.0.10/24, Gateway 60.0.0.1

Type these commands one by one in PC2's terminal:

# 1. Set IP address and subnet mask on eth0 and bring it up
ifconfig eth0 60.0.0.10 netmask 255.255.255.0 up

# Or with ip command (modern Linux):
# ip addr add 60.0.0.10/24 dev eth0
# ip link set eth0 up

# 2. Set default gateway to the switch (SW)
route add default gw 60.0.0.1

# 3. Set DNS server
echo "nameserver 8.8.8.8" > /etc/resolv.conf

![PC2 Configuration Example](imgs/PC2_Config.png)
*Figure: PC2 network configuration*



![PC1 Test Example](imgs/PC1_Test_All.png)
*Figure: PC1 connectivity and verification test*

![PC2 Test Example](imgs/PC2_Test_All.png)
*Figure: PC2 connectivity and verification test*

After configuration, use these commands to verify correctness:

Check IP address:
ifconfig eth0 or ip a

Check routing table (gateway):
route -n or ip route

Test ping to gateway (switch):
ping -c 4 50.0.0.1 (for PC1)
ping -c 4 60.0.0.1 (for PC2)

Test ping to Internet:
ping -c 4 8.8.8.8
ping -c 4 google.com (if this works, DNS is working)

---


## Web_SV (Linux) Configuration


Web_SV acts as the internal web server, connected to the switch (SW) in a /30 subnet as follows:

IP Address: 100.0.0.10

Subnet Mask: 255.255.255.252 (/30)

Gateway: 100.0.0.9 (SW IP)

### 1. Network (IP & Gateway) Configuration

Open Web_SV's terminal and type these commands:

# 1. Set IP address and subnet mask on eth0
ifconfig eth0 100.0.0.10 netmask 255.255.255.252 up

# 2. Set default gateway to the switch (SW)
route add default gw 100.0.0.9

# 3. Set DNS server
echo "nameserver 8.8.8.8" > /etc/resolv.conf

![Web Server Example](imgs/Web_Server.png)
*Figure: Web_SV configuration and HTTP server test*


Test connectivity: Try `ping 100.0.0.9` (should reach SW) and `ping 8.8.8.8` (should reach Internet)

### 2. Simulate Web Server (HTTP)

In EVE-NG, you can run a web server on a Linux node in several ways. Choose one of the following:

**Method 1: Quick Python Web Server (recommended for lab)**

If Python is installed, run a quick web server:

# Create a temporary index.html
echo "<h1>Welcome to Web_SV (100.0.0.10)</h1>" > index.html

# Run web server on port 80 (keep terminal open)
python3 -m http.server 80
# Or for Python 2: python -m SimpleHTTPServer 80


**Method 2: Install Apache Web Server (standard)**

If you want a real web server and Web_SV can access the Internet:

# Update package list and install Apache2
apt-get update
apt-get install -y apache2

# Create a test web page
echo "<h1>Welcome to Web_SV on EVE-NG</h1>" > /var/www/html/index.html

# Start Apache2
/etc/init.d/apache2 start
# Or: systemctl start apache2


### 3. Test from Client (PC1 / PC2)

After running the web server, go to PC1 (50.0.0.10) or PC2 (60.0.0.10) and use curl or wget to fetch the page:

# Download web page from Web_SV
curl http://100.0.0.10

# Or with wget
wget -qO- http://100.0.0.10


If you see the <h1>Welcome to...</h1> message, routing between 50.0.0.0/24 and 100.0.0.8/30 via the switch and web server is working!

---


## Ftp_SV (Linux) Configuration


Ftp_SV acts as the internal FTP server, connected to the switch (SW) in a /30 subnet as follows:

IP Address: 200.0.0.10

Subnet Mask: 255.255.255.252 (/30)

Gateway: 200.0.0.9 (SW IP)

### 1. Network (IP & Gateway) Configuration

Open Ftp_SV's terminal and type these commands:

# 1. Set IP address and subnet mask on eth0
ifconfig eth0 200.0.0.10 netmask 255.255.255.252 up

# 2. Set default gateway to the switch (SW)
route add default gw 200.0.0.9

# 3. Set DNS server (for package installation)
echo "nameserver 8.8.8.8" > /etc/resolv.conf

![FTP Server Example](imgs/Ftp_Server.png)
*Figure: Ftp_SV configuration and FTP server test*


Test connectivity: Try `ping 200.0.0.9` (should reach SW) and `ping 8.8.8.8` (should reach Internet)

### 2. Install and Start FTP Server (vsftpd)

For Linux (Debian/Ubuntu/Slax with apt), install vsftpd to quickly simulate an FTP server:

# 1. Update package list and install vsftpd
apt-get update
apt-get install -y vsftpd

# 2. Create a user for FTP login
useradd -m ftpuser
passwd ftpuser 

# 3. Create a test file in ftpuser's home
echo "This is a test file from FTP Server." > /home/ftpuser/testfile.txt

# 4. Start FTP service
/etc/init.d/vsftpd start
# Or: service vsftpd start


### 3. Test from Client (PC1 / PC2)

After running the FTP server, go to PC1 (50.0.0.10) or PC2 (60.0.0.10) and test FTP connection and file download:

# Connect to FTP server
ftp 200.0.0.10


When prompted:

Name: type ftpuser
Password: type the password you set above

Once you see "Login successful", you are in.

Basic FTP commands:

ls : list files (should see testfile.txt)
get testfile.txt : download file to PC
put <filename> : upload file to server
quit or exit : leave FTP

If you can download the file to PC1 or PC2, routing from PC -> Switch -> FTP Server is working!

---


## NAT/PAT Verification & Testing on R1


In EVE-NG, after configuring NAT (PAT/Overload for outbound Internet, Static NAT/Port Forwarding for inbound Web/FTP), use these commands on R1 to verify correct operation:

### 1. Show NAT Translations Table (most important)

This shows which inside local IPs are being translated to which inside global IPs and ports:

show ip nat translations

![PAT Verification Example](imgs/R1_pat.png)
*Figure: PAT/NAT translation table on R1*


Example of correct output:

Pro Inside global         Inside local          Outside local         Outside global
tcp 192.168.80.136:80     100.0.0.10:80         ---                   ---
tcp 192.168.80.136:21     200.0.0.10:21         ---                   ---
 no shutdown


Explanation:

First two lines are Static NAT (Port Forwarding) for Web (80) and FTP (21)

Last line is PAT (Overload) from PC1 (50.0.0.10) pinging 8.8.8.8, translated to R1's WAN IP (192.168.80.136)

### 2. Show NAT Statistics

This shows overall NAT operation, inside/outside interfaces, and hit/miss counts:

show ip nat statistics


Key points to check:

- Outside interfaces: should be GigabitEthernet0/0
- Inside interfaces: should be GigabitEthernet0/1
- Hits: should increase as traffic passes (means NAT is working)
- Misses: should be 0 or very low (high means traffic is not matching any rule)

### 3. Clear NAT Translations Table

If you change NAT config or want to clear old sessions (e.g., stuck Telnet/FTP), use:

clear ip nat translation *


(Note: This does not remove Static NAT config, only active sessions)

### 4. Debug NAT in Real-time

Warning: Debug will print lots of messages to the console. Use only for step-by-step testing.

! Enable NAT debug
debug ip nat

! Test: have PC1 ping 8.8.8.8 or access web from outside
! Console will show messages like:
! NAT*: s=50.0.0.10->192.168.80.136, d=8.8.8.8 [123]

! Disable debug (important after testing)
undebug all
! Or: u all



![Lab Topology Diagram](imgs/diagram.png)
*Figure: Overall lab topology for NAT/PAT testing*

To ensure NAT works both ways, follow these steps:

**Outbound Test (PAT/Overload):**

On PC1 or PC2, run: ping 8.8.8.8

On R1, run: show ip nat translations (should see icmp protocol with translated IP)

**Inbound Test (Static NAT/Port Forwarding):**

On your host PC (Windows), run: telnet 192.168.80.136 80 (or use a browser)

On R1, run: show ip nat translations (should see a line showing port 80 mapped to 100.0.0.10)

Repeat for port 21 (FTP)

interface GigabitEthernet0/3.30
 encapsulation dot1Q 30
 ip address 30.0.0.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/3.101
 encapsulation dot1Q 101
 ip address 101.0.0.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/3.201
 encapsulation dot1Q 201
 ip address 201.0.0.1 255.255.255.252
 no shutdown

! ตั้งค่า default route ให้ออกเน็ตผ่าน R1
ip route 0.0.0.0 0.0.0.0 10.10.10.1

write memory
```

---

### Step 3: PC IP Configuration

**What:** Configure IP addresses on internal PCs.

**PC1 Configuration:**

**Option 1: Using VPCS**
```bash
ip 20.0.0.10/24 20.0.0.1
save
```

**Option 2: Using Linux**
```bash
sudo ip addr add 20.0.0.10/24 dev ens3
# หรือใช้คำสั่งนี้ (legacy):
sudo route add default gw 20.0.0.1 ens3

# Make persistent (Ubuntu/Debian)
sudo nano /etc/netplan/00-installer-config.yaml
# Add:
# network:
#   ethernets:
#     ens3:
#       addresses:
#         - 20.0.0.10/24
#       routes:
#         - to: default
#           via: 20.0.0.1
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

**Mail_Sv Configuration:**

**Using Linux (recommended for mail server)**
```bash
sudo ip addr add 192.168.1.200/24 dev ens3
sudo ip route add default via 192.168.1.1 dev ens3

# Make persistent
sudo nano /etc/netplan/00-installer-config.yaml
# Add:
# network:
#   ethernets:
#     ens3:
#       addresses:
#         - 192.168.1.200/24
#       routes:
#         - to: default
#           via: 192.168.1.1
#   version: 2

sudo netplan apply

# Configure Debian sources.list (for Debian Stretch archive)
echo "deb https://archive.debian.org/debian/ stretch main contrib" | sudo tee /etc/apt/sources.list
echo "deb-src https://archive.debian.org/debian/ stretch main contrib" | sudo tee -a /etc/apt/sources.list

# Install mail server (Postfix)
sudo apt update
sudo apt install postfix -y
# Select "Internet Site" during installation
# System mail name: example.com

# Install Dovecot (IMAP server)
sudo apt install dovecot-imapd -y

# Start services
sudo systemctl start postfix
sudo systemctl enable postfix
sudo systemctl start dovecot
sudo systemctl enable dovecot

# Verify mail services
sudo netstat -tuln | grep :25    # SMTP
sudo netstat -tuln | grep :143   # IMAP
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

> **Purpose:** Configure one-to-one permanent IP address mapping for servers.

### Scenario 1: Public Web Server

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

# Configure Static NAT mapping for Web Server
# Syntax: ip nat inside source static <inside-local> <inside-global>
ip nat inside source static 192.168.1.100 203.0.113.5

# Configure Static NAT mapping for Mail Server
ip nat inside source static 192.168.1.200 203.0.113.6

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
# --- 203.0.113.6        192.168.1.200      ---                ---
```

**Show NAT statistics:**

```bash
show ip nat statistics

# Expected output:
# Total active translations: 2 (2 static, 0 dynamic; 0 extended)
# Inside interfaces:
#   GigabitEthernet0/0
# Outside interfaces:
#   GigabitEthernet0/1
```

**Test from Internet (R2_ISP):**

```bash
# From R2_ISP console
# Test Web Server
ping 203.0.113.5
# Expected: Success (pings Web_Sv via Static NAT)

# Test Mail Server
ping 203.0.113.6
# Expected: Success (pings Mail_Sv via Static NAT)
```

**Test from Web_Sv:**

```bash
# Ping Internet host
ping 8.8.8.8

# Expected: Success (return traffic works via Static NAT)
```

**Test from Mail_Sv:**

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

> **Purpose:** Allow external users to access internal servers on specific ports.

### Scenario 1: External Access to Internal Web Server

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

### Scenario 2: External Access to Internal Mail Server

**Requirement:** Internet users can send/receive email via Mail_Sv using standard ports.

**R1 Configuration:**

```bash
enable
configure terminal

# Forward SMTP port 25 to Mail_Sv (incoming mail)
ip nat inside source static tcp 192.168.1.200 25 203.0.113.1 25

# Forward SMTP Submission port 587 to Mail_Sv (outgoing mail with auth)
ip nat inside source static tcp 192.168.1.200 587 203.0.113.1 587

# Forward IMAP port 143 to Mail_Sv (mail retrieval)
ip nat inside source static tcp 192.168.1.200 143 203.0.113.1 143

# Forward IMAPS port 993 to Mail_Sv (secure IMAP)
ip nat inside source static tcp 192.168.1.200 993 203.0.113.1 993

# Forward SSH port 2223 to Mail_Sv (remote admin)
ip nat inside source static tcp 192.168.1.200 22 203.0.113.1 2223

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
# tcp 203.0.113.1:25       192.168.1.200:25     ---                  ---
# tcp 203.0.113.1:587      192.168.1.200:587    ---                  ---
# tcp 203.0.113.1:143      192.168.1.200:143    ---                  ---
# tcp 203.0.113.1:993      192.168.1.200:993    ---                  ---
# tcp 203.0.113.1:2223     192.168.1.200:22     ---                  ---
# --- 203.0.113.5          192.168.1.100        ---                  ---
# --- 203.0.113.6          192.168.1.200        ---                  ---
```

**Test Web Server from Internet (R2_ISP):**

```bash
# Test HTTP access (if telnet available)
telnet 203.0.113.1 8080

# Expected: Connection to Web_Sv:80
# GET / HTTP/1.0
# (press Enter twice)
# Should receive HTML response
```

**Test Mail Server from Internet (R2_ISP):**

```bash
# Test SMTP (if telnet available)
telnet 203.0.113.1 25

# Expected: Connection to Mail_Sv:25
# 220 example.com ESMTP Postfix
# (SMTP server greeting)

# Test IMAP
telnet 203.0.113.1 143

# Expected: Connection to Mail_Sv:143
# * OK [CAPABILITY...] Dovecot ready
```

**Test from PC with curl (if Linux):**

```bash
# Test web server
curl http://203.0.113.1:8080

# Expected: Web server response from 192.168.1.100

# Test SMTP
telnet 203.0.113.1 25
# Expected: SMTP greeting from 192.168.1.200
```

---

## ✅ Verification & Troubleshooting

> **Purpose:** Verify NAT is working and troubleshoot common issues.

### Verification Commands (R1)

**1. ตรวจสอบ IP ที่ได้รับจาก DHCP (WAN):**
```bash
show ip interface brief
show dhcp lease
```

**2. ตรวจสอบ routing table (default route จาก DHCP):**
```bash
show ip route
```

**3. ตรวจสอบ NAT translations (PAT):**
```bash
show ip nat translations
show ip nat statistics
```

**4. ตรวจสอบ access-list ที่ใช้กับ NAT:**
```bash
show access-lists
```

**5. ตรวจสอบสถานะ interface:**
```bash
show interfaces status
```

**6. ตรวจสอบ log ล่าสุด (ดู DHCP, NAT, interface):**
```bash
show logging | include DHCP|NAT|Gigabit
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
- Mail server (SMTP/IMAP)

**Solution: Combination of Static NAT and Port Forwarding**

```bash
# Static NAT for direct server access (optional - uses more IPs)
ip nat inside source static 192.168.1.100 203.0.113.5   # Web Server
ip nat inside source static 192.168.1.200 203.0.113.6   # Mail Server

# OR use Port Forwarding to share 1 public IP:

# Port forwarding for Web Server
ip nat inside source static tcp 192.168.1.100 80 203.0.113.1 80
ip nat inside source static tcp 192.168.1.100 443 203.0.113.1 443

# Port forwarding for Mail Server
ip nat inside source static tcp 192.168.1.200 25 203.0.113.1 25
ip nat inside source static tcp 192.168.1.200 587 203.0.113.1 587
ip nat inside source static tcp 192.168.1.200 143 203.0.113.1 143
ip nat inside source static tcp 192.168.1.200 993 203.0.113.1 993
```

**Result:**
- ✅ External users access http://203.0.113.1 → Web Server (or http://203.0.113.5)
- ✅ Mail clients send to 203.0.113.1:25 → Mail Server (or 203.0.113.6:25)
- ✅ IMAP access to 203.0.113.1:143 → Mail Server
- ✅ Flexible deployment: Static NAT OR Port Forwarding
- ✅ Can combine both approaches

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
