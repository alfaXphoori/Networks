# 🖧 Lab 03: Basic Router (Interface & Static Route)

> Configure router interfaces and static routing for multi-network connectivity.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 1 - Foundation |
| **Level** | ⭐ Beginner |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure router interfaces with IP addresses
- ✅ Add static routes between networks
- ✅ Configure default route for Internet access
- ✅ Test connectivity across multiple networks

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch | Lab 02 |
| IP Subnetting | Networking Fundamentals |

---

## 🗺️ Lab Topology
```
[PC1]──[SW1]──[R1 Gi0/1]   [R1 Gi0/2]──[SW2]──[PC2]
              10.0.0.1/24   20.0.0.1/24
```

| Device | Interface | IP |
|--------|-----------|-----|
| R1 | Gi0/1 | 10.0.0.1/24 |
| R1 | Gi0/2 | 20.0.0.1/24 |
| PC1 | eth0 | 10.0.0.10/24 |
| PC2 | eth0 | 20.0.0.10/24 |

---

## 🛠️ Configuration

### R1 - Interface Configuration
```ios
enable
configure terminal
hostname R1

interface GigabitEthernet0/1
 ip address 10.0.0.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/2
 ip address 20.0.0.1 255.255.255.0
 no shutdown
exit
```

### PC1 & PC2
```bash
# PC1
ifconfig eth0 10.0.0.10 netmask 255.255.255.0 up
route add default gw 10.0.0.1

# PC2
ifconfig eth0 20.0.0.10 netmask 255.255.255.0 up
route add default gw 20.0.0.1
```

---

## ✅ Verification
```ios
show ip interface brief
show ip route
```

```bash
# From PC1
ping -c 4 20.0.0.10
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
Basic router interface and static route configuration for inter-network connectivity.
