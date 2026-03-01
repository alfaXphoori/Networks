# 🖧 Lab 30: DMVPN

> Configure DMVPN Phase 1/2/3 using mGRE, NHRP, and OSPF for dynamic spoke-to-spoke communication without hub hair-pinning.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 5 - VPN |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 4-5 hours |

---

## 🎯 Lab Objectives
- ✅ Configure DMVPN Phase 1 (Hub-Spoke only)
- ✅ Upgrade to Phase 2 (direct spoke-to-spoke)
- ✅ Configure Phase 3 with NHRP redirect for scalability
- ✅ Use mGRE on hub for dynamic multipoint tunnels
- ✅ Run OSPF over DMVPN and verify routing

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| GRE Tunnel | Lab 26 - GRE Tunnel |
| GRE over IPsec | Lab 29 - GRE over IPsec |
| OSPF Single Area | Lab 10 - OSPF |

---

## 🗺️ Lab Topology
```
              [HUB 203.0.113.1]
              mGRE Tunnel0
              NHRP Server
             /              \
    [Spoke1]                [Spoke2]
    203.0.113.10            203.0.113.20
    NHRP Client             NHRP Client
    LAN: 192.168.1.0/24    LAN: 192.168.2.0/24
    
DMVPN Tunnel: 10.0.0.0/24
Hub: 10.0.0.1, Spoke1: 10.0.0.2, Spoke2: 10.0.0.3
```

---

## 🛠️ Configuration

### HUB Router (mGRE + NHRP Server)
```ios
interface Tunnel0
 ip address 10.0.0.1 255.255.255.0
 ip nhrp network-id 100
 ip nhrp map multicast dynamic
 ip nhrp redirect
 tunnel source GigabitEthernet0/1
 tunnel mode gre multipoint
 tunnel key 12345
 ip ospf network broadcast
 ip ospf priority 2
 no shutdown
```

### Spoke1 Router
```ios
interface Tunnel0
 ip address 10.0.0.2 255.255.255.0
 ip nhrp network-id 100
 ip nhrp nhs 10.0.0.1
 ip nhrp map 10.0.0.1 203.0.113.1
 ip nhrp map multicast 203.0.113.1
 ip nhrp shortcut
 tunnel source GigabitEthernet0/1
 tunnel mode gre multipoint
 tunnel key 12345
 ip ospf network broadcast
 ip ospf priority 0
 no shutdown

router ospf 1
 network 10.0.0.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
```

### IPsec Protection (all routers)
```ios
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14

crypto isakmp key DMVPN_Key address 0.0.0.0 0.0.0.0

crypto ipsec transform-set DMVPN_TS esp-aes 256 esp-sha256-hmac
 mode transport

crypto ipsec profile DMVPN_PROFILE
 set transform-set DMVPN_TS

interface Tunnel0
 tunnel protection ipsec profile DMVPN_PROFILE shared
```

---

## ✅ Verification
```ios
show dmvpn
show ip nhrp
show ip nhrp brief
show ip ospf neighbor
show crypto isakmp sa
ping 192.168.2.1 source 192.168.1.1
show ip nhrp detail
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab builds a DMVPN network with mGRE hub, NHRP for dynamic endpoint registration, IPsec encryption, and OSPF routing — progressing through Phase 1, 2, and 3 for spoke-to-spoke optimization.
