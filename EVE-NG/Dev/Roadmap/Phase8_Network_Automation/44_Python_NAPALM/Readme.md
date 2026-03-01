# 🖧 Lab 44: Python NAPALM (Multi-vendor Network Automation)

> Use NAPALM for vendor-agnostic network automation and configuration management.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 8 - Network Automation |
| **Level** | ⭐⭐⭐⭐ Advanced |
| **Status** | 🔲 Todo |
| **Est. Time** | 4-5 hours |

---

## 🎯 Lab Objectives
- ✅ Install NAPALM and required drivers
- ✅ Retrieve device facts with get_facts()
- ✅ Get interface status with get_interfaces()
- ✅ Load and compare configuration changes
- ✅ Commit or rollback configurations

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Python Netmiko | Lab 43 |

---

## 🗺️ Lab Topology
```
[Python Host]──[Mgmt Network]──[R1 IOS]
                              ──[R2 IOS]
                              ──[VyOS Router]
```

---

## 🛠️ Configuration

### Install NAPALM
```bash
pip install napalm
```

### Python - Get Device Facts
```python
from napalm import get_network_driver

driver = get_network_driver('ios')
device = driver('192.168.10.1', 'admin', 'cisco')

device.open()
facts = device.get_facts()
print(facts)

interfaces = device.get_interfaces()
print(interfaces)

device.close()
```

### Python - Load & Compare Config
```python
device.open()

# Load candidate config
device.load_merge_candidate(filename='new_config.txt')

# Compare changes
diff = device.compare_config()
print("Changes to be applied:")
print(diff)

# Commit or rollback
if input("Apply? (y/n): ") == 'y':
    device.commit_config()
else:
    device.discard_config()

device.close()
```

### Python - Get BGP/OSPF Neighbors
```python
bgp = device.get_bgp_neighbors()
arp = device.get_arp_table()
routes = device.get_route_to(destination='8.8.8.8')
```

---

## ✅ Verification
```python
# Check device uptime and model
print(f"Hostname: {facts['hostname']}")
print(f"Model: {facts['model']}")
print(f"Uptime: {facts['uptime']}")
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![NAPALM Output](imgs/napalm_output.png)

---

## 📝 Summary
NAPALM provides a unified API for multi-vendor network automation with support for configuration compare and rollback.
