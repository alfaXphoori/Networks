# 🖧 Lab 12: BGP Basic

> Configure eBGP and iBGP between three autonomous systems (AS100, AS200, AS300) with AS Path and neighbor relationships.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 2 - Routing Protocols |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure eBGP between AS100, AS200, and AS300
- ✅ Configure iBGP between routers within the same AS
- ✅ Advertise networks and verify AS Path attributes
- ✅ Understand BGP neighbor states and route selection
- ✅ Verify end-to-end connectivity across all AS numbers

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| OSPF Single Area | Lab 10 - OSPF Single Area |
| Static Routing | Lab 07 - Static Routing |
| Router basics | Lab 03 - Basic Router |

---

## 🗺️ Lab Topology
```
  [R1]--------[R2]--------[R3]--------[R4]
  AS100       AS100       AS200       AS300
  10.0.12.1   10.0.12.2   10.0.23.2   10.0.34.2
              10.0.23.1   10.0.34.1
  
  iBGP: R1--R2 (AS100)
  eBGP: R2--R3 (AS100 to AS200)
  eBGP: R3--R4 (AS200 to AS300)
```

---

## 🛠️ Configuration

### R1 (AS100)
```ios
router bgp 100
 bgp router-id 1.1.1.1
 neighbor 10.0.12.2 remote-as 100
 network 192.168.1.0 mask 255.255.255.0
```

### R2 (AS100 - iBGP + eBGP)
```ios
router bgp 100
 bgp router-id 2.2.2.2
 neighbor 10.0.12.1 remote-as 100
 neighbor 10.0.23.2 remote-as 200
 network 10.0.12.0 mask 255.255.255.252
```

### R3 (AS200 - eBGP to AS100 + AS300)
```ios
router bgp 200
 bgp router-id 3.3.3.3
 neighbor 10.0.23.1 remote-as 100
 neighbor 10.0.34.2 remote-as 300
 network 172.16.0.0 mask 255.255.0.0
```

### R4 (AS300)
```ios
router bgp 300
 bgp router-id 4.4.4.4
 neighbor 10.0.34.1 remote-as 200
 network 10.10.0.0 mask 255.255.0.0
```

---

## ✅ Verification
```ios
show ip bgp summary
show ip bgp
show ip bgp neighbors
show ip route bgp
show ip bgp 192.168.1.0
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab establishes BGP peering across three autonomous systems using eBGP for inter-AS connectivity and iBGP for intra-AS route propagation, demonstrating AS Path attribute manipulation.
