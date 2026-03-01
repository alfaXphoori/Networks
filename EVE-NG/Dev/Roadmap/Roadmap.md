# 🗺️ Network Labs Roadmap (EVE-NG Complete Guide)

> Complete network learning roadmap for EVE-NG lab environment, covering from fundamentals to modern network architecture.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Table of Contents

1. [Learning Path](#learning-path)
2. [Phase 1: Foundation](#phase-1-foundation)
3. [Phase 2: Routing Protocols](#phase-2-routing-protocols)
4. [Phase 3: Network Services](#phase-3-network-services)
5. [Phase 4: Security](#phase-4-security)
6. [Phase 5: VPN](#phase-5-vpn)
7. [Phase 6: Advanced Switching](#phase-6-advanced-switching)
8. [Phase 7: Linux Networking](#phase-7-linux-networking)
9. [Phase 8: Network Automation](#phase-8-network-automation)
10. [Phase 9: Modern Network](#phase-9-modern-network)
11. [Required Devices](#required-devices)
12. [EVE-NG Server Requirements](#eve-ng-server-requirements)

---

## 🎯 Learning Path

```
🔰 Network Engineer
Phase 1 → Phase 2 → Phase 3 → Phase 4

🔐 Security Engineer
Phase 1 → Phase 2 → Phase 4 → Phase 5

🤖 Network Automation
Phase 1 → Phase 2 → Phase 7 → Phase 8

🚀 Senior/Architect
Phase 1 → ... → Phase 9
```

---

## 📦 Phase 1: Foundation (พื้นฐาน)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 01 | Basic PC & Cable | การเชื่อมต่อ PC และสาย | ⭐ | ✅ Done |
| 02 | Basic Switch | VLAN, Trunk, Access Port | ⭐ | ✅ Done |
| 03 | Basic Router | Interface, Static Route | ⭐ | ✅ Done |
| 04 | Inter-VLAN Routing | Router-on-a-Stick | ⭐⭐ | ✅ Done |
| 05 | STP & RSTP | Spanning Tree Protocol | ⭐⭐ | ✅ Done |
| 06 | EtherChannel | LACP, PAgP Port Bundling | ⭐⭐ | ✅ Done |

---

## 📦 Phase 2: Routing Protocols (โปรโตคอลการ Routing)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 07 | Static Routing | Static + Default Route | ⭐⭐ | ✅ Done |
| 08 | RIP v2 | Distance Vector Routing | ⭐⭐ | ✅ Done |
| 09 | EIGRP | Cisco Advanced Routing | ⭐⭐⭐ | ✅ Done |
| 10 | OSPF Single Area | Link State Routing | ⭐⭐⭐ | ✅ Done |
| 11 | OSPF Multi-Area | ABR, ASBR, LSA Types | ⭐⭐⭐ | ✅ Done |
| 12 | BGP Basic | eBGP, iBGP, AS Path | ⭐⭐⭐⭐ | ✅ Done |
| 13 | Route Redistribution | OSPF ↔ EIGRP ↔ BGP | ⭐⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 3: Network Services (บริการเครือข่าย)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 14 | NAT & PAT | Static NAT, Dynamic NAT, PAT | ⭐⭐⭐ | ✅ Done |
| 15 | DHCP | Local DHCP, Relay Agent | ⭐⭐⭐ | ✅ Done |
| 16 | DNS | BIND9, Forward/Reverse Zone | ⭐⭐⭐ | ✅ Done |
| 17 | NTP | Time Synchronization Server | ⭐⭐ | 🔲 Todo |
| 18 | Syslog & SNMP | Monitoring & Logging | ⭐⭐⭐ | 🔲 Todo |
| 19 | FTP / TFTP | File Transfer Server | ⭐⭐ | 🔲 Todo |

---

## 📦 Phase 4: Security (ความปลอดภัย)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 20 | ACL Basic | Standard & Extended ACL | ⭐⭐⭐ | 🔲 Todo |
| 21 | ACL Advanced | Named ACL, Time-based ACL | ⭐⭐⭐ | 🔲 Todo |
| 22 | Port Security | MAC Flooding Prevention | ⭐⭐⭐ | 🔲 Todo |
| 23 | AAA & RADIUS | Authentication Server | ⭐⭐⭐⭐ | 🔲 Todo |
| 24 | Zone-Based Firewall | ZBF Policy, Inspect | ⭐⭐⭐⭐ | 🔲 Todo |
| 25 | DHCP Snooping & DAI | Layer 2 Security | ⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 5: VPN (เครือข่าย VPN)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 26 | GRE Tunnel | Generic Routing Encapsulation | ⭐⭐⭐ | 🔲 Todo |
| 27 | Hub-and-Spoke VPN | GRE + OSPF + NAT Exemption | ⭐⭐⭐⭐ | ✅ Done |
| 28 | IPsec Site-to-Site | IKEv1/v2, Transform Set | ⭐⭐⭐⭐ | 🔲 Todo |
| 29 | GRE over IPsec | Encrypted Tunnel | ⭐⭐⭐⭐ | 🔲 Todo |
| 30 | DMVPN Phase 1-3 | Dynamic Multipoint VPN | ⭐⭐⭐⭐⭐ | 🔲 Todo |
| 31 | SSL VPN (WebVPN) | Remote Access VPN | ⭐⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 6: Advanced Switching (สวิตช์ขั้นสูง)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 32 | VLAN Advanced | Private VLAN, Voice VLAN | ⭐⭐⭐ | 🔲 Todo |
| 33 | VTP | VLAN Trunking Protocol | ⭐⭐⭐ | 🔲 Todo |
| 34 | HSRP / VRRP | Gateway Redundancy | ⭐⭐⭐⭐ | 🔲 Todo |
| 35 | MSTP | Multiple Spanning Tree | ⭐⭐⭐⭐ | 🔲 Todo |
| 36 | QoS Basic | Traffic Shaping, Policing | ⭐⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 7: Linux Networking (Linux บนเครือข่าย)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 37 | Linux Basic Network | IP, Route, iptables | ⭐⭐ | 🔲 Todo |
| 38 | Linux as Router | IP Forwarding, NAT | ⭐⭐⭐ | 🔲 Todo |
| 39 | Linux DHCP Server | ISC-DHCP, Failover | ⭐⭐⭐ | 🔲 Todo |
| 40 | Linux DNS Server | BIND9, Split DNS | ⭐⭐⭐ | 🔲 Todo |
| 41 | Linux Web Server | Apache, Nginx, SSL/TLS | ⭐⭐⭐ | 🔲 Todo |
| 42 | Linux VPN Server | OpenVPN, WireGuard | ⭐⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 8: Network Automation (ระบบอัตโนมัติ)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 43 | Python Netmiko | SSH Automation | ⭐⭐⭐⭐ | 🔲 Todo |
| 44 | Python NAPALM | Multi-vendor Automation | ⭐⭐⭐⭐ | 🔲 Todo |
| 45 | Ansible Network | Playbook for Cisco/Linux | ⭐⭐⭐⭐ | 🔲 Todo |
| 46 | Terraform Network | Infrastructure as Code | ⭐⭐⭐⭐⭐ | 🔲 Todo |
| 47 | NetBox | Network Documentation | ⭐⭐⭐ | 🔲 Todo |

---

## 📦 Phase 9: Modern Network (เครือข่ายสมัยใหม่)

| Lab | ชื่อ | เนื้อหาหลัก | ระดับ | Status |
|-----|------|------------|-------|--------|
| 48 | SD-WAN Basic | vEdge, vManage, vSmart | ⭐⭐⭐⭐⭐ | 🔲 Todo |
| 49 | SDN & OpenFlow | OpenDaylight Controller | ⭐⭐⭐⭐⭐ | 🔲 Todo |
| 50 | IPv6 Complete | Dual Stack, Tunneling | ⭐⭐⭐⭐ | 🔲 Todo |
| 51 | MPLS Basic | Label Switching, VRF | ⭐⭐⭐⭐⭐ | 🔲 Todo |
| 52 | Network Monitoring | Zabbix, Grafana, InfluxDB | ⭐⭐⭐⭐ | 🔲 Todo |

---

## 🖥️ Required Devices

### 🔴 Cisco Images

| Image | ใช้สำหรับ | Phase ที่ใช้ | Download |
|-------|----------|------------|---------|
| **IOSv** | Router L3 | Phase 1-6 | Cisco DevNet |
| **IOSvL2** | Switch L2/L3 | Phase 1, 6 | Cisco DevNet |
| **CSR1000v** | Router Advanced, BGP, MPLS | Phase 2, 9 | Cisco DevNet |
| **ASAv** | Firewall, VPN, ZBF | Phase 4, 5 | Cisco DevNet |
| **vWLC** | Wireless LAN Controller | Phase 6 | Cisco DevNet |

### 🟡 Linux Images

| Image | ใช้สำหรับ | Phase ที่ใช้ | Download |
|-------|----------|------------|---------|
| **Ubuntu Server** | DHCP, DNS, Web, VPN Server | Phase 3, 7 | ubuntu.com |
| **Slax / Tiny Linux** | PC Client, Testing | Phase 1-9 | slax.org |
| **Kali Linux** | Security Testing | Phase 4 | kali.org |

### 🟢 Open Source / Other

| Image | ใช้สำหรับ | Phase ที่ใช้ | Download |
|-------|----------|------------|---------|
| **VyOS** | Linux Router, NAT, BGP | Phase 2, 7 | vyos.io |
| **pfSense** | Firewall, VPN | Phase 4, 5 | pfsense.org |
| **OpenWRT** | Wireless AP Simulation | Phase 6 | openwrt.org |

### 🔵 Automation & Monitoring

| Tool | ใช้สำหรับ | Phase ที่ใช้ | Download |
|------|----------|------------|---------|
| **Ansible Controller** | Network Automation | Phase 8 | ubuntu.com |
| **Python Host** | Netmiko, NAPALM | Phase 8 | ubuntu.com |
| **Zabbix Server** | Network Monitoring | Phase 9 | zabbix.com |
| **Grafana + InfluxDB** | Dashboard & Metrics | Phase 9 | grafana.com |
| **NetBox** | Network Documentation | Phase 8 | netbox.dev |

---

## ⚙️ EVE-NG Server Requirements

| Spec | Minimum | แนะนำ | Phase 8-9 |
|------|---------|-------|-----------|
| **CPU** | 4 Core | 8 Core | 16 Core |
| **RAM** | 16 GB | 32 GB | 64 GB |
| **Storage** | 100 GB | 250 GB | 500 GB |
| **Network** | 1 NIC | 2 NIC | 2 NIC |
| **EVE-NG Version** | Community | Community | Pro |

### 🎯 Minimum Setup สำหรับมือใหม่ (Phase 1-7)

```
✅ IOSv           → Router
✅ IOSvL2         → Switch
✅ Ubuntu Server  → Server (DHCP/DNS/Web)
✅ Slax Linux     → PC Client
```

### 📊 สรุป Images ทั้งหมด

```
Cisco Images    : 5 images  (ต้องใช้ Cisco Account)
Linux Images    : 3 images  (ฟรี)
Other Images    : 3 images  (ฟรี / Open Source)
Tool Images     : 5 images  (ฟรี / Open Source)
─────────────────────────────────────────────────
รวมทั้งหมด      : 16 images
```

> 💡 **Tip:** EVE-NG Community Edition รองรับได้ทุก Image ยกเว้น vWLC และ SD-WAN ที่ต้องการ EVE-NG Pro ครับ!

---

> **Last Updated:** March 2026 | **Total Labs:** 52 Labs | **Total Phases:** 9 Phases