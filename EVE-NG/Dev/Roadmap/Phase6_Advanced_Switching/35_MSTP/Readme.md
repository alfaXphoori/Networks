# 🖧 Lab 35: MSTP (Multiple Spanning Tree)

> Configure Multiple Spanning Tree Protocol instances, define MST regions, and map VLANs to specific MST instances for load balancing.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 6 - Advanced Switching |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure MST region with name, revision, and VLAN-to-instance mapping
- ✅ Create MST Instance 1 for VLANs 10-20 (SW1 as root)
- ✅ Create MST Instance 2 for VLANs 30-40 (SW2 as root)
- ✅ Achieve load balancing by having different roots per instance
- ✅ Verify MST topology and port roles

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| STP/RSTP | Lab 05 - STP/RSTP |
| VLAN Configuration | Lab 05 - VLAN |
| VTP | Lab 33 - VTP |

---

## 🗺️ Lab Topology
```
        [SW1]==========[SW2]
        /   \          /   \
    [SW3]  [SW4]   [SW5]  [SW6]
    
MST Instance 0: IST (Internal Spanning Tree)
MST Instance 1: VLAN 10,20 -> Root: SW1
MST Instance 2: VLAN 30,40 -> Root: SW2
Region: LAB_REGION, Revision: 1
```

---

## 🛠️ Configuration

### All Switches - MST Region Configuration
```ios
spanning-tree mode mst

spanning-tree mst configuration
 name LAB_REGION
 revision 1
 instance 1 vlan 10,20
 instance 2 vlan 30,40
```

### SW1 - Root for MST Instance 1
```ios
spanning-tree mst 1 priority 4096
spanning-tree mst 2 priority 8192
! SW1 is primary root for Instance 1
! SW1 is secondary root for Instance 2
```

### SW2 - Root for MST Instance 2
```ios
spanning-tree mst 2 priority 4096
spanning-tree mst 1 priority 8192
! SW2 is primary root for Instance 2
! SW2 is secondary root for Instance 1
```

### Port Configuration
```ios
! Edge ports (access ports) - PortFast + BPDU Guard
interface range FastEthernet0/1-20
 spanning-tree portfast
 spanning-tree bpduguard enable

! Uplink ports
interface GigabitEthernet0/1
 spanning-tree mst 1 cost 100
 spanning-tree mst 2 cost 200
```

---

## ✅ Verification
```ios
show spanning-tree mst configuration
show spanning-tree mst
show spanning-tree mst 1
show spanning-tree mst 2
show spanning-tree mst interface GigabitEthernet0/1 detail
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab implements MSTP with two instances mapped to different VLAN groups, using different root bridges per instance to achieve STP load balancing across trunk links.
