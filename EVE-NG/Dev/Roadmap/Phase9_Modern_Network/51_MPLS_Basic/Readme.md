# 🖧 Lab 51: MPLS Basic (Label Switching & VRF L3VPN)

> Implement MPLS label switching and configure L3VPN for customer VRF separation.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 9 - Modern Network |
| **Level** | ⭐⭐⭐⭐⭐ Expert |
| **Status** | 🔲 Todo |
| **Est. Time** | 5-6 hours |

---

## 🎯 Lab Objectives
- ✅ Enable MPLS LDP on provider routers
- ✅ Understand label distribution and FIB
- ✅ Configure VRF for customer separation
- ✅ Set up BGP VPNv4 between PE routers
- ✅ Verify MPLS L3VPN customer isolation

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| OSPF Multi-Area | Lab 11 |
| BGP Basic | Lab 12 |

---

## 🗺️ Lab Topology
```
[CE1-A]──[PE1]══[P1]══[P2]══[PE2]──[CE2-A]   (Customer A)
[CE1-B]──[PE1]  MPLS Backbone   [PE2]──[CE2-B]   (Customer B)
```

| Role | Device | Description |
|------|--------|-------------|
| CE | Customer Edge | Connects customer to provider |
| PE | Provider Edge | MPLS VPN + VRF handling |
| P | Provider Core | Label forwarding only |

---

## 🛠️ Configuration

### Enable MPLS on Provider Routers (P, PE)
```ios
mpls ip

interface GigabitEthernet0/0
 mpls ip
```

### Configure VRF on PE Router
```ios
ip vrf CUSTOMER_A
 rd 100:1
 route-target export 100:1
 route-target import 100:1

ip vrf CUSTOMER_B
 rd 100:2
 route-target export 100:2
 route-target import 100:2

interface GigabitEthernet0/1
 ip vrf forwarding CUSTOMER_A
 ip address 10.0.0.1 255.255.255.0
```

### BGP VPNv4 Between PE Routers
```ios
router bgp 65000
 neighbor 10.100.100.2 remote-as 65000
 neighbor 10.100.100.2 update-source Loopback0
 
 address-family vpnv4
  neighbor 10.100.100.2 activate
  neighbor 10.100.100.2 send-community extended
```

---

## ✅ Verification
```ios
show mpls forwarding-table
show mpls ldp neighbor
show ip bgp vpnv4 all
show ip route vrf CUSTOMER_A
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![MPLS Labels](imgs/mpls_labels.png)
![VRF Routes](imgs/vrf_routes.png)

---

## 📝 Summary
MPLS enables high-speed label switching and L3VPN provides logical separation of multiple customers over shared infrastructure.
