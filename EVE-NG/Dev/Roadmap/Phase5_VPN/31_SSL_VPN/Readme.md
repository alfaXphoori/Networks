# 🖧 Lab 31: SSL VPN (WebVPN)

> Configure SSL WebVPN on Cisco ASAv, set up AnyConnect client access, and configure split tunneling for remote users.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 5 - VPN |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 3-4 hours |

---

## 🎯 Lab Objectives
- ✅ Configure SSL VPN (WebVPN) on Cisco ASAv
- ✅ Create AnyConnect VPN profile and user group
- ✅ Configure IP address pool for remote clients
- ✅ Enable split tunneling to route only corporate traffic through VPN
- ✅ Test AnyConnect client connection and verify tunnel

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| ACL Basic | Lab 20 - ACL Basic |
| NAT/PAT | Lab 14 - NAT PAT |
| AAA RADIUS | Lab 23 - AAA RADIUS |

---

## 🗺️ Lab Topology
```
  [Remote User]---[Internet]---[ASAv 203.0.113.1]---[Inside 192.168.1.0/24]
  AnyConnect Client              outside: G0/0         inside: G0/1
  Gets VPN IP: 10.0.100.x        SSL/TLS Port 443
```

---

## 🛠️ Configuration

### ASAv - Basic Setup
```ios
interface GigabitEthernet0/0
 nameif outside
 security-level 0
 ip address 203.0.113.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 nameif inside
 security-level 100
 ip address 192.168.1.1 255.255.255.0
 no shutdown
```

### SSL VPN Configuration
```ios
! Enable WebVPN
webvpn
 enable outside
 anyconnect image disk0:/anyconnect-win-4.10.pkg 1
 anyconnect enable
 tunnel-group-list enable

! IP Pool for VPN clients
ip local pool VPN_POOL 10.0.100.10-10.0.100.50 mask 255.255.255.0

! Split tunnel ACL
access-list SPLIT_TUNNEL standard permit 192.168.1.0 255.255.255.0
access-list SPLIT_TUNNEL standard permit 10.0.0.0 255.0.0.0

! Group Policy
group-policy GP_ANYCONNECT internal
group-policy GP_ANYCONNECT attributes
 vpn-tunnel-protocol ssl-client
 split-tunnel-policy tunnelspecified
 split-tunnel-network-list value SPLIT_TUNNEL
 dns-server value 192.168.1.53

! Tunnel Group
tunnel-group ANYCONNECT_GROUP type remote-access
tunnel-group ANYCONNECT_GROUP general-attributes
 address-pool VPN_POOL
 default-group-policy GP_ANYCONNECT

tunnel-group ANYCONNECT_GROUP webvpn-attributes
 group-alias "Corporate VPN" enable

! Local User
username vpnuser password VPNpass123 privilege 0
username vpnuser attributes
 vpn-group-policy GP_ANYCONNECT
```

---

## ✅ Verification
```ios
show vpn-sessiondb anyconnect
show vpn-sessiondb summary
show webvpn enable
show run tunnel-group
show ip local pool VPN_POOL
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab configures SSL WebVPN with Cisco AnyConnect on ASAv, including split tunneling so only corporate subnet traffic traverses the VPN tunnel while internet traffic exits locally.
