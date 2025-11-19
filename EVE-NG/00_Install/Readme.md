# 🚀 Project: EVE-NG

## 👤 Authors

- [@alfaXphoori](https://www.github.com/alfaXphoori)

---

## ⚙️ Environment Variables

- **Virtual Box** [Download Here](https://www.virtualbox.org/)
- **EVE-NG ISO** [Download Here](https://www.eve-ng.net/index.php/download/)

---

## 📥 SET Enviroment Windows

### 🔹 0. Turn ON Virtualization in BIOS
- Restart your computer and enter BIOS settings (usually by pressing F2, F10, Del, or Esc during boot)
- Look for **Virtualization Technology** (Intel VT-x) or **SVM Mode** (AMD-V)
- **Enable** the virtualization option
- Save changes and exit BIOS

### 🔹 1. Disable Hypervisor Launch Type

- Open **PowerShell** as Administrator and execute:

```bash
bcdedit /set hypervisorlaunchtype off
```
- ![Disable Hypervisor](imgs/powShell.png)

### 🔹 2. Disable Group Policy Restrictions

- Open **Edit Group Policy**
- Navigate to:
  - **Computer Configuration** → **Administrative Templates** → **System** → **Device Guard**
  - Disable **Deploy App Control for Business**
  - Disable **Turn On Virtualization Base Security**
- ![Disable Policy](imgs/editGroup.png)

### 🔹 3. Turn Off Memory Integrity

- Go to **Start** → **core isolation**
- **Turn Off Memory Integrity**
- ![Disable Memory Integrity](imgs/memIntegrity.png)

### 🔹 4. Disable Windows Features

- Open **Turn Windows Features On or Off**
- **Disable**:
  - **Hyper-V**
  - **Virtual Machine Platform**
  - **Windows Hypervisor Platform**
- ![Disable Windows Features](imgs/winFeature.png)

### 🔹 Install EVE-NG in Virtual Box

## 🔹 0. Create New Virtual Machine

- Open **VirtualBox** and click **New** to create a new Virtual Machine
- ![New Virtual Machine](imgs/_0New%20Virtual.png)

## 🔹 1. Set Virtual Machine Name

- Set the Virtual Machine name such as **EVE-NG**
- Select Type: **Linux**
- Select Version: **Ubuntu (64-bit)**
- ![Create Name](imgs/_01Create%20Name.png)

## 🔹 2. Go to Settings

- Right-click on the created Virtual Machine and select **Settings**
- ![Go Setting](imgs/_02Go%20Setting.png)

## 🔹 3. Add CPU and RAM

- Go to **System** tab
- Set **RAM**: Recommended at least **4096 MB (4 GB)**
- Set **CPU**: Recommended at least **2-4 Cores**
- ![Add CPU RAM](imgs/_03Add%20CPU%20RAM.png)

## 🔹 4. Enable VT-x/AMD-V

- Go to **System** tab → **Acceleration**
- Enable **VT-x/AMD-V**
- ![Open VT](imgs/_04Open%20VT.png)

## 🔹 5. Add Hard Disk

- Go to **Storage** tab
- Add **Hard Disk** with recommended size at least **30-50 GB**
- Add the downloaded **EVE-NG ISO file**
- ![Add HDD](imgs/_05Add%20HDD.png)

## 🔹 6. Configure Network

- Go to **Network** tab
- Select **Attached to**: **Bridged Adapter**
- ![Add Network](imgs/_06Add%20Network.png)

## 🔹 7. Start Virtual Machine

- Click **Start** to boot the Virtual Machine
- ![Start](imgs/_07Start.png)

## 🔹 8. Choose Language

- Select your preferred language such as **English**
- ![Choose Language](imgs/_08Choose%20laugaue.png)

## 🔹 9. Proceed with Installation

- Click **Install** to begin EVE-NG installation
- ![Go Next](imgs/_09Go%20next.png)

## 🔹 10. Complete Installation

- Wait for the installation to complete
- Click **Continue** to proceed
- ![Continue](imgs/_11Continue.png)

## 🔹 11. Unmount CD/ISO

- After installation completes, **Unmount CD/ISO**
- Go to **Devices** → **Optical Drives** → **Remove disk from virtual drive**
- ![Unmount CD](imgs/_11Unmount%20CD.png)

## 🔹 12. Confirm Unmount

- Confirm the unmount action
- ![Confirm Unmount](imgs/_12Confirm%20Unmount.png)

## 🔹 13. Force Power Off

- Perform **Force Power Off** to shut down the machine
- ![Force Power Off](imgs/_13Force%20PoweOff.png)

## 🔹 14. First Login

- Start the Virtual Machine again
- **Username:** `root`
- **Password:** `eve`
- ![First Login](imgs/_14First%20login.png)

## 🔹 15. Change Root Password

- The system will prompt you to change the **root password**
- Enter a new password
- ![Password](imgs/_15Passwd.png)

## 🔹 16. Confirm Password

- Confirm the password again
- ![Confirm Password](imgs/_16Confirm%20Passwd.png)

## 🔹 17. Set Hostname

- Set the **Hostname** for the EVE-NG machine
- ![Hostname](imgs/_17Hostname.png)

## 🔹 18. Configure DNS Domain Name

- Configure **DNS Domain Name** (or leave blank)
- ![DNS](imgs/_18DNS.png)

## 🔹 19. Configure IP Address

- Choose **DHCP** or configure Static IP
- Recommended to use **DHCP** for initial setup
- ![DHCP](imgs/_19DHCP.png)

## 🔹 20. Configure NTP Server

- Configure **NTP Server** (or leave blank to use default)
- ![NTP](imgs/_20NTP.png)

## 🔹 21. Configure Proxy

- Configure **Proxy** (if available) or leave blank
- Select **Direct Connection** for direct connection
- ![Proxy](imgs/_21Proxy.png)

## 🔹 22. Installation Complete

- Configuration is complete
- ![Done](imgs/_22Done.png)

## 🔹 23. Access Web Interface

- Open a web browser and enter the **IP Address** of EVE-NG
- **Username:** `admin`
- **Password:** `eve`
- ![Login Web](imgs/_23LoginWeb.png)

## 🔹 24. Install Windows Client Side

- Download and install **Windows Client Side** from [EVE-NG Downloads](https://www.eve-ng.net/index.php/download/)
- ![Windows Client Pack](imgs/winPack.png)

---

✅ **Installation and configuration completed successfully!** 🚀




