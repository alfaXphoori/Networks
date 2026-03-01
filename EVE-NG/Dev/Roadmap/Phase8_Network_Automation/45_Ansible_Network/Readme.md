# 🖧 Lab 45: Ansible Network (Playbooks for Cisco & Linux)

> Automate network device configuration using Ansible playbooks and network modules.

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
- ✅ Install Ansible on Ubuntu controller
- ✅ Configure inventory file for network devices
- ✅ Use ios_command module to run show commands
- ✅ Use ios_config module to push configurations
- ✅ Build reusable roles and playbooks

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Python NAPALM | Lab 44 |
| SSH on Cisco | Lab 43 |

---

## 🗺️ Lab Topology
```
[Ansible Controller Ubuntu]──[Mgmt SW]──[R1, R2, SW1, SW2]
```

---

## 🛠️ Configuration

### Install Ansible
```bash
apt-get install -y ansible
pip install ansible-pylibssh paramiko
ansible-galaxy collection install cisco.ios
```

### Inventory File (hosts.ini)
```ini
[routers]
R1 ansible_host=192.168.10.1
R2 ansible_host=192.168.10.2

[switches]
SW1 ansible_host=192.168.10.11

[network:children]
routers
switches

[network:vars]
ansible_network_os=ios
ansible_connection=network_cli
ansible_user=admin
ansible_password=cisco
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

### Playbook - Collect Info
```yaml
# gather_info.yml
---
- name: Gather Network Device Info
  hosts: routers
  gather_facts: false
  tasks:
    - name: Get interface brief
      cisco.ios.ios_command:
        commands:
          - show ip interface brief
          - show version
      register: output

    - name: Display output
      debug:
        var: output.stdout_lines
```

### Playbook - Push Config
```yaml
# configure_interfaces.yml
---
- name: Configure Loopback Interfaces
  hosts: routers
  gather_facts: false
  tasks:
    - name: Configure Loopback0
      cisco.ios.ios_config:
        lines:
          - ip address 1.1.1.1 255.255.255.255
        parents: interface Loopback0
```

---

## ✅ Verification
```bash
ansible all -i hosts.ini -m ping
ansible-playbook -i hosts.ini gather_info.yml
ansible-playbook -i hosts.ini configure_interfaces.yml --check
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Ansible Run](imgs/ansible_run.png)

---

## 📝 Summary
Ansible enables idempotent, agentless network automation through simple YAML playbooks.
