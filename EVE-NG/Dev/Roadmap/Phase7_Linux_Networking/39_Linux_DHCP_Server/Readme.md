# 🖧 Lab 39: Linux DHCP Server (ISC-DHCP & Failover)

> Install and configure ISC-DHCP server with multiple scopes and failover.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 7 - Linux Networking |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Install ISC-DHCP server on Ubuntu
- ✅ Configure DHCP pools for multiple subnets
- ✅ Set up DHCP relay on Cisco router
- ✅ Configure DHCP failover between two servers
- ✅ Verify client IP assignments

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux Basic Network | Lab 37 |
| Basic Router | Lab 03 |

---

## 🗺️ Lab Topology
```
[PC Clients]──[SW]──[Router (relay)]──[SW]──[DHCP Server 192.168.100.10]
                                              [DHCP Failover 192.168.100.11]
```

---

## 🛠️ Configuration

### Install DHCP Server
```bash
apt-get update && apt-get install -y isc-dhcp-server
```

### /etc/dhcp/dhcpd.conf
```bash
default-lease-time 600;
max-lease-time 7200;

subnet 192.168.10.0 netmask 255.255.255.0 {
  range 192.168.10.50 192.168.10.200;
  option routers 192.168.10.1;
  option domain-name-servers 8.8.8.8;
}

subnet 192.168.20.0 netmask 255.255.255.0 {
  range 192.168.20.50 192.168.20.200;
  option routers 192.168.20.1;
  option domain-name-servers 8.8.8.8;
}

# Static binding
host pc1-static {
  hardware ethernet 00:50:79:66:68:00;
  fixed-address 192.168.10.100;
}
```

### DHCP Relay on Cisco Router
```ios
interface GigabitEthernet0/1
 ip helper-address 192.168.100.10
```

---

## ✅ Verification
```bash
systemctl status isc-dhcp-server
cat /var/lib/dhcp/dhcpd.leases
tail -f /var/log/syslog | grep dhcpd
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![DHCP Leases](imgs/dhcp_leases.png)

---

## 📝 Summary
Linux ISC-DHCP provides enterprise-grade DHCP with static bindings, multiple scopes, and failover support.
