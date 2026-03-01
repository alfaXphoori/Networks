# 🖧 Lab 28: IPsec Site-to-Site VPN

> Configure IPsec Site-to-Site VPN using IKEv1 Phase 1/Phase 2, transform sets, crypto maps, and verify IPsec Security Associations.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 5 - VPN |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 2-3 hours |

---

## 🎯 Lab Objectives
- ✅ Configure IKEv1 Phase 1 (ISAKMP policy) parameters
- ✅ Configure IKEv1 Phase 2 (transform set, IPsec profile)
- ✅ Define interesting traffic with crypto ACL
- ✅ Apply crypto map to WAN interface
- ✅ Verify IPsec SA establishment and encrypted traffic

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Basic Routing | Lab 08 - Basic Routing |
| ACL Basic | Lab 20 - ACL Basic |
| NAT/PAT | Lab 14 - NAT PAT |

---

## 🗺️ Lab Topology
```
  [LAN1 192.168.1.0/24]---[R1 203.0.113.1]===IPsec===[R2 203.0.113.2]---[LAN2 192.168.2.0/24]
  
  IKE Phase 1: AES-256, SHA, DH Group 14, PSK
  IKE Phase 2: AES-256, SHA-HMAC, ESP
```

---

## 🛠️ Configuration

### R1 - IKEv1 Phase 1 (ISAKMP)
```ios
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14
 lifetime 86400

crypto isakmp key VPNsecret123 address 203.0.113.2
```

### R1 - IKEv1 Phase 2 (Transform Set + Crypto Map)
```ios
crypto ipsec transform-set TS_AES256 esp-aes 256 esp-sha256-hmac
 mode tunnel

ip access-list extended VPN_TRAFFIC
 permit ip 192.168.1.0 0.0.0.255 192.168.2.0 0.0.0.255

crypto map SITE_TO_SITE 10 ipsec-isakmp
 set peer 203.0.113.2
 set transform-set TS_AES256
 set pfs group14
 match address VPN_TRAFFIC

interface GigabitEthernet0/1
 crypto map SITE_TO_SITE
```

### R2 - Mirror Configuration
```ios
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14

crypto isakmp key VPNsecret123 address 203.0.113.1

crypto ipsec transform-set TS_AES256 esp-aes 256 esp-sha256-hmac
 mode tunnel

ip access-list extended VPN_TRAFFIC
 permit ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255

crypto map SITE_TO_SITE 10 ipsec-isakmp
 set peer 203.0.113.1
 set transform-set TS_AES256
 match address VPN_TRAFFIC

interface GigabitEthernet0/1
 crypto map SITE_TO_SITE
```

---

## ✅ Verification
```ios
show crypto isakmp sa
show crypto ipsec sa
show crypto map
ping 192.168.2.1 source 192.168.1.1
debug crypto isakmp
debug crypto ipsec
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab establishes an IPsec Site-to-Site VPN using IKEv1 with AES-256 encryption and SHA-256 authentication, securing traffic between two remote LANs over an untrusted WAN.
