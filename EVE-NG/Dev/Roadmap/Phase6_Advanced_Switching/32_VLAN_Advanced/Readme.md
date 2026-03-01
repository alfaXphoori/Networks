# 🖧 Lab 32: VLAN Advanced

> Configure Private VLANs (isolated and community), Voice VLANs with QoS trust, and advanced VLAN features on Cisco switches.

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
- ✅ Configure Private VLAN with primary VLAN and secondary VLANs
- ✅ Set up isolated and community secondary VLANs
- ✅ Configure Voice VLAN for IP phones alongside data VLAN
- ✅ Enable QoS trust for voice traffic
- ✅ Verify PVLAN isolation and voice VLAN tagging

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| VLAN Configuration | Lab 05 - VLAN |
| STP/RSTP | Lab 05 - STP/RSTP |
| Basic Switch | Lab 02 - Basic Switch |

---

## 🗺️ Lab Topology
```
  Private VLAN:
  Primary VLAN 100
    Isolated VLAN 101 -> Server1 (cannot talk to Server2)
    Community VLAN 102 -> Server2/Server3 (can talk within community)
  Promiscuous Port: Router/Firewall (talks to all)
  
  Voice VLAN:
  Data VLAN 10, Voice VLAN 50
  [PC]---[IP Phone]---[SW1 Fa0/1]
```

---

## 🛠️ Configuration

### Private VLAN Setup
```ios
! Create VLANs with PVLAN types
vlan 100
 private-vlan primary
vlan 101
 private-vlan isolated
vlan 102
 private-vlan community

! Associate secondary VLANs to primary
vlan 100
 private-vlan association 101,102
```

### PVLAN Ports
```ios
! Isolated host port (Server1)
interface FastEthernet0/1
 switchport mode private-vlan host
 switchport private-vlan host-association 100 101

! Community host port (Server2)
interface FastEthernet0/2
 switchport mode private-vlan host
 switchport private-vlan host-association 100 102

! Promiscuous port (Router/Firewall)
interface GigabitEthernet0/1
 switchport mode private-vlan promiscuous
 switchport private-vlan mapping 100 101,102
```

### Voice VLAN Configuration
```ios
interface FastEthernet0/10
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 50
 mls qos trust cos
 spanning-tree portfast
```

---

## ✅ Verification
```ios
show vlan private-vlan
show interfaces FastEthernet0/1 switchport
show interfaces FastEthernet0/10 switchport
show mac address-table
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures Private VLANs for server isolation and community segmentation, and Voice VLANs for IP phone deployment with QoS trust settings.
