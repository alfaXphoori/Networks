# 🖧 Lab 13: Route Redistribution

> Redistribute routes between OSPF, EIGRP, and BGP using redistribute commands and route-maps for controlled route injection.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 2 - Routing Protocols |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Configure mutual redistribution between OSPF and EIGRP
- ✅ Redistribute OSPF routes into BGP
- ✅ Use route-maps to filter and tag redistributed routes
- ✅ Prevent routing loops using administrative distance and tags
- ✅ Verify route propagation and metric manipulation

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| OSPF Multi-Area | Lab 11 - OSPF Multi-Area |
| EIGRP | Lab 09 - EIGRP |
| BGP Basic | Lab 12 - BGP Basic |

---

## 🗺️ Lab Topology
```
  [OSPF Domain]---[Redistribution Router]---[EIGRP Domain]
  192.168.1.0/24        (ASBR)              10.10.0.0/24
                          |
                       [BGP AS100]
                       172.16.0.0/16
```

---

## 🛠️ Configuration

### Redistribution Router (ASBR)
```ios
! Route-map to tag OSPF routes before redistributing into EIGRP
route-map OSPF_TO_EIGRP permit 10
 match ip address prefix-list OSPF_NETS
 set tag 100

! Route-map to tag EIGRP routes before redistributing into OSPF
route-map EIGRP_TO_OSPF permit 10
 match ip address prefix-list EIGRP_NETS
 set tag 200

! Redistribute OSPF into EIGRP
router eigrp 1
 redistribute ospf 1 metric 10000 100 255 1 1500 route-map OSPF_TO_EIGRP

! Redistribute EIGRP into OSPF
router ospf 1
 redistribute eigrp 1 subnets route-map EIGRP_TO_OSPF

! Redistribute OSPF into BGP
router bgp 100
 redistribute ospf 1 route-map OSPF_TO_BGP
```

### Loop Prevention (Deny tags in redistribution)
```ios
route-map OSPF_TO_EIGRP deny 5
 match tag 200

route-map EIGRP_TO_OSPF deny 5
 match tag 100
```

---

## ✅ Verification
```ios
show ip route
show ip route ospf
show ip route eigrp
show ip route bgp
show ip ospf database external
show ip eigrp topology
debug ip routing
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab demonstrates controlled route redistribution between OSPF, EIGRP, and BGP using route-maps and tags to prevent routing loops while enabling full network reachability.
