# 🖧 Lab 29: GRE over IPsec

> Combine GRE tunnels with IPsec encryption to achieve secure routing — leveraging GRE for multi-protocol support and IPsec for encryption.

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
- ✅ Configure GRE tunnel between two sites
- ✅ Protect GRE tunnel traffic with IPsec encryption
- ✅ Use IPsec profile instead of crypto map for tunnel mode
- ✅ Run OSPF or static routing over the encrypted GRE tunnel
- ✅ Verify encryption of GRE-encapsulated routing traffic

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| GRE Tunnel | Lab 26 - GRE Tunnel |
| IPsec Site-to-Site | Lab 28 - IPsec Site-to-Site |
| OSPF Single Area | Lab 10 - OSPF |

---

## 🗺️ Lab Topology
```
  [LAN1 192.168.1.0/24]---[R1]===GRE Tunnel (IPsec Protected)===[ R2]---[LAN2 192.168.2.0/24]
                        203.0.113.1  Tunnel0: 172.16.0.1/30    203.0.113.2
                                     172.16.0.2/30
```

---

## 🛠️ Configuration

### R1 - IKEv1 + IPsec Profile
```ios
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14

crypto isakmp key GRE_IPsec_Key address 203.0.113.2

crypto ipsec transform-set GRE_TS esp-aes 256 esp-sha256-hmac
 mode transport

crypto ipsec profile GRE_PROFILE
 set transform-set GRE_TS
 set pfs group14
```

### R1 - GRE Tunnel with IPsec Profile
```ios
interface Tunnel0
 ip address 172.16.0.1 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 203.0.113.2
 tunnel protection ipsec profile GRE_PROFILE
 keepalive 10 3
 no shutdown

router ospf 1
 network 172.16.0.0 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.255 area 0
```

### R2 - Mirror Configuration
```ios
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14

crypto isakmp key GRE_IPsec_Key address 203.0.113.1

crypto ipsec transform-set GRE_TS esp-aes 256 esp-sha256-hmac
 mode transport

crypto ipsec profile GRE_PROFILE
 set transform-set GRE_TS

interface Tunnel0
 ip address 172.16.0.2 255.255.255.252
 tunnel source GigabitEthernet0/1
 tunnel destination 203.0.113.1
 tunnel protection ipsec profile GRE_PROFILE
 no shutdown

router ospf 1
 network 172.16.0.0 0.0.0.3 area 0
 network 192.168.2.0 0.0.0.255 area 0
```

---

## ✅ Verification
```ios
show interface Tunnel0
show crypto isakmp sa
show crypto ipsec sa
show ip ospf neighbor
show ip route ospf
ping 192.168.2.1 source 192.168.1.1
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)

---

## 📝 Summary
This lab combines GRE tunneling with IPsec transport mode encryption using tunnel protection profiles, enabling secure multi-protocol routing between remote sites.
