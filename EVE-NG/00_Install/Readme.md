# 🚀 Project: EVE-NG

## 👤 Authors

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## ⚙️ Environment Variables

- **VMware Workstation Pro** [Download Here](https://drive.google.com/file/d/1bxlDTs__T3jXgVUE330gMokMCemzCkCg/view?usp=sharing)
- **EVE-NG ISO** [Download Here](https://www.eve-ng.net/)

---

## 📥 Installation Guide

### 💻 Virtualization Settings (Intel or AMD)

- Ensure **Virtualization Technology (VT-x/AMD-V)** is enabled in BIOS.
- ![Virtualization Check](imgs/checkVirtual.png)

### 🛠 Disable Hypervisor Launch Type

- Open **PowerShell** as Administrator and execute:

```bash
bcdedit /set hypervisorlaunchtype off
```
- ![Disable Hypervisor](imgs/powShell.png)

### 🔹 Disable Group Policy Restrictions

- Open **Edit Group Policy**
- Navigate to:
  - **Computer Configuration** → **Administrative Templates** → **System** → **Device Guard**
  - Disable **Deploy App Guard**
- ![Disable Policy](imgs/editGroup.png)

### 🔹 Turn Off Memory Integrity

- Go to **Settings** → **Privacy & Security** → **Windows Security** → **Device Security**
- **Turn Off Memory Integrity**
- ![Disable Memory Integrity](imgs/memIntegrity.png)

### 🔹 Disable Windows Features

- Open **Turn Windows Features On or Off**
- **Disable**:
  - **Hyper-V**
  - **Virtual Machine Platform**
  - **Windows Hypervisor Platform**
- ![Disable Windows Features](imgs/winFeature.png)

---

## 🔹 1. Login to EVE-NG

- **Username:** `root`
- **Password:** `eve`
- ![Login to EVE-NG](imgs/login.png)

## 🔹 2. Change the EVE-NG Root Password

- ![Change Password](imgs/chgpasswd.png)

## 🔹 3. Configure DNS Domain Name

- ![Configure DNS](imgs/conDns.png)

## 🔹 4. Configure IP Address

- **Using DHCP**
- ![Configure IP](imgs/confIP.png)

## 🔹 5. Configure NTP Server

- **Default is blank**
- ![Configure NTP](imgs/confNTP.png)

## 🔹 6. Configure Network Connection

- **Direct Connection**
- ![Configure Network](imgs/confNTP.png)

## 🔹 7. Complete Installation

- ![Finish Installation](imgs/finish.png)

## 🔹 8. Access EVE-NG Web GUI

- Open a **browser** and enter the **EVE-NG server IP address**.
  - **Username:** `admin`
  - **Password:** `eve`
- ![EVE-NG Web Access](imgs/urlEveNG.png)

## 🔹 9. Install Windows Client Side

- Download and install the **Windows Client Side** from [EVE-NG Downloads](https://www.eve-ng.net/index.php/download/).
- ![Windows Client Pack](imgs/winPack.png)

✅ **Installation and setup completed successfully!** 🚀
