# 🖧 Lab 18: Syslog & SNMP

> Configure Syslog server logging and SNMP v2c/v3 with community strings, traps, and agent settings on Cisco devices.

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
- ✅ Configure Syslog on a router to send logs to a central server
- ✅ Set logging severity levels and facility types
- ✅ Configure SNMP v2c with community strings (read-only/read-write)
- ✅ Configure SNMP v3 with authentication and privacy
- ✅ Enable SNMP traps for interface state changes and CPU threshold

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| NTP Configuration | Lab 17 - NTP |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [Router/Switch]---[Management Network]---[Syslog/SNMP Server]
  192.168.1.0/24                           192.168.1.200
  Devices: R1, R2, SW1                     Linux: rsyslog + snmpd
```

---

## 🛠️ Configuration

### Syslog Configuration
```ios
logging on
logging host 192.168.1.200
logging trap informational
logging facility local6
logging buffered 16384 debugging
service timestamps log datetime msec localtime show-timezone
```

### SNMP v2c Configuration
```ios
snmp-server community PUBLIC_READ ro
snmp-server community PRIVATE_WRITE rw
snmp-server location "EVE-NG Lab Room"
snmp-server contact "admin@lab.local"
snmp-server trap-source GigabitEthernet0/0
snmp-server enable traps
snmp-server host 192.168.1.200 version 2c PUBLIC_READ
```

### SNMP v3 Configuration
```ios
snmp-server group ADMIN_GROUP v3 priv
snmp-server user snmpv3user ADMIN_GROUP v3 auth sha AuthPass123 priv aes 128 PrivPass456
snmp-server host 192.168.1.200 version 3 priv snmpv3user
```

### SNMP Traps
```ios
snmp-server enable traps snmp linkdown linkup
snmp-server enable traps config
snmp-server enable traps cpu threshold
snmp-server enable traps bgp
```

---

## ✅ Verification
```ios
show logging
show snmp
show snmp user
show snmp group
show snmp community
show snmp host
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures centralized Syslog for event logging and SNMP v2c/v3 for network management, enabling monitoring and alerting from a central management server.
