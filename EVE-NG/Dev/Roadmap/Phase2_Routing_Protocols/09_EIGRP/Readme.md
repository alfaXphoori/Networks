# 🖧 Lab 09: EIGRP (Enhanced Interior Gateway Routing Protocol)

> Configure EIGRP, Cisco's advanced distance-vector routing protocol.

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
- ✅ Configure EIGRP with AS number on multiple routers
- ✅ Understand DUAL algorithm and successor/feasible successor
- ✅ Configure EIGRP route summarization
- ✅ Tune EIGRP metrics (bandwidth, delay)
- ✅ Verify neighbor relationships

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| RIP v2 | Lab 08 |

---

## 🗺️ Lab Topology
```
[PC1]──[R1]──[R2]──[R3]──[PC2]
         \       /
           [R4]
```

---

## 🛠️ Configuration

### All Routers
```ios
router eigrp 100
 no auto-summary
 network 10.0.0.0 0.255.255.255
exit
```

---

## ✅ Verification
```ios
show ip eigrp neighbors
show ip eigrp topology
show ip route eigrp
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![EIGRP Neighbors](imgs/eigrp_neighbors.png)

---

## 📝 Summary
EIGRP uses DUAL for loop-free fast convergence. Supports unequal cost load balancing.
