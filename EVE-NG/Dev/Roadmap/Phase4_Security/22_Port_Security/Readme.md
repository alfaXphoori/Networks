# 🖧 Lab 22: Port Security

> Configure Port Security on Cisco switches to limit MAC addresses, enable sticky MAC learning, and set violation modes.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 4 - Security |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Enable Port Security on access switch interfaces
- ✅ Limit the number of MAC addresses per port
- ✅ Configure sticky MAC address learning
- ✅ Set violation modes: protect, restrict, and shutdown
- ✅ Verify port security counters and error-disabled recovery

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch | Lab 02 - Basic Switch |
| VLAN Configuration | Lab 05 - VLAN |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [PC1 VLAN10]---[SW1 Fa0/1]  (Port Security: max 1, shutdown)
  [PC2 VLAN10]---[SW1 Fa0/2]  (Port Security: max 2, restrict)
  [PC3 VLAN20]---[SW1 Fa0/3]  (Port Security: sticky, protect)
  [Unauthorized PC]---[SW1 Fa0/1]  -> Violation triggered
```

---

## 🛠️ Configuration

### Basic Port Security (Shutdown on violation)
```ios
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation shutdown
 switchport port-security mac-address 0011.2233.4455
```

### Sticky MAC (Dynamic learning + persistent)
```ios
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

### Protect Mode (Silent drop, no log)
```ios
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation protect
 switchport port-security mac-address sticky
```

### Error-Disabled Recovery
```ios
errdisable recovery cause psecure-violation
errdisable recovery interval 300
```

---

## ✅ Verification
```ios
show port-security
show port-security interface FastEthernet0/1
show port-security address
show interfaces FastEthernet0/1 status
show errdisable recovery
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures Port Security with MAC limiting, sticky learning, and all three violation modes to protect switch ports from unauthorized device connections.
