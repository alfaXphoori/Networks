# 🖧 Lab 04: Inter-VLAN Routing (Router-on-a-Stick)

> Configure inter-VLAN routing using a single router interface with sub-interfaces (Router-on-a-Stick).

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
- ✅ Configure sub-interfaces on a router for VLAN routing
- ✅ Enable dot1Q encapsulation on sub-interfaces
- ✅ Route traffic between VLANs through the router
- ✅ Verify inter-VLAN connectivity

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch & VLAN | Lab 02 |
| Basic Router | Lab 03 |

---

## 🗺️ Lab Topology
```
[PC1 VLAN10]──┐
              [SW1]──(Trunk)──[R1 Gi0/0]
[PC2 VLAN20]──┘               ├── Gi0/0.10 (10.0.10.1/24)
                               └── Gi0/0.20 (10.0.20.1/24)
```

| Device | VLAN | IP |
|--------|------|----|
| PC1 | 10 | 10.0.10.10/24, GW: 10.0.10.1 |
| PC2 | 20 | 10.0.20.10/24, GW: 10.0.20.1 |

---

## 🛠️ Configuration

### SW1 - Trunk & Access Ports
```ios
interface GigabitEthernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
exit
interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 20
exit
```

### R1 - Sub-interfaces
```ios
interface GigabitEthernet0/0
 no shutdown
exit

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.0.10.1 255.255.255.0
exit

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.0.20.1 255.255.255.0
exit
```

---

## ✅ Verification
```ios
show ip route
show interfaces GigabitEthernet0/0.10
```

```bash
# From PC1 (VLAN10) to PC2 (VLAN20)
ping -c 4 10.0.20.10
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
Inter-VLAN routing using sub-interfaces allows multiple VLANs to communicate through a single physical router interface.
