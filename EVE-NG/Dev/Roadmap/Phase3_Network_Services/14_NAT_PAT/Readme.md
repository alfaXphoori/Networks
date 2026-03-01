# 🖧 Lab 14: NAT & PAT Configuration

> Configure Static NAT, Dynamic NAT, and PAT (overload) on a Cisco router for inside/outside address translation.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 3 - Network Services |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure Static NAT for a server with fixed public IP mapping
- ✅ Configure Dynamic NAT with an IP pool for multiple hosts
- ✅ Configure PAT (NAT overload) to share a single public IP
- ✅ Define NAT inside and outside interfaces correctly
- ✅ Verify translations with show and debug commands

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| Static Routing | Lab 07 - Static Routing |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [PC1 192.168.1.10]---+
  [PC2 192.168.1.11]---+---[Router]---[Internet 203.0.113.0/30]---[ISP]
  [SRV 192.168.1.100]--+
  
  Inside: 192.168.1.0/24
  Outside: 203.0.113.1/30
  NAT Pool: 203.0.113.10-20
  Static NAT: 192.168.1.100 <-> 203.0.113.5
```

---

## 🛠️ Configuration

### Router - Interface Setup
```ios
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown

interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.252
 ip nat outside
 no shutdown
```

### Static NAT (Server)
```ios
ip nat inside source static 192.168.1.100 203.0.113.5
```

### Dynamic NAT (Pool)
```ios
ip nat pool PUBLIC_POOL 203.0.113.10 203.0.113.20 netmask 255.255.255.0
access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 pool PUBLIC_POOL
```

### PAT / NAT Overload
```ios
access-list 2 permit 192.168.1.0 0.0.0.255
ip nat inside source list 2 interface GigabitEthernet0/1 overload
```

---

## ✅ Verification
```ios
show ip nat translations
show ip nat statistics
debug ip nat
clear ip nat translation *
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab covers all three NAT types — Static for fixed server mappings, Dynamic for pool-based translation, and PAT for many-to-one overloading on a single public IP.
