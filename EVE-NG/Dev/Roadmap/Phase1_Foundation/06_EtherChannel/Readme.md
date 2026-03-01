# 🖧 Lab 06: EtherChannel (LACP & PAgP)

> Configure EtherChannel to bundle multiple physical links into a single logical interface.

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
- ✅ Configure EtherChannel using LACP (IEEE 802.3ad)
- ✅ Configure EtherChannel using PAgP (Cisco proprietary)
- ✅ Verify load balancing across bundled links
- ✅ Test failover when one link goes down

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch | Lab 02 |
| STP | Lab 05 |

---

## 🗺️ Lab Topology
```
[SW1] ══════ [SW2]
      (Po1)
      Gi0/0
      Gi0/1
      Gi0/2
```

---

## 🛠️ Configuration

### SW1 & SW2 - LACP EtherChannel
```ios
enable
configure terminal

interface range GigabitEthernet0/0 - 2
 channel-protocol lacp
 channel-group 1 mode active
exit

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
```

### Alternative - PAgP
```ios
interface range GigabitEthernet0/0 - 2
 channel-protocol pagp
 channel-group 1 mode desirable
exit
```

---

## ✅ Verification
```ios
show etherchannel summary
show etherchannel port-channel
show interfaces Port-channel1
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![EtherChannel Summary](imgs/etherchannel_summary.png)

---

## 📝 Summary
EtherChannel bundles multiple physical links for increased bandwidth and redundancy.
