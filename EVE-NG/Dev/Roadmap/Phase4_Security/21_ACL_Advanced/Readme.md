# 🖧 Lab 21: ACL Advanced

> Configure Named ACLs, Time-Based ACLs with time ranges, and Reflexive ACLs for stateful return traffic filtering.

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
- ✅ Configure Named ACL for readable access control policies
- ✅ Configure Time-Based ACL using time-range for business hours
- ✅ Configure Reflexive ACL to permit return traffic dynamically
- ✅ Understand dynamic ACL entries created by reflect keyword
- ✅ Verify time-based and reflexive ACL behavior

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| ACL Basic | Lab 20 - ACL Basic |
| NTP Configuration | Lab 17 - NTP |
| Basic Routing | Lab 08 - Basic Routing |

---

## 🗺️ Lab Topology
```
  [Inside 192.168.1.0/24]---[R1 Firewall]---[Outside 203.0.113.0/30]
                             G0/0  G0/1
  NTP synced for time-based rules
  Reflexive ACL on G0/1 outbound
```

---

## 🛠️ Configuration

### Named ACL
```ios
ip access-list extended NAMED_POLICY
 remark Permit web traffic from internal LAN
 permit tcp 192.168.1.0 0.0.0.255 any eq 80
 permit tcp 192.168.1.0 0.0.0.255 any eq 443
 remark Deny Telnet
 deny   tcp any any eq 23
 permit ip any any
```

### Time-Based ACL
```ios
! Define time range for business hours
time-range BUSINESS_HOURS
 periodic weekdays 08:00 to 18:00

! Apply time range in ACL
ip access-list extended TIME_BASED
 permit tcp 192.168.1.0 0.0.0.255 any eq 80 time-range BUSINESS_HOURS
 deny   tcp 192.168.1.0 0.0.0.255 any eq 80
 permit ip any any
```

### Reflexive ACL
```ios
ip access-list extended OUTBOUND
 permit tcp 192.168.1.0 0.0.0.255 any reflect TCP_SESSIONS
 permit udp 192.168.1.0 0.0.0.255 any reflect UDP_SESSIONS
 permit icmp 192.168.1.0 0.0.0.255 any reflect ICMP_SESSIONS

ip access-list extended INBOUND
 evaluate TCP_SESSIONS
 evaluate UDP_SESSIONS
 evaluate ICMP_SESSIONS
 deny ip any any

interface GigabitEthernet0/1
 ip access-group OUTBOUND out
 ip access-group INBOUND in
```

---

## ✅ Verification
```ios
show ip access-lists
show time-range
show clock
show ip access-lists INBOUND
! Watch reflexive entries appear after outbound traffic
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab implements advanced ACL techniques including named ACLs for readability, time-based ACLs for schedule-driven policies, and reflexive ACLs for stateful traffic filtering.
