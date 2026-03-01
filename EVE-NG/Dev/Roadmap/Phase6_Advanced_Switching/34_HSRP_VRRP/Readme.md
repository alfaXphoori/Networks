# 🖧 Lab 34: HSRP & VRRP

> Configure HSRP active/standby and VRRP master/backup gateway redundancy with virtual IPs, priorities, and preemption.

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
- ✅ Configure HSRP with active/standby roles and virtual IP
- ✅ Set HSRP priorities and enable preemption
- ✅ Configure HSRP interface tracking to fail over on uplink loss
- ✅ Configure VRRP as an open-standard alternative
- ✅ Verify gateway failover and recovery behavior

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Inter-VLAN Routing | Lab 04 - Inter-VLAN Routing |
| VLAN Configuration | Lab 05 - VLAN |
| Basic Routing | Lab 08 - Basic Routing |

---

## 🗺️ Lab Topology
```
  [PC VLAN10]---[SW1]---[R1 .1 (Active)]---[Internet]
                    \---[R2 .2 (Standby)]---[Internet]
  Virtual IP: 192.168.10.254
  R1: 192.168.10.1  Priority 110
  R2: 192.168.10.2  Priority 100
```

---

## 🛠️ Configuration

### R1 - HSRP Active
```ios
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 standby version 2
 standby 1 ip 192.168.10.254
 standby 1 priority 110
 standby 1 preempt delay minimum 15
 standby 1 track GigabitEthernet0/1 30
 standby 1 authentication md5 key-string HSRPsecret
```

### R2 - HSRP Standby
```ios
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.2 255.255.255.0
 standby version 2
 standby 1 ip 192.168.10.254
 standby 1 priority 100
 standby 1 preempt
 standby 1 authentication md5 key-string HSRPsecret
```

### VRRP Configuration (Alternative to HSRP)
```ios
! R1 - VRRP Master
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 vrrp 1 ip 192.168.20.254
 vrrp 1 priority 110
 vrrp 1 preempt
 vrrp 1 authentication md5 keystring VRRPsecret

! R2 - VRRP Backup
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.2 255.255.255.0
 vrrp 1 ip 192.168.20.254
 vrrp 1 priority 100
```

---

## ✅ Verification
```ios
show standby
show standby brief
show vrrp
show vrrp brief
! Simulate failover
shutdown interface GigabitEthernet0/1
show standby
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures HSRP and VRRP for first-hop redundancy, providing automatic gateway failover with configurable priorities, preemption, and interface tracking.
