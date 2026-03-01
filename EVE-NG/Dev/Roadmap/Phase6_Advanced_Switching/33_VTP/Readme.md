# 🖧 Lab 33: VTP (VLAN Trunking Protocol)

> Configure VTP server, client, and transparent modes with a shared VTP domain, version, and VLAN pruning across trunk links.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 6 - Advanced Switching |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Configure VTP server mode on distribution switch
- ✅ Configure VTP client mode on access switches
- ✅ Configure VTP transparent mode on isolated switch
- ✅ Set VTP domain name, version, and password
- ✅ Enable VTP pruning and verify VLAN propagation

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| VLAN Configuration | Lab 05 - VLAN |
| Basic Switch | Lab 02 - Basic Switch |
| Trunk Configuration | Lab 05 - VLAN |

---

## 🗺️ Lab Topology
```
  [SW1 - VTP Server]
       |  trunk
  [SW2 - VTP Client]---[SW3 - VTP Client]
       |  trunk
  [SW4 - VTP Transparent]
  Domain: LAB_DOMAIN
  VTP Version: 2
```

---

## 🛠️ Configuration

### SW1 - VTP Server
```ios
vtp mode server
vtp domain LAB_DOMAIN
vtp version 2
vtp password VTP_Pass123

! Create VLANs on server (propagate to clients)
vlan 10
 name DATA
vlan 20
 name VOICE
vlan 30
 name MGMT
vlan 40
 name SERVERS
```

### SW2, SW3 - VTP Clients
```ios
vtp mode client
vtp domain LAB_DOMAIN
vtp version 2
vtp password VTP_Pass123
! Clients cannot create/delete VLANs
```

### SW4 - VTP Transparent
```ios
vtp mode transparent
vtp domain LAB_DOMAIN
vtp version 2
! Creates local VLANs only, forwards VTP messages
vlan 50
 name LOCAL_ONLY
```

### VTP Pruning (on server)
```ios
vtp pruning
! Pruning eligible VLANs
switchport trunk pruning vlan 10,20,30,40
```

### Trunk Links
```ios
interface GigabitEthernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan all
```

---

## ✅ Verification
```ios
show vtp status
show vtp counters
show vlan brief
show interfaces trunk
show vtp password
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures VTP to centralize VLAN management across a switched network with server/client modes, transparent mode for local VLANs, and pruning to optimize trunk bandwidth.
