# 🖧 Lab 16: DNS Server (BIND9)

> Set up a BIND9 DNS server on Linux with forward and reverse zones, and configure A records for lab devices.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 3 - Network Services |
| **Level** | ⭐⭐ Beginner-Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Install and configure BIND9 on Ubuntu/Linux
- ✅ Create a forward lookup zone (lab.local)
- ✅ Create a reverse lookup zone (PTR records)
- ✅ Add A records for routers, switches, and servers
- ✅ Test DNS resolution from network clients

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| DHCP Configuration | Lab 15 - DHCP |
| Linux Basics | Phase 7 - Linux Networking |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [DNS Server 192.168.1.53]---[Switch]---[Router]---[Clients]
  Ubuntu Linux / BIND9
  Domain: lab.local
  Zone: 192.168.1.0/24
```

---

## 🛠️ Configuration

### Install BIND9
```bash
apt-get update
apt-get install bind9 bind9utils bind9-doc -y
```

### /etc/bind/named.conf.local
```bash
zone "lab.local" {
    type master;
    file "/etc/bind/zones/db.lab.local";
};

zone "1.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.192.168.1";
};
```

### /etc/bind/zones/db.lab.local (Forward Zone)
```bash
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                         2026030101   ; Serial
                         604800       ; Refresh
                         86400        ; Retry
                         2419200      ; Expire
                         604800 )     ; Negative Cache TTL

@       IN      NS      ns1.lab.local.
ns1     IN      A       192.168.1.53
router1 IN      A       192.168.1.1
router2 IN      A       192.168.1.2
server1 IN      A       192.168.1.100
```

### /etc/bind/zones/db.192.168.1 (Reverse Zone)
```bash
$TTL    604800
@       IN      SOA     ns1.lab.local. admin.lab.local. (
                         2026030101 604800 86400 2419200 604800 )
@       IN      NS      ns1.lab.local.
53      IN      PTR     ns1.lab.local.
1       IN      PTR     router1.lab.local.
100     IN      PTR     server1.lab.local.
```

---

## ✅ Verification
```bash
named-checkconf
named-checkzone lab.local /etc/bind/zones/db.lab.local
systemctl restart bind9
systemctl status bind9
nslookup router1.lab.local 192.168.1.53
dig router1.lab.local @192.168.1.53
dig -x 192.168.1.1 @192.168.1.53
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab deploys BIND9 on Linux to serve forward and reverse DNS zones for the lab.local domain, providing hostname resolution for all network devices.
