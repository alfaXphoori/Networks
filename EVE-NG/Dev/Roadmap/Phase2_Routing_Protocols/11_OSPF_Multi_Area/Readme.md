# 🖧 Lab 11: OSPF Multi-Area

> Configure OSPF with multiple areas including ABR/ASBR, LSA types, and Area 0 backbone connected to Areas 1 and 2.

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
- ✅ Configure OSPF multi-area with Area 0 backbone
- ✅ Set up ABR (Area Border Router) between Area 0, Area 1, and Area 2
- ✅ Configure ASBR (Autonomous System Boundary Router) for external route injection
- ✅ Understand LSA Types 1, 2, 3, 4, 5, and 7
- ✅ Verify inter-area routing and summarization

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| OSPF Single Area | Lab 10 - OSPF Single Area |
| Subnetting | Lab 01 - Basic PC Cable |
| Router basics | Lab 03 - Basic Router |

---

## 🗺️ Lab Topology
```
  [R1-Area1]----(Area 1)----[ABR1]----(Area 0)----[ABR2]----(Area 2)----[R4-Area2]
       |                      |                      |
  192.168.1.0/24         10.0.12.0/30           10.0.23.0/30
                              |
                           [ASBR]
                              |
                       (External Network)
                        172.16.0.0/16
```

---

## 🛠️ Configuration

### R1 (Area 1 Internal Router)
```ios
router ospf 1
 router-id 1.1.1.1
 network 192.168.1.0 0.0.0.255 area 1
 network 10.0.11.0 0.0.0.3 area 1
```

### ABR1 (Area 0 / Area 1 Border Router)
```ios
router ospf 1
 router-id 2.2.2.2
 network 10.0.11.0 0.0.0.3 area 1
 network 10.0.12.0 0.0.0.3 area 0
 area 1 range 192.168.1.0 255.255.255.0
```

### ABR2 (Area 0 / Area 2 Border Router)
```ios
router ospf 1
 router-id 3.3.3.3
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 2
 area 2 range 192.168.2.0 255.255.255.0
```

### ASBR (Redistributing External Routes)
```ios
router ospf 1
 router-id 5.5.5.5
 network 10.0.12.0 0.0.0.3 area 0
 redistribute connected subnets
 redistribute static subnets
```

---

## ✅ Verification
```ios
show ip ospf neighbor
show ip ospf database
show ip ospf database summary
show ip route ospf
show ip ospf border-routers
show ip ospf database external
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab demonstrates OSPF multi-area design with ABRs connecting Area 0 backbone to Areas 1 and 2, and an ASBR injecting external routes via LSA Type 5.
