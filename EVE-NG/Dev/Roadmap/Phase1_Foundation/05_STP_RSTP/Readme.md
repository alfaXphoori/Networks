# 🖧 Lab 05: STP & RSTP (Spanning Tree Protocol)

> Configure and observe Spanning Tree Protocol behavior to prevent switching loops.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 1 - Foundation |
| **Level** | ⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Understand STP loop prevention mechanism
- ✅ Configure root bridge priority
- ✅ Observe port states (Blocking, Listening, Learning, Forwarding)
- ✅ Configure RSTP for faster convergence
- ✅ Test failover behavior

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch & VLAN | Lab 02 |

---

## 🗺️ Lab Topology
```
        [SW1] (Root)
       /      \
    [SW2] ── [SW3]
```

---

## 🛠️ Configuration

### SW1 - Set Root Bridge
```ios
enable
configure terminal
spanning-tree vlan 1 priority 4096
```

### All Switches - Enable RSTP
```ios
spanning-tree mode rapid-pvst
```

### Verify Port Roles
```ios
show spanning-tree vlan 1
show spanning-tree detail
```

---

## ✅ Verification
```ios
show spanning-tree
show spanning-tree vlan 1 detail
show spanning-tree active
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![STP Output](imgs/stp_output.png)

---

## 📝 Summary
STP prevents switching loops in redundant topologies. RSTP provides faster convergence compared to classic STP.
