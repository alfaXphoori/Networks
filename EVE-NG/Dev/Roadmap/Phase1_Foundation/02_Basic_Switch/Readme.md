# 🖧 Lab 02: Basic Switch (VLAN, Trunk, Access Port)

> Hands-on lab for VLAN creation, port assignment, and trunk link configuration.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 1 - Foundation |
| **Level** | ⭐ Beginner |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Create VLANs on Cisco switches
- ✅ Assign ports to VLANs (Access mode)
- ✅ Configure trunk links between switches (802.1Q)
- ✅ Test intra-VLAN connectivity
- ✅ Verify VLAN isolation

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic PC & Cable | Lab 01 |
| IP Addressing | Networking Fundamentals |

---

## 🗺️ Lab Topology
```
[PC1]──[SW1 Gi0/0]   [SW1 Gi0/1]──[PC2]
         |                              
    [SW1 Gi1/0] (Trunk)                
         |                              
    [SW2 Gi1/0] (Trunk)                
         |                              
[PC3]──[SW2 Gi0/0]   [SW2 Gi0/1]──[PC4]
```

| Device | VLAN | IP |
|--------|------|----|
| PC1 | VLAN 10 | 192.168.10.1/24 |
| PC2 | VLAN 20 | 192.168.20.1/24 |
| PC3 | VLAN 10 | 192.168.10.2/24 |
| PC4 | VLAN 20 | 192.168.20.2/24 |

---

## 🛠️ Configuration

### SW1 & SW2 - Create VLANs
```ios
enable
configure terminal
vlan 10
 name VLAN10
vlan 20
 name VLAN20
exit
```

### SW1 - Access Ports
```ios
interface GigabitEthernet0/0
 switchport mode access
 switchport access vlan 10
exit
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 20
exit
```

### SW1 & SW2 - Trunk Link
```ios
interface GigabitEthernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
```

---

## ✅ Verification
```ios
show vlan brief
show interfaces trunk
show mac address-table
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
VLAN segmentation and trunk link configuration between switches.
