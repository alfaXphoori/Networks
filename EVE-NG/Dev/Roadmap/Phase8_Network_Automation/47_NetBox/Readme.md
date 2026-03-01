# 🖧 Lab 47: NetBox (Network Documentation & IPAM)

> Deploy NetBox as a network source of truth for IP management and device documentation.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 8 - Network Automation |
| **Level** | ⭐⭐⭐ Intermediate |
| **Status** | 🔲 Todo |
| **Est. Time** | 4-5 hours |

---

## 🎯 Lab Objectives
- ✅ Install NetBox using Docker Compose
- ✅ Add sites, racks, and device types
- ✅ Manage IP prefixes and addresses (IPAM)
- ✅ Use NetBox API for inventory automation
- ✅ Integrate NetBox as Ansible dynamic inventory

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Ansible Network | Lab 45 |
| Linux Web Server | Lab 41 |

---

## 🗺️ Lab Topology
```
[NetBox Server Ubuntu]──[Mgmt SW]──[All Network Devices]
  Docker: netbox + postgres + redis
```

---

## 🛠️ Configuration

### Install NetBox via Docker
```bash
git clone -b release https://github.com/netbox-community/netbox-docker.git
cd netbox-docker
cp env/netbox.env.example env/netbox.env
docker-compose pull
docker-compose up -d
# Default: http://localhost:8000 | admin / admin
```

### NetBox API - Add Device via Python
```python
import requests

url = "http://192.168.10.100:8000/api/dcim/devices/"
headers = {
    "Authorization": "Token abc1234567890",
    "Content-Type": "application/json"
}
payload = {
    "name": "R1",
    "device_type": {"id": 1},
    "site": {"id": 1},
    "status": "active"
}
response = requests.post(url, json=payload, headers=headers)
print(response.json())
```

### Ansible Dynamic Inventory from NetBox
```bash
pip install pynetbox
# netbox_inventory.yml
plugin: netbox.netbox.nb_inventory
api_endpoint: http://192.168.10.100:8000
token: abc1234567890
group_by:
  - site
  - device_role
```

---

## ✅ Verification
```bash
docker-compose ps
curl -H "Authorization: Token abc1234567890" http://localhost:8000/api/dcim/devices/
ansible-inventory -i netbox_inventory.yml --list
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![NetBox Dashboard](imgs/netbox_dashboard.png)

---

## 📝 Summary
NetBox serves as the single source of truth for network infrastructure, providing IPAM, DCIM, and API-driven automation.
