# 🖧 Lab 46: Terraform Network (Infrastructure as Code)

> Use Terraform to define and deploy network configurations as code.

## 👤 Author
- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Lab Info
| Item | Detail |
|------|--------|
| **Phase** | 8 - Network Automation |
| **Level** | ⭐⭐⭐⭐⭐ Expert |
| **Status** | 🔲 Todo |
| **Est. Time** | 5-6 hours |

---

## 🎯 Lab Objectives
- ✅ Install Terraform on Ubuntu
- ✅ Use Cisco IOS provider for Terraform
- ✅ Define network resources in HCL
- ✅ Execute plan/apply/destroy lifecycle
- ✅ Manage Terraform state

---

## ✅ Prerequisites
| Topic | Reference |
|-------|-----------|
| Ansible Network | Lab 45 |
| Python Netmiko | Lab 43 |

---

## 🗺️ Lab Topology
```
[Terraform Controller]──[Mgmt Network]──[R1, R2, SW1]
     (defines all network state via .tf files)
```

---

## 🛠️ Configuration

### Install Terraform
```bash
wget https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip
unzip terraform_1.7.0_linux_amd64.zip
mv terraform /usr/local/bin/
terraform version
```

### main.tf - Provider Configuration
```hcl
terraform {
  required_providers {
    iosxe = {
      source = "CiscoDevNet/iosxe"
    }
  }
}

provider "iosxe" {
  host     = "https://192.168.10.1"
  username = "admin"
  password = "cisco"
  insecure = true
}
```

### main.tf - Define VLAN Resource
```hcl
resource "iosxe_vlan" "vlan10" {
  vlan_id = 10
  name    = "DATA_VLAN"
}

resource "iosxe_vlan" "vlan20" {
  vlan_id = 20
  name    = "VOICE_VLAN"
}
```

### main.tf - Interface Configuration
```hcl
resource "iosxe_interface_loopback" "lo0" {
  name        = 0
  description = "Managed by Terraform"
  ipv4_address      = "1.1.1.1"
  ipv4_address_mask = "255.255.255.255"
}
```

---

## ✅ Verification
```bash
terraform init
terraform plan
terraform apply
terraform show
terraform destroy
```

---

## 📷 Screenshots
![Topology](imgs/topology.png)
![Terraform Apply](imgs/terraform_apply.png)

---

## 📝 Summary
Terraform brings Infrastructure as Code to network management, enabling versioned, repeatable network deployments.
