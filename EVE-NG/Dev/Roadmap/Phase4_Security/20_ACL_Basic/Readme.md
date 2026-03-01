# 🖧 Lab 20: ACL Basic

> Configure Standard and Extended ACLs to permit or deny traffic by source IP, destination IP, and port, applied inbound and outbound.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 4 - Security |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure Standard ACL (permit/deny by source IP only)
- ✅ Configure Extended ACL (source + destination + port)
- ✅ Apply ACLs inbound and outbound on interfaces
- ✅ Understand implicit deny at the end of each ACL
- ✅ Verify ACL hit counters and traffic filtering

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| Inter-VLAN Routing | Lab 04 - Inter-VLAN Routing |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [PC1 192.168.1.10]---[R1]---[R2]---[Server 10.0.2.100]
  [PC2 192.168.1.20]          |
                         [ACL Applied]
  VLAN10: 192.168.1.0/24
  Server: 10.0.2.0/24
```

---

## 🛠️ Configuration

### Standard ACL (Filter by Source IP)
```ios
! Permit only PC1, deny everything else
access-list 10 permit host 192.168.1.10
access-list 10 deny any

! Apply outbound on interface toward server
interface GigabitEthernet0/1
 ip access-group 10 out
```

### Extended ACL (Filter by Src + Dst + Port)
```ios
! Permit HTTP/HTTPS from 192.168.1.0/24 to server only
! Deny Telnet from anywhere
! Permit ICMP for troubleshooting
ip access-list extended INTERNET_POLICY
 permit tcp 192.168.1.0 0.0.0.255 host 10.0.2.100 eq 80
 permit tcp 192.168.1.0 0.0.0.255 host 10.0.2.100 eq 443
 deny   tcp any any eq 23
 permit icmp any any
 deny   ip any any

! Apply inbound on LAN interface
interface GigabitEthernet0/0
 ip access-group INTERNET_POLICY in
```

---

## ✅ Verification
```ios
show ip access-lists
show ip interface GigabitEthernet0/0
show ip interface GigabitEthernet0/1
show run | section access-list
clear ip access-list counters
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures Standard and Extended ACLs to control network traffic, demonstrating inbound/outbound placement and the impact of implicit deny on unmatched traffic.
