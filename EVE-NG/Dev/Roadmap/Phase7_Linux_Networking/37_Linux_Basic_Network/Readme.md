# 🖧 Lab 37: Linux Basic Network (IP, Route, iptables)

> Master basic Linux networking commands for interface configuration and traffic filtering.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 7 - Linux Networking |
| **Level** | ⭐⭐ Beginner |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure network interfaces using ip/ifconfig commands
- ✅ Manage routing table with ip route
- ✅ Configure basic iptables firewall rules
- ✅ Diagnose connectivity with netstat, ss, tcpdump

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Linux | Linux Fundamentals |

---

## 🗺️ Lab Topology
```
[PC1 Linux]──[SW1]──[Linux Host]──[SW2]──[PC2 Linux]
```

---

## 🛠️ Configuration

### Interface Configuration
```bash
# Using ip command (modern)
ip addr add 192.168.10.10/24 dev eth0
ip link set eth0 up

# Using ifconfig (classic)
ifconfig eth0 192.168.10.10 netmask 255.255.255.0 up
```

### Routing
```bash
ip route add 192.168.20.0/24 via 192.168.10.1
ip route add default via 192.168.10.1
route -n
```

### iptables Basic Rules
```bash
# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Drop all other inbound
iptables -A INPUT -j DROP

# View rules
iptables -L -v -n
```

---

## ✅ Verification
```bash
ip addr show
ip route show
netstat -tuln
ss -tuln
ping -c 4 192.168.20.10
tcpdump -i eth0 icmp
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Network Config](imgs/network_config.png)

---

## 📝 Summary
Linux networking tools provide powerful command-line control over interfaces, routing, and firewall rules.
