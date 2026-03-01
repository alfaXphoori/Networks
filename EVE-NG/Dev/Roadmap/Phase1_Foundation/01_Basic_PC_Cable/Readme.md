# 🖧 Lab 01: Basic PC & Cable

> Hands-on lab for connecting PCs and understanding basic cable types in EVE-NG.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 1 - Foundation |
| **Level** | ⭐ Beginner |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Add PC nodes in EVE-NG
- ✅ Connect PCs using Ethernet cables
- ✅ Understand straight-through vs crossover cables
- ✅ Assign static IP addresses to PCs
- ✅ Test basic ping connectivity between PCs

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| EVE-NG Installation | 00_Install Lab |
| Basic IP Addressing | Networking Fundamentals |

---

## 🗺️ Lab Topology
```
[ PC1 ] ──────────────── [ PC2 ]
10.0.0.1/24          10.0.0.2/24
```

---

## 🛠️ Configuration

### PC1
```bash
ifconfig eth0 10.0.0.1 netmask 255.255.255.0 up
```

### PC2
```bash
ifconfig eth0 10.0.0.2 netmask 255.255.255.0 up
```

---

## ✅ Verification
```bash
# From PC1
ping -c 4 10.0.0.2
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
Basic PC-to-PC connectivity using static IP configuration.
