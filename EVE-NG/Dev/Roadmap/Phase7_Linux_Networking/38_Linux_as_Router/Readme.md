# 🖧 Lab 38: Linux as Router (IP Forwarding & NAT)

> Configure a Linux host as a network router with IP forwarding and NAT masquerade.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 7 - Linux Networking |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Enable IP forwarding on Linux
- ✅ Configure NAT masquerade with iptables
- ✅ Set up static routes between networks
- ✅ Verify inter-network connectivity through Linux router

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux Basic Network | Lab 37 |

---

## 🗺️ Lab Topology
```
[PC1 LAN]──[eth1: Linux Router :eth0]──[Internet/WAN]
192.168.10.0/24                         DHCP
```

---

## 🛠️ Configuration

### Enable IP Forwarding
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
# Permanent:
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p
```

### NAT Masquerade
```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT
```

### Static Routes Between Networks
```bash
ip route add 192.168.20.0/24 via 192.168.10.254
```

---

## ✅ Verification
```bash
cat /proc/sys/net/ipv4/ip_forward
iptables -t nat -L -v -n
ip route show
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
Linux can act as a full-featured router using built-in kernel IP forwarding and iptables NAT.
