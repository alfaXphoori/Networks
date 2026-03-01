# 🖧 Lab 08: RIP v2 (Distance Vector Routing)

> Configure RIPv2 dynamic routing protocol for automatic route discovery.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 2 - Routing Protocols |
| **Level** | ⭐⭐ Beginner |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure RIPv2 on multiple routers
- ✅ Understand distance vector routing behavior
- ✅ Configure route summarization
- ✅ Observe RIP convergence

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Static Routing | Lab 07 |

---

## 🗺️ Lab Topology
```
[PC1]──[R1]──[R2]──[R3]──[PC2]
```

---

## 🛠️ Configuration

### All Routers (R1, R2, R3)
```ios
router rip
 version 2
 no auto-summary
 network 10.0.0.0
exit
```

---

## ✅ Verification
```ios
show ip route rip
show ip rip database
debug ip rip
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![RIP Routes](imgs/rip_routes.png)

---

## 📝 Summary
RIPv2 uses hop count as metric with max 15 hops. Simple to configure but not suitable for large networks.
