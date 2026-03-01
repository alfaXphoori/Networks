# 🖧 Lab 15: DHCP Configuration

> Configure a Cisco router as a local DHCP server, set excluded addresses, and use ip helper-address for DHCP relay across subnets.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 3 - Network Services |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 1-2 hours |

---

## 🎯 Lab Objectives
- ✅ Configure DHCP pool on a router for local subnet clients
- ✅ Exclude static IP addresses from the DHCP pool
- ✅ Assign default-gateway, DNS, and domain options
- ✅ Configure DHCP relay (ip helper-address) for remote subnets
- ✅ Verify DHCP leases and relay operation

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| Inter-VLAN Routing | Lab 04 - Inter-VLAN Routing |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [PC1/PC2]---[Switch]---[Router G0/0 192.168.1.1]
                                    |
                             [Router G0/1 10.0.1.1]---[Remote Subnet]
                                    |                  [PC3/PC4 need DHCP relay]
                             [DHCP Server]
```

---

## 🛠️ Configuration

### Router - DHCP Pool (Local Subnet)
```ios
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp excluded-address 192.168.1.254

ip dhcp pool LAN_POOL
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name lab.local
 lease 1
```

### Router - DHCP Pool (Remote Subnet)
```ios
ip dhcp excluded-address 10.0.1.1 10.0.1.10

ip dhcp pool REMOTE_POOL
 network 10.0.1.0 255.255.255.0
 default-router 10.0.1.1
 dns-server 8.8.8.8
 lease 1
```

### DHCP Relay (on remote subnet interface)
```ios
interface GigabitEthernet0/1
 ip address 10.0.1.1 255.255.255.0
 ip helper-address 192.168.1.1
 no shutdown
```

---

## ✅ Verification
```ios
show ip dhcp pool
show ip dhcp binding
show ip dhcp conflict
show ip dhcp statistics
debug ip dhcp server events
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures a Cisco router as a DHCP server for local clients with excluded addresses, and demonstrates DHCP relay using ip helper-address to serve clients on remote subnets.
