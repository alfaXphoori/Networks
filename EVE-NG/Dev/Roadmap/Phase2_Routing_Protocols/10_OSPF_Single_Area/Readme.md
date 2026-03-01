# 🖧 Lab 10: OSPF Single Area

> Configure OSPF single area routing protocol for link-state dynamic routing.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 2 - Routing Protocols |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Configure OSPF Area 0 on all routers
- ✅ Assign Router IDs
- ✅ Understand DR/BDR election
- ✅ Verify OSPF neighbor adjacency
- ✅ Observe OSPF route table

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| EIGRP | Lab 09 |

---

## 🗺️ Lab Topology
```
[R1]──[R2]──[R3]
  \        /
   [R4]──[R5]
   All in Area 0
```

---

## 🛠️ Configuration

### All Routers
```ios
router ospf 1
 router-id X.X.X.X
 network 10.0.0.0 0.255.255.255 area 0
exit
```

---

## ✅ Verification
```ios
show ip ospf neighbor
show ip ospf database
show ip route ospf
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![OSPF Neighbors](imgs/ospf_neighbors.png)

---

## 📝 Summary
OSPF is a link-state protocol using SPF algorithm. Faster convergence than distance-vector protocols.
