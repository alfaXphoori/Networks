# 🖧 Lab 48: SD-WAN Basic (Cisco vEdge & vManage)

> Deploy and configure a basic Cisco SD-WAN fabric with vEdge, vManage, vSmart, and vBond.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 9 - Modern Network |
| **Level** | ⭐⭐⭐⭐⭐ Expert |
| **Status** | 🔲 Todo |
| **Est. Time** | 6-8 hours |
| **EVE-NG** | Pro Edition Required |

---

## 🎯 Lab Objectives
- ✅ Deploy SD-WAN controller plane (vManage, vSmart, vBond)
- ✅ Onboard vEdge routers to the fabric
- ✅ Configure OMP (Overlay Management Protocol)
- ✅ Create and apply SD-WAN policies
- ✅ Monitor tunnel health and traffic statistics

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| BGP Basic | Lab 12 |
| IPsec VPN | Lab 28 |
| Ansible Network | Lab 45 |

---

## 🗺️ Lab Topology
```
            [vManage NMS]
                 |
            [vBond Orchestrator]
                 |
            [vSmart Controller]
           /                   \
    [vEdge-1]               [vEdge-2]
    Site 1                   Site 2
    [PC1]                    [PC2]
```

---

## 🛠️ Configuration

### vManage Initial Setup
```bash
# On vManage CLI
system
 host-name vManage
 system-ip 1.1.1.1
 site-id 100
 organization-name "Lab-SD-WAN"
 vbond 192.168.1.2
```

### vEdge Onboarding
```bash
# On vEdge CLI
system
 host-name vEdge-1
 system-ip 10.10.1.1
 site-id 1
 organization-name "Lab-SD-WAN"
 vbond 192.168.1.2

interface ge0/0
 ip address 192.168.10.1/24
 no shutdown
```

### OMP Policy (via vManage GUI)
```
1. Configuration → Templates → Feature Template
2. Create VPN 0 (Transport) and VPN 1 (Service) templates
3. Attach templates to vEdge devices
4. Configure centralized policy for traffic steering
```

---

## ✅ Verification
```bash
show sdwan control connections
show sdwan bfd sessions
show sdwan omp routes
show sdwan tunnel statistics
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![vManage Dashboard](imgs/vmanage_dashboard.png)
![SD-WAN Tunnels](imgs/sdwan_tunnels.png)

---

## 📝 Summary
Cisco SD-WAN provides centralized policy management, transport independence, and application-aware routing across WAN connections.
