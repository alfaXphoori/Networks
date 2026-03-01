# 🖧 Lab 25: DHCP Snooping & DAI

> Configure DHCP Snooping trusted/untrusted ports, Dynamic ARP Inspection, and IP Source Guard to prevent rogue DHCP and ARP attacks.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 4 - Security |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure DHCP Snooping to block rogue DHCP servers
- ✅ Define trusted (uplink) and untrusted (access) ports
- ✅ Configure Dynamic ARP Inspection (DAI) using the snooping binding table
- ✅ Enable IP Source Guard to prevent IP spoofing
- ✅ Verify snooping bindings and DAI drop statistics

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| DHCP Configuration | Lab 15 - DHCP |
| VLAN Configuration | Lab 05 - VLAN |
| Basic Switch | Lab 02 - Basic Switch |

---

## 🗺️ Lab Topology
```
  [DHCP Server]---[TRUSTED Uplink Fa0/24]---[SW1]---[Fa0/1 PC1]
  [Rogue DHCP]---[UNTRUSTED Fa0/2]---[SW1]          [Fa0/2 PC2]
  
  VLAN10: 192.168.1.0/24
  Trusted port: Fa0/24 (uplink to router/DHCP server)
  Untrusted: all access ports
```

---

## 🛠️ Configuration

### DHCP Snooping
```ios
ip dhcp snooping
ip dhcp snooping vlan 10,20
no ip dhcp snooping information option

! Mark uplink to router as trusted
interface GigabitEthernet0/1
 ip dhcp snooping trust

! Access ports are untrusted by default
interface range FastEthernet0/1-20
 ip dhcp snooping limit rate 15
```

### Dynamic ARP Inspection (DAI)
```ios
ip arp inspection vlan 10,20

! Uplink is trusted for ARP inspection too
interface GigabitEthernet0/1
 ip arp inspection trust

! Rate limit ARP on access ports
interface range FastEthernet0/1-20
 ip arp inspection limit rate 100
```

### ARP ACL for Static Hosts (no DHCP)
```ios
arp access-list STATIC_HOSTS
 permit ip host 192.168.1.1 mac host 00AA.BB11.2233

ip arp inspection filter STATIC_HOSTS vlan 10
```

### IP Source Guard
```ios
interface FastEthernet0/1
 ip verify source
```

---

## ✅ Verification
```ios
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics
show ip arp inspection
show ip arp inspection statistics
show ip arp inspection vlan 10
show ip verify source
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab hardens Layer 2 infrastructure by deploying DHCP Snooping to prevent rogue DHCP servers, Dynamic ARP Inspection to stop ARP poisoning, and IP Source Guard to block IP spoofing.
