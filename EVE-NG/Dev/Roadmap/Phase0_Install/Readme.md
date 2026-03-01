# 🏗️ Phase 0 - Installation & Setup

> Complete EVE-NG setup guide covering installation, web interface, image management, and basic device configuration.

## 👤 Author

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## 📋 Labs in This Phase

| Lab | Topic | Status | Est. Time |
|-----|-------|--------|-----------|
| [00 - EVE-NG Installation](00_EVE_NG_Install/Readme.md) | Install EVE-NG on VMware | ✅ Done | 45–60 min |
| [01 - Web GUI & First Lab](01_Web_GUI_First_Lab/Readme.md) | Navigate web interface, create first lab | ✅ Done | 30 min |
| [02 - Add Router & Switch](02_Add_Router_Switch/Readme.md) | Upload Cisco images via SFTP | ✅ Done | 30–45 min |
| [03 - Switch Basic Config](03_Switch_Basic_Config/Readme.md) | Hostname, passwords, SSH setup | ✅ Done | 30–45 min |

---

## 🎯 Phase Objectives

By completing Phase 0, you will have:
- ✅ Installed and configured EVE-NG on VMware Workstation
- ✅ Navigated the EVE-NG web interface
- ✅ Created your first network lab with virtual devices
- ✅ Uploaded Cisco router and switch images
- ✅ Performed basic switch configuration with SSH access

---

## 🔧 Required Software

| Software | Purpose | Download |
|----------|---------|----------|
| **VMware Workstation** | Hypervisor for EVE-NG | [Link](https://drive.google.com/file/d/149EBZ2zX4P6vws69DXrD-YWwnsTeMJZw/view?usp=sharing) |
| **EVE-NG ISO** | Network lab OS | [eve-ng.net](https://www.eve-ng.net/index.php/download/) |
| **FileZilla** | SFTP image upload | [filezilla-project.org](https://filezilla-project.org) |
| **PuTTY** | SSH / Console client | [putty.org](https://www.putty.org/) |
| **Cisco Images** | Router & Switch images | [Google Drive](https://drive.google.com/drive/folders/14ENNfWrLGDTylXUmcRXCpSXpMo0-dni5?usp=sharing) |

---

## ⚡ Quick Start Checklist

- [ ] Enable VT-x/AMD-V in BIOS
- [ ] Disable Windows Hyper-V features (`bcdedit /set hypervisorlaunchtype off`)
- [ ] Turn off Memory Integrity (Core Isolation)
- [ ] Install VMware Workstation
- [ ] Download and mount EVE-NG ISO
- [ ] Create VM: 4GB+ RAM, 2+ cores, 50GB+ disk
- [ ] Install EVE-NG, set root password
- [ ] Access web UI at `http://<VM-IP>` with `admin/eve`
- [ ] Upload Cisco images via FileZilla to `/opt/unetlab/addons/qemu/`
- [ ] Run `unl_wrapper -a fixpermissions` via SSH
- [ ] Create first lab and verify devices appear

---

## ➡️ Next Phase

**Phase 1 - Foundation** → Basic Switching, VLANs, STP, Inter-VLAN Routing, Basic Routing

See [Roadmap](../Roadmap/Roadmap.md) for the complete curriculum.

---

✅ **Phase 0 setup complete — ready to start network labs!** 🚀
