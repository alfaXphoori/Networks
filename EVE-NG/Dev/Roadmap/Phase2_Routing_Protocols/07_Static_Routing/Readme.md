# 🖧 Lab 07: Static Routing

> Configure static and default routes for inter-network communication.

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
- ✅ Configure static routes between routers
- ✅ Configure default route for Internet access
- ✅ Understand administrative distance
- ✅ Test connectivity across multiple hops

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Router | Lab 03 |
| IP Subnetting | Networking Fundamentals |

---

## 🗺️ Lab Topology
```
[PC1]──[R1]──[R2]──[R3]──[PC2]
10.x       12.x   23.x   30.x
```

| Link | Subnet |
|------|--------|
| R1 LAN | 10.0.0.0/24 |
| R1-R2 | 10.1.12.0/30 |
| R2-R3 | 10.1.23.0/30 |
| R3 LAN | 30.0.0.0/24 |

---

## 🛠️ Configuration

### R1
```ios
ip route 10.1.23.0 255.255.255.252 10.1.12.2
ip route 30.0.0.0 255.255.255.0 10.1.12.2
```

### R2
```ios
ip route 10.0.0.0 255.255.255.0 10.1.12.1
ip route 30.0.0.0 255.255.255.0 10.1.23.2
```

### R3
```ios
ip route 10.0.0.0 255.255.255.0 10.1.23.1
ip route 10.1.12.0 255.255.255.252 10.1.23.1
```

---

## ✅ Verification
```ios
show ip route
show ip route static
```

```bash
ping -c 4 30.0.0.10
traceroute 30.0.0.10
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Routing Table](imgs/routing_table.png)

---

## 📝 Summary
Static routes provide manual control over network paths. Default routes simplify routing for unknown destinations.
