# 🖧 Lab 43: Python Netmiko (SSH Network Automation)

> Automate Cisco IOS device configuration and data collection using Python Netmiko.

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
- ✅ Install Python and Netmiko library
- ✅ Connect to Cisco IOS via SSH using Netmiko
- ✅ Collect device information (show commands)
- ✅ Push configuration changes to multiple devices
- ✅ Build a simple network audit script

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Python Basics | Python Fundamentals |
| Basic Router/Switch | Lab 02-03 |
| SSH Configuration on Cisco | Networking Knowledge |

---

## 🗺️ Lab Topology
```
[Automation Host Python]──[Mgmt SW]──[R1]
                                    ──[R2]
                                    ──[SW1]
```

---

## 🛠️ Configuration

### Enable SSH on Cisco Devices
```ios
hostname R1
ip domain-name lab.local
crypto key generate rsa modulus 2048
ip ssh version 2
username admin privilege 15 secret cisco
line vty 0 4
 transport input ssh
 login local
```

### Install Netmiko
```bash
pip install netmiko
```

### Python - Basic Connection
```python
from netmiko import ConnectHandler

device = {
    'device_type': 'cisco_ios',
    'host': '192.168.10.1',
    'username': 'admin',
    'password': 'cisco',
}

with ConnectHandler(**device) as conn:
    output = conn.send_command('show ip interface brief')
    print(output)
```

### Python - Push Config to Multiple Devices
```python
from netmiko import ConnectHandler

devices = [
    {'device_type': 'cisco_ios', 'host': '10.0.0.1', 'username': 'admin', 'password': 'cisco'},
    {'device_type': 'cisco_ios', 'host': '10.0.0.2', 'username': 'admin', 'password': 'cisco'},
]

config_commands = [
    'interface Loopback0',
    'ip address 1.1.1.1 255.255.255.255',
    'description Automated by Python'
]

for device in devices:
    with ConnectHandler(**device) as conn:
        conn.send_config_set(config_commands)
        print(f"Configured {device['host']}")
```

---

## ✅ Verification
```python
output = conn.send_command('show running-config | section interface')
print(output)
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Python Output](imgs/python_output.png)

---

## 📝 Summary
Netmiko simplifies SSH automation for Cisco and multi-vendor devices using a Pythonic interface.
