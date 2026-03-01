# 🗺️ EVE-NG Network Labs — Master Index

> Complete EVE-NG network lab curriculum from installation to modern networking. 52 labs across 10 phases.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📊 Overview

| Phase | Topic | Labs | Status |
|-------|-------|------|--------|
| [Phase 0](#-phase-0--installation--setup) | Installation & Setup | 4 | ✅ Done |
| [Phase 1](#-phase-1--foundation) | Foundation | 6 | ✅ Done |
| [Phase 2](#-phase-2--routing-protocols) | Routing Protocols | 7 | ✅ Done (Lab 13 Todo) |
| [Phase 3](#-phase-3--network-services) | Network Services | 6 | ✅ Done (Lab 17-19 Todo) |
| [Phase 4](#-phase-4--security) | Security | 6 | 🔲 Todo |
| [Phase 5](#-phase-5--vpn) | VPN | 6 | ✅ Lab 27 Done |
| [Phase 6](#-phase-6--advanced-switching) | Advanced Switching | 5 | 🔲 Todo |
| [Phase 7](#-phase-7--linux-networking) | Linux Networking | 6 | 🔲 Todo |
| [Phase 8](#-phase-8--network-automation) | Network Automation | 5 | 🔲 Todo |
| [Phase 9](#-phase-9--modern-network) | Modern Network | 5 | 🔲 Todo |
| **Total** | | **56 Labs** | |

📄 Full roadmap details → [Roadmap.md](Roadmap.md)

---

## 🏗️ Phase 0 — Installation & Setup

📁 [Phase0_Install/](Phase0_Install/)

| Lab | Topic | Status |
|-----|-------|--------|
| [00 - EVE-NG Installation](Phase0_Install/00_EVE_NG_Install/Readme.md) | Install EVE-NG on VMware | ✅ Done |
| [01 - Web GUI & First Lab](Phase0_Install/01_Web_GUI_First_Lab/Readme.md) | Navigate web interface, create first lab | ✅ Done |
| [02 - Add Router & Switch](Phase0_Install/02_Add_Router_Switch/Readme.md) | Upload Cisco images via SFTP | ✅ Done |
| [03 - Switch Basic Config](Phase0_Install/03_Switch_Basic_Config/Readme.md) | Hostname, passwords, SSH setup | ✅ Done |

---

## 🔌 Phase 1 — Foundation

📁 [Phase1_Foundation/](Phase1_Foundation/)

| Lab | Topic | Status |
|-----|-------|--------|
| [01 - Basic PC & Cable](Phase1_Foundation/01_Basic_PC_Cable/Readme.md) | PC connection, Ethernet cables | ✅ Done |
| [02 - Basic Switch](Phase1_Foundation/02_Basic_Switch/Readme.md) | VLAN, Trunk, Access Port | ✅ Done |
| [03 - Basic Router](Phase1_Foundation/03_Basic_Router/Readme.md) | Interface, Static Route | ✅ Done |
| [04 - Inter-VLAN Routing](Phase1_Foundation/04_Inter_VLAN_Routing/Readme.md) | Router-on-a-Stick | ✅ Done |
| [05 - STP & RSTP](Phase1_Foundation/05_STP_RSTP/Readme.md) | Spanning Tree Protocol | ✅ Done |
| [06 - EtherChannel](Phase1_Foundation/06_EtherChannel/Readme.md) | LACP, PAgP Port Bundling | ✅ Done |

---

## 🔀 Phase 2 — Routing Protocols

📁 [Phase2_Routing_Protocols/](Phase2_Routing_Protocols/)

| Lab | Topic | Status |
|-----|-------|--------|
| [07 - Static Routing](Phase2_Routing_Protocols/07_Static_Routing/Readme.md) | Static + Default Route | ✅ Done |
| [08 - RIP v2](Phase2_Routing_Protocols/08_RIP_v2/Readme.md) | Distance Vector Routing | ✅ Done |
| [09 - EIGRP](Phase2_Routing_Protocols/09_EIGRP/Readme.md) | Cisco Advanced Routing | ✅ Done |
| [10 - OSPF Single Area](Phase2_Routing_Protocols/10_OSPF_Single_Area/Readme.md) | Link State Routing | ✅ Done |
| [11 - OSPF Multi-Area](Phase2_Routing_Protocols/11_OSPF_Multi_Area/Readme.md) | ABR, ASBR, LSA Types | ✅ Done |
| [12 - BGP Basic](Phase2_Routing_Protocols/12_BGP_Basic/Readme.md) | eBGP, iBGP, AS Path | ✅ Done |
| [13 - Route Redistribution](Phase2_Routing_Protocols/13_Route_Redistribution/Readme.md) | OSPF ↔ EIGRP ↔ BGP | 🔲 Todo |

---

## 🌐 Phase 3 — Network Services

📁 [Phase3_Network_Services/](Phase3_Network_Services/)

| Lab | Topic | Status |
|-----|-------|--------|
| [14 - NAT & PAT](Phase3_Network_Services/14_NAT_PAT/Readme.md) | Static NAT, Dynamic NAT, PAT | ✅ Done |
| [15 - DHCP](Phase3_Network_Services/15_DHCP/Readme.md) | Local DHCP, Relay Agent | ✅ Done |
| [16 - DNS](Phase3_Network_Services/16_DNS/Readme.md) | BIND9, Forward/Reverse Zone | ✅ Done |
| [17 - NTP](Phase3_Network_Services/17_NTP/Readme.md) | Time Synchronization Server | 🔲 Todo |
| [18 - Syslog & SNMP](Phase3_Network_Services/18_Syslog_SNMP/Readme.md) | Monitoring & Logging | 🔲 Todo |
| [19 - FTP / TFTP](Phase3_Network_Services/19_FTP_TFTP/Readme.md) | File Transfer Server | 🔲 Todo |

---

## 🔒 Phase 4 — Security

📁 [Phase4_Security/](Phase4_Security/)

| Lab | Topic | Status |
|-----|-------|--------|
| [20 - ACL Basic](Phase4_Security/20_ACL_Basic/Readme.md) | Standard & Extended ACL | 🔲 Todo |
| [21 - ACL Advanced](Phase4_Security/21_ACL_Advanced/Readme.md) | Named ACL, Time-based ACL | 🔲 Todo |
| [22 - Port Security](Phase4_Security/22_Port_Security/Readme.md) | MAC Flooding Prevention | 🔲 Todo |
| [23 - AAA & RADIUS](Phase4_Security/23_AAA_RADIUS/Readme.md) | Authentication Server | 🔲 Todo |
| [24 - Zone-Based Firewall](Phase4_Security/24_Zone_Based_Firewall/Readme.md) | ZBF Policy, Inspect | 🔲 Todo |
| [25 - DHCP Snooping & DAI](Phase4_Security/25_DHCP_Snooping_DAI/Readme.md) | Layer 2 Security | 🔲 Todo |

---

## 🔐 Phase 5 — VPN

📁 [Phase5_VPN/](Phase5_VPN/)

| Lab | Topic | Status |
|-----|-------|--------|
| [26 - GRE Tunnel](Phase5_VPN/26_GRE_Tunnel/Readme.md) | Generic Routing Encapsulation | 🔲 Todo |
| [27 - Hub-and-Spoke VPN](Phase5_VPN/27_Hub_and_Spoke_VPN/Readme.md) | GRE + OSPF + NAT Exemption | ✅ Done |
| [28 - IPsec Site-to-Site](Phase5_VPN/28_IPsec_Site_to_Site/Readme.md) | IKEv1/v2, Transform Set | 🔲 Todo |
| [29 - GRE over IPsec](Phase5_VPN/29_GRE_over_IPsec/Readme.md) | Encrypted Tunnel | 🔲 Todo |
| [30 - DMVPN Phase 1-3](Phase5_VPN/30_DMVPN/Readme.md) | Dynamic Multipoint VPN | 🔲 Todo |
| [31 - SSL VPN](Phase5_VPN/31_SSL_VPN/Readme.md) | Remote Access VPN | 🔲 Todo |

---

## 🔄 Phase 6 — Advanced Switching

📁 [Phase6_Advanced_Switching/](Phase6_Advanced_Switching/)

| Lab | Topic | Status |
|-----|-------|--------|
| [32 - VLAN Advanced](Phase6_Advanced_Switching/32_VLAN_Advanced/Readme.md) | Private VLAN, Voice VLAN | 🔲 Todo |
| [33 - VTP](Phase6_Advanced_Switching/33_VTP/Readme.md) | VLAN Trunking Protocol | 🔲 Todo |
| [34 - HSRP / VRRP](Phase6_Advanced_Switching/34_HSRP_VRRP/Readme.md) | Gateway Redundancy | 🔲 Todo |
| [35 - MSTP](Phase6_Advanced_Switching/35_MSTP/Readme.md) | Multiple Spanning Tree | 🔲 Todo |
| [36 - QoS Basic](Phase6_Advanced_Switching/36_QoS_Basic/Readme.md) | Traffic Shaping, Policing | 🔲 Todo |

---

## 🐧 Phase 7 — Linux Networking

📁 [Phase7_Linux_Networking/](Phase7_Linux_Networking/)

| Lab | Topic | Status |
|-----|-------|--------|
| [37 - Linux Basic Network](Phase7_Linux_Networking/37_Linux_Basic_Network/Readme.md) | IP, Route, iptables | 🔲 Todo |
| [38 - Linux as Router](Phase7_Linux_Networking/38_Linux_as_Router/Readme.md) | IP Forwarding, NAT | 🔲 Todo |
| [39 - Linux DHCP Server](Phase7_Linux_Networking/39_Linux_DHCP_Server/Readme.md) | ISC-DHCP, Failover | 🔲 Todo |
| [40 - Linux DNS Server](Phase7_Linux_Networking/40_Linux_DNS_Server/Readme.md) | BIND9, Split DNS | 🔲 Todo |
| [41 - Linux Web Server](Phase7_Linux_Networking/41_Linux_Web_Server/Readme.md) | Apache, Nginx, SSL/TLS | 🔲 Todo |
| [42 - Linux VPN Server](Phase7_Linux_Networking/42_Linux_VPN_Server/Readme.md) | OpenVPN, WireGuard | 🔲 Todo |

---

## 🤖 Phase 8 — Network Automation

📁 [Phase8_Network_Automation/](Phase8_Network_Automation/)

| Lab | Topic | Status |
|-----|-------|--------|
| [43 - Python Netmiko](Phase8_Network_Automation/43_Python_Netmiko/Readme.md) | SSH Automation | 🔲 Todo |
| [44 - Python NAPALM](Phase8_Network_Automation/44_Python_NAPALM/Readme.md) | Multi-vendor Automation | 🔲 Todo |
| [45 - Ansible Network](Phase8_Network_Automation/45_Ansible_Network/Readme.md) | Playbook for Cisco/Linux | 🔲 Todo |
| [46 - Terraform Network](Phase8_Network_Automation/46_Terraform_Network/Readme.md) | Infrastructure as Code | 🔲 Todo |
| [47 - NetBox](Phase8_Network_Automation/47_NetBox/Readme.md) | Network Documentation | 🔲 Todo |

---

## 🚀 Phase 9 — Modern Network

📁 [Phase9_Modern_Network/](Phase9_Modern_Network/)

| Lab | Topic | Status |
|-----|-------|--------|
| [48 - SD-WAN Basic](Phase9_Modern_Network/48_SD_WAN_Basic/Readme.md) | vEdge, vManage, vSmart | 🔲 Todo |
| [49 - SDN & OpenFlow](Phase9_Modern_Network/49_SDN_OpenFlow/Readme.md) | OpenDaylight Controller | 🔲 Todo |
| [50 - IPv6 Complete](Phase9_Modern_Network/50_IPv6_Complete/Readme.md) | Dual Stack, Tunneling | 🔲 Todo |
| [51 - MPLS Basic](Phase9_Modern_Network/51_MPLS_Basic/Readme.md) | Label Switching, VRF | 🔲 Todo |
| [52 - Network Monitoring](Phase9_Modern_Network/52_Network_Monitoring/Readme.md) | Zabbix, Grafana, InfluxDB | 🔲 Todo |

---

## 🎯 Learning Paths

```
🔰 Network Engineer
  Phase 0 → 1 → 2 → 3 → 4

🔐 Security Engineer
  Phase 0 → 1 → 2 → 4 → 5

🤖 Network Automation
  Phase 0 → 1 → 2 → 7 → 8

🚀 Senior / Architect
  Phase 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9
```

---

> **Total:** 56 Labs | **Phases:** 10 | **Updated:** March 2026
