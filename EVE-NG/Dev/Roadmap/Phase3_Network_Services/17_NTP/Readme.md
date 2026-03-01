# 🖧 Lab 17: NTP Configuration

> Configure NTP server and client on Cisco routers, understand stratum levels, and verify clock synchronization across the network.

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
- ✅ Configure a Cisco router as an NTP master/server
- ✅ Configure NTP clients to synchronize with the server
- ✅ Understand and verify stratum levels
- ✅ Configure NTP authentication for security
- ✅ Verify clock synchronization on all devices

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| IP Addressing | Lab 01 - Basic PC Cable |

---

## 🗺️ Lab Topology
```
  [NTP Master R1]---[R2 NTP Client]---[R3 NTP Client]
  Stratum 1          Stratum 2          Stratum 3
  10.0.12.1          10.0.12.2          10.0.23.2
                     10.0.23.1
```

---

## 🛠️ Configuration

### R1 - NTP Master Server
```ios
clock timezone UTC 0
clock set 12:00:00 1 March 2026
ntp master 1
ntp authenticate
ntp authentication-key 1 md5 NTPsecret
ntp trusted-key 1
```

### R2 - NTP Client
```ios
clock timezone UTC 0
ntp authenticate
ntp authentication-key 1 md5 NTPsecret
ntp trusted-key 1
ntp server 10.0.12.1 key 1
```

### R3 - NTP Client
```ios
clock timezone UTC 0
ntp server 10.0.23.1
```

### NTP Access Control
```ios
ntp access-group peer 10
access-list 10 permit 10.0.0.0 0.0.255.255
```

---

## ✅ Verification
```ios
show ntp status
show ntp associations
show clock detail
show ntp associations detail
debug ntp events
debug ntp packets
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures NTP hierarchy with R1 as stratum 1 master and downstream clients synchronizing their clocks, with NTP authentication enabled for security.
