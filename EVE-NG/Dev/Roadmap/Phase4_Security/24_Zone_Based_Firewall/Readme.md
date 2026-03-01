# 🖧 Lab 24: Zone-Based Firewall (ZBF)

> Configure Cisco Zone-Based Firewall with inside, outside, and DMZ zones using class-maps, policy-maps, and service-policy.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 4 - Security |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Create security zones (INSIDE, OUTSIDE, DMZ)
- ✅ Assign interfaces to zones
- ✅ Define traffic class-maps using match criteria
- ✅ Build policy-maps with inspect/drop/pass actions
- ✅ Apply service-policy to zone pairs

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| ACL Basic | Lab 20 - ACL Basic |
| NAT/PAT | Lab 14 - NAT PAT |
| Basic Routing | Lab 08 - Basic Routing |

---

## 🗺️ Lab Topology
```
  [Inside 192.168.1.0/24]---G0/0---[ZBF Router]---G0/1---[Outside/Internet]
                                         |
                                        G0/2
                                         |
                               [DMZ 172.16.0.0/24]
                               [Web/Mail Server]
```

---

## 🛠️ Configuration

### Define Zones
```ios
zone security INSIDE
 description Internal LAN

zone security OUTSIDE
 description Internet

zone security DMZ
 description Servers DMZ
```

### Assign Interfaces to Zones
```ios
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 zone-member security INSIDE
 no shutdown

interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.252
 zone-member security OUTSIDE
 no shutdown

interface GigabitEthernet0/2
 ip address 172.16.0.1 255.255.255.0
 zone-member security DMZ
 no shutdown
```

### Class-Maps
```ios
class-map type inspect match-any INSIDE_PROTOCOLS
 match protocol http
 match protocol https
 match protocol dns
 match protocol icmp

class-map type inspect match-any DMZ_ACCESS
 match protocol http
 match protocol https
```

### Policy-Maps
```ios
policy-map type inspect INSIDE_TO_OUTSIDE
 class type inspect INSIDE_PROTOCOLS
  inspect
 class class-default
  drop log

policy-map type inspect INSIDE_TO_DMZ
 class type inspect DMZ_ACCESS
  inspect
 class class-default
  drop
```

### Zone-Pairs (Service Policy)
```ios
zone-pair security IN_OUT source INSIDE destination OUTSIDE
 service-policy type inspect INSIDE_TO_OUTSIDE

zone-pair security IN_DMZ source INSIDE destination DMZ
 service-policy type inspect INSIDE_TO_DMZ
```

---

## ✅ Verification
```ios
show zone security
show zone-pair security
show policy-map type inspect zone-pair
show class-map type inspect
debug policy-firewall
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab implements a Zone-Based Firewall with three zones (INSIDE, OUTSIDE, DMZ) using Cisco's policy-based inspection model to control and inspect traffic between zones.
