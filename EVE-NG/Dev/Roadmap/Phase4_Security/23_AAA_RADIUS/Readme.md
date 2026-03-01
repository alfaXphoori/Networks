# 🖧 Lab 23: AAA & RADIUS

> Configure AAA (Authentication, Authorization, Accounting) with local fallback and RADIUS server for console, VTY, and SSH access.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 4 - Security |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure local user database as authentication fallback
- ✅ Set up FreeRADIUS server on Linux
- ✅ Configure AAA on Cisco router with RADIUS primary
- ✅ Apply AAA to console, VTY lines, and SSH
- ✅ Verify authentication with RADIUS and local fallback

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Switch/Router | Lab 02, 03 |
| ACL Basic | Lab 20 - ACL Basic |
| Linux Basics | Phase 7 - Linux Networking |

---

## 🗺️ Lab Topology
```
  [Admin PC]---[Management Switch]---[RADIUS Server 192.168.1.10]
                       |
               [R1, R2, SW1, SW2]
               (AAA clients)
  Management: 192.168.1.0/24
```

---

## 🛠️ Configuration

### FreeRADIUS Server (Linux)
```bash
apt-get install freeradius -y

# /etc/freeradius/3.0/clients.conf
client cisco_devices {
    ipaddr = 192.168.1.0/24
    secret = RadiusSecret123
    shortname = lab-network
}

# /etc/freeradius/3.0/users
netadmin Cleartext-Password := "Admin@Pass"
         Service-Type = NAS-Prompt-User,
         Cisco-AVPair = "shell:priv-lvl=15"

systemctl restart freeradius
```

### Cisco Router - Local Users
```ios
username admin privilege 15 secret LocalAdmin123
username readonly privilege 1 secret ReadOnly123
```

### Cisco Router - AAA Configuration
```ios
aaa new-model
aaa authentication login default group radius local
aaa authentication login CONSOLE local
aaa authorization exec default group radius local
aaa accounting exec default start-stop group radius

radius server RADIUS_SERVER
 address ipv4 192.168.1.10 auth-port 1812 acct-port 1813
 key RadiusSecret123
```

### Apply AAA to Lines
```ios
line console 0
 login authentication CONSOLE

line vty 0 15
 login authentication default
 transport input ssh

ip ssh version 2
crypto key generate rsa modulus 2048
```

---

## ✅ Verification
```ios
show aaa servers
show aaa local user lockout
show users
debug aaa authentication
debug radius
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab implements centralized AAA using FreeRADIUS for network device authentication with local database fallback for console access and SSH on VTY lines.
