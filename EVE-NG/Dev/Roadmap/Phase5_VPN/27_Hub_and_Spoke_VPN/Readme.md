# 🖧 Lab 27: Hub-and-Spoke VPN

> Build a Hub-and-Spoke topology using GRE tunnels, OSPF for routing, DHCP on WAN, and NAT Exemption for VPN traffic.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 5 - VPN |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Configure Hub router with multiple GRE tunnels to spokes
- ✅ Configure Spoke routers with WAN IP via DHCP
- ✅ Run OSPF over GRE tunnels for dynamic routing
- ✅ Configure NAT with route-map exemption for VPN traffic
- ✅ Verify spoke-to-spoke communication through hub

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| GRE Tunnel | Lab 26 - GRE Tunnel |
| OSPF Single Area | Lab 10 - OSPF |
| NAT/PAT | Lab 14 - NAT PAT |
| DHCP | Lab 15 - DHCP |

---

## 🗺️ Lab Topology
```
         [HUB 203.0.113.1]
         Tunnel10 / Tunnel20
        /                   \
[Spoke1]                   [Spoke2]
DHCP WAN                   DHCP WAN
192.168.10.0/24            192.168.20.0/24
Tunnel10: 10.10.10.2       Tunnel20: 10.10.20.2
```

---

## 🛠️ Configuration

### HUB Router
```ios
interface Tunnel10
 ip address 10.10.10.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 203.0.113.10

interface Tunnel20
 ip address 10.10.20.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 203.0.113.20

router ospf 1
 network 10.10.10.0 0.0.0.3 area 0
 network 10.10.20.0 0.0.0.3 area 0
 network 192.168.0.0 0.0.0.255 area 0
```

### Spoke1 Router
```ios
interface GigabitEthernet0/1
 ip address dhcp
 ip nat outside

interface Tunnel10
 ip address 10.10.10.2 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 203.0.113.1

router ospf 1
 network 10.10.10.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

### NAT Exemption (Spoke)
```ios
! Exempt VPN traffic from NAT
ip access-list extended NAT_EXEMPT
 deny ip 192.168.10.0 0.0.0.255 192.168.0.0 0.0.255.255
 permit ip 192.168.10.0 0.0.0.255 any

route-map NAT_ROUTE permit 10
 match ip address NAT_EXEMPT

ip nat inside source route-map NAT_ROUTE interface GigabitEthernet0/1 overload
```

---

## ✅ Verification
```ios
show interface Tunnel10
show ip ospf neighbor
show ip route ospf
ping 192.168.20.1 source 192.168.10.1
traceroute 192.168.20.1 source 192.168.10.1
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab builds a Hub-and-Spoke VPN using GRE tunnels with DHCP WAN addresses, OSPF routing over tunnels, and NAT exemption to ensure VPN traffic bypasses PAT.
