# 🖧 Lab 26: GRE Tunnel

> Configure a GRE tunnel between two routers, assign tunnel interface IPs, and route traffic through the tunnel.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 5 - VPN |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Configure GRE tunnel source and destination addresses
- ✅ Assign IP addresses to tunnel interfaces
- ✅ Route LAN traffic over the GRE tunnel
- ✅ Understand GRE encapsulation and its limitations
- ✅ Verify tunnel up/down state and end-to-end reachability

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| Static Routing | Lab 07 - Static Routing |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [LAN1 192.168.1.0/24]---[R1]===GRE Tunnel===[ R2]---[LAN2 192.168.2.0/24]
                          G0/1  Tunnel0  Tunnel0  G0/1
                        10.0.12.1           10.0.12.2
                    Tunnel IP: 172.16.0.1   172.16.0.2
```

---

## 🛠️ Configuration

### R1 - GRE Tunnel
```ios
interface Tunnel0
 ip address 172.16.0.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 10.0.12.2
 keepalive 10 3
 no shutdown

! Route LAN2 traffic over tunnel
ip route 192.168.2.0 255.255.255.0 172.16.0.2
```

### R2 - GRE Tunnel
```ios
interface Tunnel0
 ip address 172.16.0.2 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 10.0.12.1
 keepalive 10 3
 no shutdown

! Route LAN1 traffic over tunnel
ip route 192.168.1.0 255.255.255.0 172.16.0.1
```

---

## ✅ Verification
```ios
show interface Tunnel0
show ip route
ping 172.16.0.2 source GigabitEthernet0/0
ping 192.168.2.1 source 192.168.1.1
traceroute 192.168.2.1
show interfaces Tunnel0 counters
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab establishes a basic GRE tunnel between two routers to encapsulate LAN traffic and route it over an IP WAN, providing a simple VPN transport mechanism.
