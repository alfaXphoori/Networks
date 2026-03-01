# 🖧 Lab 50: IPv6 Complete (Dual Stack, OSPFv3 & Tunneling)

> Deploy a complete IPv6 network with dual-stack, OSPFv3 routing, and IPv6 tunneling mechanisms.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 9 - Modern Network |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 4-5 hours |

---

## 🎯 Lab Objectives
- ✅ Configure IPv6 addresses on router interfaces
- ✅ Enable dual-stack (IPv4 + IPv6) operation
- ✅ Configure OSPFv3 for IPv6 routing
- ✅ Set up 6to4 and ISATAP tunneling
- ✅ Test IPv6 connectivity end-to-end

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| OSPF Single Area | Lab 10 |
| GRE Tunnel | Lab 26 |

---

## 🗺️ Lab Topology
```
[PC1 IPv6]──[R1]──[R2]──[R3]──[PC2 IPv6]
         2001:db8:1::/64        2001:db8:3::/64
              Dual Stack Throughout
```

| Device | IPv6 Address |
|--------|-------------|
| R1 Gi0/1 | 2001:db8:1::1/64 |
| R1 Gi0/0 | 2001:db8:12::1/64 |
| R2 Gi0/0 | 2001:db8:12::2/64 |
| R2 Gi0/1 | 2001:db8:23::1/64 |
| R3 Gi0/0 | 2001:db8:23::2/64 |
| R3 Gi0/1 | 2001:db8:3::1/64 |

---

## 🛠️ Configuration

### Enable IPv6 on Routers
```ios
ipv6 unicast-routing

interface GigabitEthernet0/0
 ipv6 address 2001:db8:12::1/64
 ipv6 enable
 no shutdown
```

### OSPFv3 Configuration
```ios
ipv6 router ospf 1
 router-id 1.1.1.1
exit

interface GigabitEthernet0/0
 ipv6 ospf 1 area 0
```

### 6to4 Tunnel (IPv6 over IPv4)
```ios
interface Tunnel0
 ipv6 address 2002:c0a8:0101::1/64
 tunnel source GigabitEthernet0/0
 tunnel mode ipv6ip 6to4
```

---

## ✅ Verification
```ios
show ipv6 interface brief
show ipv6 route
show ipv6 ospf neighbor
```

```bash
# From PC
ping6 -c 4 2001:db8:3::10
traceroute6 2001:db8:3::10
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![IPv6 Routes](imgs/ipv6_routes.png)

---

## 📝 Summary
IPv6 provides a vastly larger address space. Dual-stack and tunneling mechanisms ease the transition from IPv4.
