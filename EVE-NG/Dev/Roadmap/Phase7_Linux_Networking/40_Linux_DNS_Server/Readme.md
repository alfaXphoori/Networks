# 🖧 Lab 40: Linux DNS Server (BIND9 & Split DNS)

> Install BIND9 DNS server with forward/reverse zones and split DNS configuration.

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
- ✅ Install BIND9 on Ubuntu
- ✅ Configure forward DNS zone (A records)
- ✅ Configure reverse DNS zone (PTR records)
- ✅ Set up split DNS for internal/external resolution
- ✅ Test DNS resolution from clients

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Linux Basic Network | Lab 37 |

---

## 🗺️ Lab Topology
```
[Clients]──[SW]──[Router]──[DNS Server 192.168.100.20]
```

---

## 🛠️ Configuration

### Install BIND9
```bash
apt-get install -y bind9 bind9utils dnsutils
```

### /etc/bind/named.conf.local
```bash
zone "lab.local" {
    type master;
    file "/etc/bind/db.lab.local";
};

zone "168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168";
};
```

### /etc/bind/db.lab.local
```bash
$TTL 604800
@   IN  SOA ns.lab.local. admin.lab.local. (
            2         ; Serial
       604800         ; Refresh
        86400         ; Retry
      2419200         ; Expire
       604800 )       ; Negative Cache TTL
@       IN  NS   ns.lab.local.
ns      IN  A    192.168.100.20
www     IN  A    192.168.20.10
router  IN  A    192.168.10.1
```

---

## ✅ Verification
```bash
systemctl status bind9
named-checkconf
named-checkzone lab.local /etc/bind/db.lab.local
nslookup www.lab.local 192.168.100.20
dig @192.168.100.20 www.lab.local
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![DNS Resolution](imgs/dns_resolution.png)

---

## 📝 Summary
BIND9 provides authoritative DNS with flexible zone configuration for internal network name resolution.
